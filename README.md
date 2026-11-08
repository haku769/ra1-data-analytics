# Análise do Mercado Global de Vinhos (X-Wines) — RA1 e RA2

**Curso:** Sistemas de Informação · **Professora:** Marina de Lara · **Equipe:** João Guilherme Marques Camargo
**Documentação do RA1:** [RA1.Documentacao.docx](https://github.com/user-attachments/files/33410059/RA1.Documentacao.docx)

Todo o desenvolvimento está no notebook `analise_vinhos.ipynb`, organizado em duas seções:

| Seção | O que faz | Ferramenta |
|---|---|---|
| **RA1** | Ingestão, diagnóstico de qualidade, limpeza e exportação da base X-Wines | Pandas |
| **RA2** | Simulação da chegada de novas avaliações e agregação por janela temporal | Spark Structured Streaming |

## RA1 — Ingestão, qualidade e preparação

- **Fontes:** `XWines_Full_100K_wines.csv` (cadastro de vinhos) e `XWines_Full_21M_ratings.csv` (avaliações).
- **Ingestão:** *inner join* por `WineID`, resultando em **21.013.536 linhas × 22 colunas** (uma linha = uma avaliação + dados do vinho).
- **Qualidade:** `Website` com 2.440.869 nulos (~11,6%); ABV com valores impossíveis (0% e 50%); `Vintage` com tipos mistos e `Date` como texto.
- **Limpeza:** remoção de `Website`; filtro de ABV entre 4% e 25% (3.013 registros removidos); `Vintage` → `Int64` (safras não numéricas viram nulo) e `Date` → `datetime`.
- **Resultado:** amostra reprodutível de 100.000 linhas × 21 colunas em `x_wines_preparado_amostra.csv`.

## RA2 — Streaming com janelas temporais (Cenário A: dados estáticos)

- **Objetivo:** monitorar a **nota média** e o **volume** de novas avaliações por **tipo de vinho**, sinalizando janelas com média abaixo de 3,5.
- **Fonte simulada:** 63 avaliações artificiais com 9 vinhos reais do catálogo, publicadas em 4 lotes como arquivos JSON numa pasta monitorada (`readStream` com *file source*, `maxFilesPerTrigger=1`). O lote 3 traz registros **atrasados** e o lote 4 simula uma **queda nas notas dos tintos**.
- **Janela:** **tumbling de 10 minutos** sobre `event_time`, agrupada por `Type`, com `count`, `avg`, `min` e `max`; modo `complete`, *sink* em memória.
- **Resultado:** o total recebido cresce 15 → 30 → 48 → 63; os registros atrasados atualizam a janela 20:00–20:10 (15 → 18 avaliações); na janela 20:30–20:40 a média dos tintos cai para 2,22 e dispara o alerta.

## Como reproduzir

1. Instale Java 11 ou 17 e crie um ambiente com Python 3.11/3.12: `pip install -r requirements.txt`.
2. **Windows:** confira o caminho do JDK na célula de configuração da RA2. A pasta de trabalho do streaming é `C:\spark_ra2`, fora do OneDrive. Se o *checkpoint* falhar por permissão, coloque o `winutils.exe` (Hadoop 3.3) em `C:\hadoop\bin`; o notebook detecta e configura o `HADOOP_HOME`.
3. **RA1:** coloque os dois CSVs brutos na pasta do projeto e execute as células da seção RA1.
4. **RA2:** independente dos CSVs. Reinicie o kernel e execute a partir da seção RA2. As pastas do streaming são recriadas a cada execução.

## Referências

- DE AZAMBUJA, R. X.; MORAIS, A. J.; FILIPE, V. X-Wines: A Wine Dataset for Recommender Systems and Machine Learning. *Big Data and Cognitive Computing*, v. 7, n. 1, 2023.
- Apache Spark. *Structured Streaming Programming Guide* (versão 3.5).
- MCKINNEY, W. Data Structures for Statistical Computing in Python. *Proceedings of the 9th Python in Science Conference*, 2010.
