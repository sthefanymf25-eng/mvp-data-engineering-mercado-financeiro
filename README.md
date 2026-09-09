# MVP de Engenharia de Dados — Mercado Financeiro, Criptomoedas e Inflação

Este projeto foi desenvolvido como MVP da disciplina de Engenharia de Dados, com o objetivo de construir um pipeline na nuvem para integrar dados de mercado financeiro, criptomoedas e indicadores macroeconômicos.

O período analisado compreende janeiro de 2021 a dezembro de 2025.

O projeto foi desenvolvido no Databricks Free Edition, utilizando Python, Pandas, PySpark, Spark SQL, Delta Lake e GitHub.

---

## Objetivo

O objetivo principal é construir um pipeline de dados capaz de coletar, tratar, integrar e analisar informações de:

- Bitcoin;
- Ethereum;
- Solana;
- XRP;
- Ibovespa;
- dólar USD/BRL;
- Meta Selic;
- IPCA.

A partir desses dados, o projeto busca comparar desempenho e risco dos ativos, avaliar retorno real após a inflação e explorar associações com indicadores macroeconômicos.

---

## Arquitetura

O projeto segue uma arquitetura Medallion:

**Fontes → Coleta → Bronze → Silver → Gold → Análises**

### Bronze

Responsável pela persistência dos dados coletados das fontes externas, mantendo informações de origem e data de ingestão.

### Silver

Responsável pelo tratamento, padronização e validação da qualidade dos dados.

### Gold

Responsável pela integração das fontes e criação dos indicadores analíticos utilizados nas perguntas de negócio.

---

## Fontes de dados

| Dados | Fonte | Frequência |
|---|---|---|
| Bitcoin, Ethereum, Solana e XRP | Binance API | Diária |
| Ibovespa | B3 — BVBG.087.01 IndexReport | Pregão |
| USD/BRL | Banco Central do Brasil — PTAX | Dias com cotação |
| Meta Selic | Banco Central do Brasil — SGS 432 | Diária |
| IPCA | Banco Central do Brasil — SGS 433 | Mensal |

---

## Tecnologias utilizadas

- Databricks Free Edition
- Python
- Pandas
- PySpark
- Spark SQL
- Delta Lake
- Git
- GitHub
- Markdown

---

## Estrutura do repositório

```text
mvp-data-engineering-mercado-financeiro/
├── README.md
├── docs/
│   ├── 01_objetivo.md
│   ├── 02_coleta.md
│   ├── 03_data_catalog.md
│   ├── 04_metodologia_arquitetura.md
│   └── 05_autoavaliacao.md
└── notebooks/
    ├── 00_configuracao.ipynb
    ├── 01_coleta_criptomoedas.ipynb
    ├── 02_coleta_indicadores_macro.ipynb
    ├── 03_coleta_ibovespa.ipynb
    ├── 04_camada_bronze.ipynb
    ├── 05_camada_silver.ipynb
    ├── 06_camada_gold.ipynb
    └── 07_analises_negocio.ipynb