<<<<<<< HEAD
Projeto: Análise Exploratória do Comportamento de Consumo e Mercado Global de Vinhos (X-Wines) 

Curso: Sistemas de Informação

Professor ou professora: Marina de Lara 

Equipe: João Guilherme Marques Camargo 

Repositório:(https://github.com/haku769/ra1-data-analytics.git)

(https://github.com/user-attachments/files/33410059/RA1.Documentacao.docx)

Entrega: Curitiba • 13/10/2026 

Seção 01: Problema e perguntas analíticas 

Contexto e problema: O mercado global de vinhos movimenta bilhões de dólares anualmente, abrangendo milhares de vinícolas, regiões produtoras e perfis diversificados de consumidores. No entanto, a tomada de decisão no setor — seja por apreciadores, distribuidores ou produtores — frequentemente esbarra na dispersão de dados sobre avaliações de usuários, características físico-químicas (como teor alcoólico) e tipificações de uvas. O problema investigado neste projeto é a assimetria de informações na caracterização de vinhos e na avaliação subjetiva dos consumidores, dificultando a identificação clara dos fatores que correlacionam a qualidade percebida (notas) com atributos intrínsecos do produto. 

Objetivo e recorte: Estabelecer o pipeline de ingestão, avaliação de qualidade e higienização de um grande volume de dados de vinhos e avaliações, entregando um dataset limpo e estruturado. O escopo abrange o dataset global X-Wines, focando na análise relacional entre as características das vinícolas/vinhos (XWines_Full_100K_wines.csv) e as avaliações globais de usuários (XWines_Full_21M_ratings.csv). 

Perguntas analíticas: P1 | Como a distribuição das notas (Rating) varia de acordo com o país de origem (Country) e o tipo de vinho (Type)? Rating, Country, Type (Tabelas de avaliações e vinhos) 
P2 | Qual é a correlação entre o teor alcoólico (ABV) e a nota média atribuída pelos usuários aos vinhos tintos e brancos? | ABV, Rating, Type | 
P3 | Quais são as safras (Vintage) mais recorrentes nas avaliações de maior destaque (notas 4.5 e 5.0) nos últimos 15 anos? | Vintage, Rating | 

Viabilidade inicial: A base de dados X-Wines possui o volume e a granularidade necessários para responder às perguntas propostas, contendo chaves relacionais (WineID) consistentes. Identificou-se como lacuna preliminar a presença de registros com valores nulos em colunas descritivas secundárias (ex: Website), que foram devidamente tratadas sem prejuízo à integridade das análises. 

Seção 02: Fontes de dados e ingestão 

Ficha da fonte: 

Identificação: F1 • X-Wines Dataset (XWines_Full_100K_wines.csv e XWines_Full_21M_ratings.csv) 

Origem e acesso: Repositório acadêmico / plataforma Kaggle (X-Wines Database). Acesso em: Outubro de 2026. 

Formato e volume: Arquivos estruturados em formato CSV. O dataset de vinhos possui cerca de 100 mil registros e 22 atributos; o dataset de avaliações possui aproximadamente 21 milhões de registros e 6 atributos. 

Cobertura: Escopo global, englobando múltiplas regiões produtoras de vinhos internacionais e avaliações de usuários. 

Condições de uso: Disponibilizado para fins acadêmicos e de pesquisa em Ciência de Dados. 

Relação com o problema: Base primária e essencial para estruturar o relacionamento entre as propriedades físico-químicas/geográficas dos vinhos (F1) e o comportamento de feedback dos consumidores (P1, P2 e P3). 

Procedimento de ingestão: | Etapa | Procedimento ou parâmetro | Evidência no repositório | | :--- | :--- | :--- | | 1 | Leitura dos arquivos brutos em formato CSV utilizando o motor otimizado do Pandas. | analise_vinhos.ipynb (Célula 1) | | 2 | Execução de junção relacional (Inner Join) entre as tabelas de avaliações e vinhos através da chave estrangeira comum (WineID). | analise_vinhos.ipynb (Célula 1) | | 3 | Verificação de integridade estrutural e contagem volumétrica de linhas e colunas resultantes. | analise_vinhos.ipynb (Célula 1) | 

Resultado da ingestão: O processo unificou com sucesso as bases, resultando em um volume bruto inicial de 6.569.617 registros consolidados e 22 colunas. Os arquivos brutos encontram-se isolados no ambiente de desenvolvimento local, garantindo reprodutibilidade controlada. 

Seção 03: Compreensão e qualidade 

Unidade de observação: Cada linha representa uma avaliação individual (rating) realizada por um usuário em um determinado vinho, acoplada às características cadastrais daquele vinho. 

Identificação dos registros: Chave primária composta por RatingID nas avaliações, vinculada à chave estrangeira WineID do cadastro de vinhos. 

Dicionário inicial dos dados: | Atributo | Significado | Tipo e formato | Unidade ou domínio  | RatingID | Identificador único da avaliação | Inteiro (int64) | Numérico sequencial | | UserID | Identificador único do usuário | Inteiro (int64) | Numérico sequencial | | WineID | Identificador único do vinho | Ponto flutuante inicial (float64) | Numérico | | Rating | Nota atribuída pelo usuário | Ponto flutuante (float64) | Intervalo de 1.0 a 5.0 | | ABV | Teor alcoólico do vinho | Ponto flutuante (float64) | Percentual (%) | | Vintage | Safra de produção do vinho | Ponto flutuante inicial (float64) | Anual (Ex: 2010) | | Date | Data e hora da avaliação | Objeto / Texto (object) | Formato de data misto | | Website | Sítio eletrônico da vinícola | Objeto / Texto (object) | URLs ou valores nulos | 

Diagnóstico de qualidade: | ID | Verificação e regra | Resultado observado | Evidência | | :--- | :--- | :--- | :--- | | Q1 | Verificação de valores ausentes (nulos) em todo o conjunto de dados | A coluna Website apresentou 630.011 registros nulos. Demais colunas críticas sem nulos estruturais. | analise_vinhos.ipynb (Célula 2) | | Q2 | Verificação de registros duplicados idênticos | Foram encontradas 0 linhas estritamente duplicadas. | analise_vinhos.ipynb (Célula 2) | | Q3 | Identificação de outliers e valores inválidos no teor alcoólico (ABV) | Presença de valores extremos/impossíveis, com mínimo de 0.0% e máximo de 50.0% de álcool. | analise_vinhos.ipynb (Célula 2) | | Q4 | Verificação de tipos de dados inadequados | As colunas WineID, Vintage constavam como float64 e Date como texto (object). | analise_vinhos.ipynb (Célula 2) | 

Impacto dos problemas identificados: A presença de valores extremos de ABV (0% ou 50%) distorceria análises estatísticas e correlações de modelos futuros (RA2). Tipos de dados incorretos (WineID em decimal) impedem operações relacionais limpas. A alta taxa de nulos na coluna Website polui o dataset, visto que o atributo é irrelevante para as perguntas analíticas formuladas. 

Seção 04: Limpeza e preparação 

Registro das decisões: 

Decisão e diagnóstico [D1 • relacionado a Q1]: 

Problema e abrangência: A coluna Website continha mais de 630 mil valores nulos. 

Tratamento e justificativa: Exclusão integral da coluna Website do dataframe por irrelevância analítica e otimização de memória. 

Implementação: df_clean = df_clean.drop(columns=['Website']) (analise_vinhos.ipynb - Célula 3). 

Validação: Confirmação via .info() da supressão da coluna. 

Decisão e diagnóstico [D2 • relacionado a Q3]: 

Problema e abrangência: Existência de registros com valores de teor alcoólico (ABV) fisicamente impossíveis ou incorretos (<= 4.0% ou > 25.0%). 

Tratamento e justificativa: Filtragem restritiva mantendo exclusivamente vinhos com ABV realista (entre 4% e 25%). 

Implementação: df_clean = df_clean[(df_clean['ABV'] > 4.0) & (df_clean['ABV'] <= 25.0)] (analise_vinhos.ipynb - Célula 3). 

Validação: Contagem volumétrica comparativa atestando a remoção dos registros anômalos. 

Decisão e diagnóstico [D3 • relacionado a Q4]: 

Problema e abrangência: Tipos de dados inadequados (WineID e Vintage como ponto flutuante; Date como string). 

Tratamento e justificativa: Conversão explícita de WineID e Vintage para inteiros (int) e padronização da coluna Date para o tipo temporal (datetime). 

Implementação: Conversão via .astype(int) e pd.to_datetime(..., format='mixed') (analise_vinhos.ipynb - Célula 3). 

Validação: Validação de schema no .info() pós-conversão. 

Resumo das intervenções: | Decisão | Ação executada | Impacto nos dados | Evidência | | :--- | :--- | :--- | :--- | | D1 | Remoção de coluna irrelevante com nulos | Eliminação da coluna Website (630k nulos eliminados) | analise_vinhos.ipynb (Célula 3) | | D2 | Filtragem de outliers de teor alcoólico | Exclusão de registros com ABV fora do intervalo biológico/comercial (4% a 25%) | analise_vinhos.ipynb (Célula 3) | | D3 | Correção e tipagem de dados | Padronização de chaves e safras para inteiros e datas para datetime | analise_vinhos.ipynb (Célula 3) | 

Seção 05: Conjunto de dados resultante 

Arquivo ou conjunto final: x_wines_preparado_amostra.csv (Armazenado na raiz do repositório no formato CSV). 

Unidade de observação final: Uma linha representa uma avaliação de usuário higienizada e validada, associada aos metadados consistentes de um vinho. 

Dicionário atualizado: Referenciado na Seção 03 com tipos de dados corrigidos. 

Comparação antes e depois: | Medida | Após ingestão | Após preparação | Explicação da diferença | | :--- | :--- | :--- | :--- | | Registros | 6.569.617 | Amostra de 100.000 linhas | Amostragem controlada para versionamento eficiente em Git (evitando o limite de 100MB), mantendo a integridade estatística. | | Atributos | 22 colunas | 21 colunas | Redução de 1 coluna (Website) por irrelevância e excesso de nulos. | | Anomalias de ABV | Presentes (0% a 50%) | Eliminadas (Intervalo 4% a 25%) | Tratamento rigoroso de outliers de teor alcoólico. | 

Verificações finais e limitações: Testes de integridade pós-preparação confirmaram ausência de anomalias nas variáveis essenciais. As restrições volumétricas do GitHub foram supridas de forma metodológica através da amostra representativa de 100 mil linhas gerada de forma reprodutível no script. 

Seção 06: Organização e reprodutibilidade 

Repositório e versão:https://github.com/haku769/ra1-data-analytics.git • Branch main. 

Ambiente: Python 3.10+, Pandas, VS Code / Jupyter Notebook. 

Localização dos artefatos: | Artefato | Caminho ou forma de obtenção | | :--- | :--- | | Dados brutos | Obtidos via repositório X-Wines (Kaggle) — Arquivos locais de grande porte. | | Código de ingestão e preparação | analise_vinhos.ipynb (organizado em 4 células sequenciais). | | Dados preparados | x_wines_preparado_amostra.csv (raiz do repositório). | | Documentação e dependências | Documentação oficial técnica do RA1. | 

Passos para reprodução: 1 | Clonar o repositório e instalar dependências (pip install pandas ipykernel). | Ambiente pronto para execução. |  

 2 | Posicionar os arquivos brutos na mesma pasta do projeto. | Arquivos disponíveis para leitura local.  

 3 | Abrir o arquivo analise_vinhos.ipynb no VS Code e executar as células sequencialmente. | Ingestão, diagnóstico, limpeza e geração do CSV amostral executados. | |  

4 | Validar a presença do arquivo resultante x_wines_preparado_amostra.csv na pasta.  

Seção 07: Referências 

GOMES, M. et al. X-Wines: A Large-Scale Wine Dataset for Machine Learning and Data Analytics. Repositório de Dados Acadêmicos, 2024. Disponível em: Plataforma Kaggle. Acesso em: Outubro de 2026. 

MCKINNEY, W. Data Structures for Statistical Computing in Python. Proceedings of the 9th Python in Science Conference, 2010. 


