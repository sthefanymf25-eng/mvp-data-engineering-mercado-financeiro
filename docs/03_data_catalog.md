# Data Catalog

Este documento apresenta o catálogo de dados do MVP de Engenharia de Dados, descrevendo as principais tabelas das camadas Bronze, Silver e Gold.

O objetivo é registrar a finalidade, a granularidade, as principais chaves, as fontes e os campos utilizados no pipeline, facilitando a rastreabilidade e a compreensão do modelo de dados.

---

## 1. Camada Bronze

A camada Bronze armazena os dados coletados das fontes externas com o mínimo de transformação necessário para persistência e rastreabilidade.

### 1.1 `bronze.criptomoedas`

**Finalidade:** armazenar os dados diários de mercado das criptomoedas coletados pela Binance API.

**Granularidade:** um registro por ativo e por dia.

**Chave lógica:** `data` + `ativo`.

**Fonte:** Binance API.

| Campo | Tipo | Descrição |
|---|---|---|
| data | timestamp | Data da observação |
| ativo | string | Par negociado, como BTCUSDT |
| open | double | Preço de abertura |
| high | double | Preço máximo do dia |
| low | double | Preço mínimo do dia |
| close | double | Preço de fechamento |
| volume | double | Volume negociado do ativo |
| quote_asset_volume | double | Volume negociado na moeda de cotação |
| numero_negociacoes | long | Quantidade de negociações |
| fonte | string | Origem dos dados |
| data_ingestao | timestamp | Data e hora de ingestão |

**Observação:** os pares de criptomoedas estão cotados em USDT.

---

### 1.2 `bronze.dolar`

**Finalidade:** armazenar as cotações diárias do dólar comercial utilizadas no projeto.

**Granularidade:** um registro por dia com cotação disponível.

**Chave lógica:** `data`.

**Fonte:** Banco Central do Brasil — PTAX.

| Campo | Tipo | Descrição |
|---|---|---|
| data | timestamp | Data da cotação |
| cotacaoCompra | double | Cotação de compra do dólar |
| cotacaoVenda | double | Cotação de venda do dólar |
| indicador | string | Identificação do indicador |
| fonte | string | Origem dos dados |
| data_ingestao | timestamp | Data e hora de ingestão |

---

### 1.3 `bronze.selic`

**Finalidade:** armazenar a série histórica da Meta Selic definida pelo Copom.

**Granularidade:** um registro por dia.

**Chave lógica:** `data`.

**Fonte:** Banco Central do Brasil — SGS série 432.

| Campo | Tipo | Descrição |
|---|---|---|
| data | timestamp | Data da observação |
| selic_meta_pct_aa | double | Meta Selic em percentual ao ano |
| indicador | string | Identificação do indicador |
| fonte | string | Origem dos dados |
| data_ingestao | timestamp | Data e hora de ingestão |

---

### 1.4 `bronze.ipca`

**Finalidade:** armazenar a variação mensal do IPCA.

**Granularidade:** um registro por mês.

**Chave lógica:** `data`.

**Fonte:** Banco Central do Brasil — SGS série 433.

| Campo | Tipo | Descrição |
|---|---|---|
| data | timestamp | Mês de referência |
| ipca_pct_mes | double | Variação percentual do IPCA no mês |
| indicador | string | Identificação do indicador |
| fonte | string | Origem dos dados |
| data_ingestao | timestamp | Data e hora de ingestão |

---

### 1.5 `bronze.ibovespa`

**Finalidade:** armazenar os dados oficiais diários do índice Ibovespa.

**Granularidade:** um registro por pregão.

**Chave lógica:** `data`.

**Fonte:** B3 — BVBG.087.01 IndexReport.

| Campo | Tipo | Descrição |
|---|---|---|
| data | timestamp | Data do pregão |
| ativo | string | Identificação do índice |
| abertura | double | Valor de abertura |
| minima | double | Menor valor do pregão |
| maxima | double | Maior valor do pregão |
| fechamento | double | Valor de fechamento |
| valor_indice | double | Valor informado para o índice |
| oscilacao | double | Oscilação informada pela B3 |
| fonte | string | Origem dos dados |
| data_ingestao | timestamp | Data e hora de ingestão |

---

## 2. Camada Silver

A camada Silver contém os dados tratados e padronizados. Nesta etapa são aplicadas regras de qualidade, padronização de tipos, seleção de campos relevantes e definição das granularidades.

### 2.1 `silver.criptomoedas`

**Granularidade:** um registro por ativo e por dia.

**Chave lógica:** `data` + `ativo`.

**Principais campos:** data, ativo, moeda de cotação, abertura, máxima, mínima, fechamento, volume, volume de cotação, número de negociações, fonte e data de ingestão.

---

### 2.2 `silver.dolar`

**Granularidade:** um registro por dia com cotação disponível.

**Chave lógica:** `data`.

**Principais campos:** data, cotação de compra, cotação de venda, fonte e data de ingestão.

---

### 2.3 `silver.selic`

