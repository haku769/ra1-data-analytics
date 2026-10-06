# 🍷 Projeto de Análise de Dados - RA1

**Autor:** João Guilherme Marques Camargo  
**Instituição:** Pontifícia Universidade Católica do Paraná (PUCPR) — Ciência da Computação / Análise de Dados  

---

## 📌 Contexto e Introdução do Projeto

Este repositório destina-se à entrega da primeira avaliação (RA1) de **Data Analytics**. O projeto tem como objetivo central estruturar o pipeline inicial de tratamento de dados utilizando o abrangente ecossistema do **X-Wines** (`XWines_Full_100K_wines.csv` e `XWines_Full_21M_ratings.csv`), investigando o mercado global de vinhos e o comportamento de consumo dos usuários através de avaliações reais.

Diferente das etapas posteriores de modelagem e visualização, o escopo deste RA1 foca estritamente na engenharia preliminar dos dados, assegurando confiabilidade, rastreabilidade e reprodutibilidade por meio de:

1. **Definição Analítica:** Formulação do problema de negócio e das perguntas norteadoras da investigação.
2. **Ingestão Reprodutível:** Carregamento estruturado e junção relacional das bases de dados em ambiente Python local utilizando Pandas.
3. **Avaliação de Qualidade:** Diagnóstico profundo de anomalias estruturais, como valores nulos, registros duplicados, tipos de dados incorretos e *outliers* críticos (ex: faixas de teor alcoólico inverossímeis).
4. **Limpeza e Preparação:** Aplicação de regras de negócio consistentes para higienização e padronização do dataset.
5. **Geração de Amostra Tratada:** Disponibilização de um conjunto de dados limpo e documentado (`x_wines_preparado_amostra.csv`), servindo como alicerce para as próximas etapas de transformação do projeto (RA2).

---
