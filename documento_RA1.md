# RA1: Ingestão e Preparação dos Dados - Análise do Mercado de Vinhos

Este repositório contém a entrega da primeira etapa (RA1) do Projeto de Data Analytics. O objetivo desta fase é obter, compreender, avaliar e limpar os dados que servirão de base para análises futuras.

## 1. Definição do problema e perguntas analíticas

**Contexto e Problema:**
O mercado vitivinícola global é altamente complexo e diversificado, apresentando milhares de rótulos com características físico-químicas e geográficas distintas. Essa vasta gama de opções dificulta a tomada de decisão estratégica por parte de importadoras, varejistas e plataformas de e-commerce na elaboração de seus catálogos.

O problema central que este projeto investiga é: *Como as características intrínsecas de um vinho (tipo, composição de uvas, teor alcoólico) e sua origem geográfica influenciam a percepção de qualidade (nota de avaliação) dada pelos consumidores?*

**Perguntas Analíticas:**

1. Quais combinações de características técnicas (ex: variedade de uva, teor alcoólico, corpo e acidez) tendem a receber as avaliações mais altas dos usuários?

2. Existe uma variação estatisticamente relevante nas avaliações médias de vinhos quando categorizados de acordo com o seu país e região de origem?

3. Como os vinhos do tipo *blends* (mistura de uvas) performam na preferência do consumidor em comparação aos vinhos varietais (elaborados com predominância de uma única uva)?

## 2. Fontes de dados

Os dados selecionados para este projeto têm origem no conjunto **X-Wines**, uma base robusta estruturada para análises de mercado e sistemas de recomendação.

* **Fonte/Origem:** Dados coletados de avaliações reais de usuários na web (disponibilizado via Kaggle/Repositórios Acadêmicos).

* **Formato:** Arquivos tabulares (`.csv`).

* **Quantidade aproximada de registros:** A base original possui \~100 mil registros de vinhos únicos e \~21 milhões de avaliações de usuários.

* **Principais atributos/variáveis:**

  * *Tabela Wines:* `WineID` (Identificador), `WineName` (Nome), `Type` (Tipo do vinho), `Grapes` (Uvas), `ABV` (Teor Alcoólico), `Body` (Corpo), `Acidity` (Acidez) e `Country` (País).

  * *Tabela Ratings:* `RatingID`, `UserID`, `WineID` (Chave de ligação), `Vintage` (Safra) e `Rating` (Nota de 1 a 5).

* **Relação com o problema:** A base fornece perfeitamente as variáveis independentes (características técnicas do produto) e a métrica de sucesso/aceitação (avaliações dos usuários), permitindo o cruzamento de dados necessário para responder às perguntas analíticas.

> ⚠️ **Nota de Reprodutibilidade:** Os arquivos originais brutos (`XWines_Full_100K_wines.csv` e `XWines_Full_21M_ratings.csv`) excedem o limite de tamanho de 100MB do GitHub. Para reproduzir a ingestão do zero, é necessário possuir a base original do X-Wines no mesmo diretório do notebook.

## 3. Ingestão dos dados

A ingestão dos dados é realizada de forma programática, local e 100% reproduzível utilizando a linguagem Python no ambiente do Jupyter Notebook (VS Code), através da biblioteca **Pandas**.

O processo consiste em:

1. Leitura dos arquivos CSV brutos para a memória usando `pd.read_csv()`.

2. Integração (Merge) das duas tabelas através de um *Inner Join* utilizando a chave comum `WineID`, consolidando as características do vinho e as notas do usuário em um único *DataFrame* para avaliação.

## 4. Compreensão e avaliação da qualidade dos dados

Após a ingestão, foi realizada uma exploração técnica dos dados (via funções `.info()`, `.isnull().sum()` e `.describe()`), identificando os seguintes problemas de qualidade:

* **Valores Ausentes:** Identificamos que a coluna `Website` possuía uma quantidade massiva de valores nulos (mais de 600 mil). Além disso, mapeamos o risco de nulos em colunas críticas para a análise (`Rating`, `Grapes`, `Country`).

* **Tipos de Dados Incorretos:** As colunas numéricas de identificação (`WineID`) e ano da safra (`Vintage`) foram importadas originalmente como valores decimais (`float64`). A coluna `Date` foi importada como texto (`object`).

* **Valores Inválidos/Outliers:** A análise descritiva da coluna `ABV` (Teor Alcoólico) revelou valores irreais, com o mínimo em 0.0% (suco) e o máximo em 50.0% (característica de destilados, não de vinhos convencionais).

* **Registros Duplicados:** A base de 21M de registros apresentou integridade quanto a duplicidades exatas de linhas na estrutura atual.

## 5. Limpeza e preparação dos dados

Com base nos problemas identificados, as seguintes decisões de tratamento foram aplicadas via código (Pandas):

1. **Remoção de coluna irrelevante:** A coluna `Website` foi removida (`drop`), pois a alta taxa de valores nulos e sua natureza não agregam valor analítico para prever a nota de qualidade do vinho.

2. **Tratamento de Outliers (ABV):** Aplicamos um filtro para manter na base apenas registros com teor alcoólico (`ABV`) maior que 4.0% e menor ou igual a 25.0%. *Justificativa:* Isso garante que as futuras análises estatísticas não sejam enviesadas por erros de digitação dos usuários na origem dos dados.

3. **Correção de tipos de dados:** Convertidas as colunas `WineID` e `Vintage` para inteiros (`int`), e a coluna `Date` para o tipo `datetime`. *Justificativa:* Tipificação correta é essencial para agregações temporais e junções em etapas futuras.

4. **Tratamento de nulos vitais:** Remoção de eventuais linhas que não possuam preenchimento nas colunas `Rating`, `Grapes` e `Country` (`dropna`). *Justificativa:* Linhas sem essas informações impedem diretamente a resposta das perguntas propostas no item 1.

## 6. Conjunto de dados resultante do RA1

Após o pipeline de limpeza, o conjunto de dados resultante encontra-se perfeitamente estruturado, sem inconsistências de tipagem ou outliers severos nas variáveis de interesse.

Devido ao tamanho original do dataset (mais de 1GB), o arquivo resultante gerado no final do processo de preparação é uma amostra aleatória robusta contendo 100.000 registros, salva como **`x_wines_preparado_amostra.csv`**.