**Granularidade:** um registro por dia.

**Chave lógica:** `data`.

**Principais campos:** data, Meta Selic em percentual ao ano, fonte e data de ingestão.

---

### 2.4 `silver.ipca`

**Granularidade:** um registro por mês.

**Chave lógica:** `mes_referencia`.

**Principais campos:** mês de referência, IPCA mensal, fonte e data de ingestão.

---

### 2.5 `silver.ibovespa`

**Granularidade:** um registro por pregão.

**Chave lógica:** `data`.

**Principais campos:** data, ativo, abertura, mínima, máxima, fechamento, valor do índice, oscilação, fonte e data de ingestão.

---

## 3. Camada Gold

A camada Gold reúne as tabelas analíticas utilizadas para responder às perguntas de negócio.

### 3.1 `gold.precos_diarios`

**Finalidade:** consolidar os preços diários de fechamento e calcular retornos entre observações consecutivas.

**Granularidade:** ativo + data.

**Chave lógica:** `data` + `ativo`.

**Principais campos:** data, ativo, classe do ativo, unidade de preço, preço de fechamento e retorno diário.

---

### 3.2 `gold.mercado_mensal`

**Finalidade:** armazenar o último fechamento disponível de cada ativo em cada mês.

**Granularidade:** ativo + mês.

**Chave lógica:** `mes_referencia` + `ativo`.

**Principais campos:** mês de referência, ativo, data de fechamento, preço de fechamento e retorno mensal.

---

### 3.3 `gold.macro_mensal`

**Finalidade:** consolidar os indicadores macroeconômicos em frequência mensal.

**Granularidade:** mês.

**Chave lógica:** `mes_referencia`.

**Principais campos:** mês de referência, IPCA mensal, USD/BRL, variação mensal do dólar, Meta Selic, variação da Selic e fator de inflação.

---

### 3.4 `gold.analise_mensal`

**Finalidade:** integrar dados de mercado e indicadores macroeconômicos.

**Granularidade:** ativo + mês.

**Chave lógica:** `mes_referencia` + `ativo`.

**Principais campos:** retorno original, retorno aproximado em reais, retorno real, IPCA, dólar e Selic.

**Observação:** para as criptomoedas, a conversão para reais considera a PTAX USD/BRL e assume paridade entre USDT e USD.

---

### 3.5 `gold.desempenho_ativos`

**Finalidade:** resumir os principais indicadores de desempenho e risco por ativo.

**Granularidade:** ativo.

**Chave lógica:** `ativo`.

**Principais campos:** retorno acumulado, retorno anualizado, volatilidade anualizada, relação retorno/volatilidade, retorno real acumulado e drawdown máximo.

---

### 3.6 `gold.volatilidade_anual`

**Finalidade:** comparar retorno e volatilidade dos ativos por ano.

**Granularidade:** ativo + ano.

**Chave lógica:** `ativo` + `ano`.

**Principais campos:** meses com retorno, retorno médio mensal, retorno acumulado no ano e volatilidade anualizada.

---

### 3.7 `gold.correlacoes`

**Finalidade:** registrar as correlações de Pearson entre ativos e indicadores macroeconômicos.

**Granularidade:** par de variáveis + perspectiva.

**Chave lógica:** `perspectiva` + `variavel_a` + `variavel_b`.

**Principais campos:** perspectiva, variável A, variável B, número de observações e correlação de Pearson.

---

### 3.8 `gold.cenarios_mensais`

**Finalidade:** classificar os meses em baixa, média ou alta volatilidade.

**Granularidade:** mês.

**Chave lógica:** `mes_referencia`.

**Principais campos:** mês de referência, quantidade de pregões, volatilidade realizada anualizada e cenário.

---

### 3.9 `gold.desempenho_cenarios`

**Finalidade:** comparar o comportamento dos ativos nos diferentes cenários de volatilidade.

**Granularidade:** cenário + ativo.

**Chave lógica:** `cenario` + `ativo`.

**Principais campos:** retorno médio mensal, volatilidade mensal, proporção de meses positivos, pior retorno e melhor retorno.

---

### 3.10 `gold.evolucao_acumulada`

**Finalidade:** acompanhar a evolução acumulada nominal e real dos ativos.

**Granularidade:** ativo + mês.

**Chave lógica:** `mes_referencia` + `ativo`.

**Principais campos:** índice nominal, índice real, retorno acumulado e retorno real acumulado.

---

## 4. Observações sobre o modelo

As camadas seguem uma arquitetura Medallion:

- **Bronze:** dados coletados e persistidos com rastreabilidade da fonte.
- **Silver:** dados padronizados, tratados e validados.
- **Gold:** dados integrados e preparados para análise.

As diferentes frequências das fontes foram preservadas durante o tratamento. Séries diárias não foram preenchidas artificialmente para finais de semana ou dias sem pregão, e o IPCA foi mantido em frequência mensal.

As tabelas analíticas foram construídas apenas após as validações de qualidade das camadas anteriores.