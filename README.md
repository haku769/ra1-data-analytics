# Análise do Mercado Global de Vinhos (X-Wines) — RA1, RA2 e RA3

**Curso:** Sistemas de Informação · **Professora:** Marina de Lara · **Equipe:** João Guilherme Marques Camargo
**Documentação do RA1:** [RA1.Documentacao.docx](https://github.com/user-attachments/files/33410059/RA1.Documentacao.docx)

Todo o desenvolvimento está no notebook `analise_vinhos.ipynb`, organizado em três seções independentes:

| Seção | O que faz | Ferramentas |
|---|---|---|
| **RA1** | Ingestão, diagnóstico de qualidade, limpeza e exportação da base X-Wines | Pandas |
| **RA2** | Simulação da chegada de novas avaliações e agregação por janela temporal | Spark Structured Streaming |
| **RA3** | Estatística descritiva, visualizações, representatividade e Machine Learning | Spark, Pandas, Matplotlib, scikit-learn |

## RA1 — Ingestão, qualidade e preparação

- **Fontes:** `XWines_Full_100K_wines.csv` (cadastro de vinhos) e `XWines_Full_21M_ratings.csv` (avaliações).
- **Ingestão:** *inner join* por `WineID`, resultando em **21.013.536 linhas × 22 colunas** (uma linha = uma avaliação + dados do vinho).
- **Qualidade:** `Website` com 2.440.869 nulos (~11,6%); ABV com valores impossíveis (0% e 50%); `Vintage` com tipos mistos e `Date` como texto.
- **Limpeza:** remoção de `Website`; filtro de ABV entre 4% e 25% (3.013 registros removidos); `Vintage` → `Int64` e `Date` → `datetime`.
- **Resultado:** amostra reprodutível de 100.000 linhas em `x_wines_preparado_amostra.csv`.

## RA2 — Streaming com janelas temporais (Cenário A: dados estáticos)

- **Objetivo:** monitorar a nota média e o volume de novas avaliações por tipo de vinho, sinalizando janelas com média abaixo de 3,5.
- **Fonte simulada:** 63 avaliações artificiais com 9 vinhos reais, publicadas em 4 lotes JSON numa pasta monitorada (`readStream`, `maxFilesPerTrigger=1`), com registros atrasados no lote 3 e queda de notas dos tintos no lote 4.
- **Janela:** tumbling de 10 minutos sobre `event_time`, agrupada por `Type`, com `count`, `avg`, `min` e `max` (modo `complete`).
- **Resultado:** total recebido 15 → 30 → 48 → 63; os atrasados atualizam a janela 20:00–20:10 (15 → 18); a média dos tintos cai para 2,22 na janela 20:30–20:40 e dispara o alerta.

## RA3 — Análise estatística, visualização e Machine Learning

Base completa: **21.013.536 avaliações, 100.646 vinhos, 1.056.079 usuários, 62 países, 2012–2021**, agregada com Spark (tabelas em `dados_ra3/`).

| Pergunta | Técnica | Principal resultado |
|---|---|---|
| P1. Quais são os vinhos mais populares? | `groupBy("WineID")` + `count`; curva de concentração e Gini | Popularidade muito concentrada (Gini 0,73): 10% dos vinhos recebem 60,6% das avaliações. O mais avaliado é o Rioja Reserva da Marqués de Riscal (27.415). |
| P2. Qual é a qualidade percebida de cada vinho? | `avg("Rating")` por vinho, média ponderada bayesiana, comparação por tipo | Mediana das notas = 4,0. No topo ponderado estão ícones (Yquem, Vega Sicilia Unico, Pétrus); na base, tintos de mesa. Sobremesa e Porto são os mais bem avaliados; rosés, os menos. |
| P3. Volume e nota média estão correlacionados? | Pearson (log do volume), Spearman, notas por faixa de popularidade | Correlação positiva e fraca (Spearman 0,16). A nota sobe de 3,66 para 4,14 entre as faixas, e as médias com poucas avaliações são instáveis. |
| P4. Como as avaliações se comportam no tempo? | Séries mensal e anual a partir de `Date`; sazonalidade | Volume ×68 entre 2012 e 2020, estável em 2021. Dezembro concentra 12,9% do ano. A nota média sobe de 3,72 (2013) para 3,99 (2021). |
| ML. Prever se um vinho será bem avaliado (média ≥ 4,0) | Random Forest com 10 features, treino/teste 80/20 estratificado | ROC-AUC 0,81 e F1 0,62 (referência: F1 0). As variáveis mais importantes são ABV, país, tipo e corpo. |

- **Estatística descritiva:** média, mediana, moda, desvio-padrão, amplitude, IQR, coeficiente de variação, assimetria e tabela de frequência.
- **Visualizações (6):** distribuição das notas; ranking e concentração; nota por vinho e por tipo; nota por faixa de popularidade; série temporal e sazonalidade; matriz de confusão e importância das variáveis. Cada uma é seguida de interpretação.
- **Representatividade:** sem amostragem no RA3. São discutidos os vieses geográfico, de tipo, de atividade mínima, de autosseleção e de período, além da ausência de preço.

## Como reproduzir

1. Instale o Java 11 ou 17 e as dependências: `pip install -r requirements.txt`.
   - **Windows:** coloque `winutils.exe` e `hadoop.dll` (Hadoop 3.3) em `C:\hadoop\bin`. O notebook encontra o JDK e configura o `HADOOP_HOME` automaticamente.
2. **RA1:** coloque os dois CSVs brutos na pasta do projeto e execute a seção RA1.
3. **RA2:** independente dos CSVs. Reinicie o kernel e execute a partir da seção RA2.
4. **RA3:** reinicie o kernel e execute a partir da seção RA3.
   - Com os CSVs brutos na pasta, a etapa 3.1 agrega os 21 milhões de avaliações com Spark e grava as tabelas em `dados_ra3/`.
   - Sem os CSVs brutos, o notebook usa as tabelas já versionadas em `dados_ra3/`, e todas as análises, gráficos e o modelo podem ser reproduzidos.

## Referências

- DE AZAMBUJA, R. X.; MORAIS, A. J.; FILIPE, V. X-Wines: A Wine Dataset for Recommender Systems and Machine Learning. *Big Data and Cognitive Computing*, v. 7, n. 1, 2023.
- Apache Spark. *Structured Streaming Programming Guide* e *Spark SQL Guide* (versão 3.5).
- PEDREGOSA, F. et al. Scikit-learn: Machine Learning in Python. *Journal of Machine Learning Research*, v. 12, 2011.
- MCKINNEY, W. Data Structures for Statistical Computing in Python. *Proceedings of the 9th Python in Science Conference*, 2010.
