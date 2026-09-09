# Metodologia e Arquitetura do Pipeline

Este documento descreve a metodologia adotada no desenvolvimento do MVP de Engenharia de Dados, incluindo a arquitetura do pipeline, as tecnologias utilizadas, as decisões de modelagem e os principais cuidados aplicados ao tratamento dos dados.

O projeto foi desenvolvido com foco em reprodutibilidade, rastreabilidade e organização em camadas, utilizando dados do mercado financeiro, criptomoedas e indicadores macroeconômicos no período de janeiro de 2021 a dezembro de 2025.

---

## 1. Visão geral do projeto

O objetivo do pipeline é integrar dados de diferentes fontes e frequências para permitir análises comparativas entre criptomoedas, Ibovespa, dólar, Meta Selic e IPCA.

As principais etapas do processo foram:

1. coleta dos dados nas fontes externas;
2. persistência dos dados na camada Bronze;
3. padronização e validação na camada Silver;
4. criação de indicadores e tabelas analíticas na camada Gold;
5. realização das análises de negócio.

A separação em etapas facilita a manutenção do projeto e permite identificar com mais clareza onde cada transformação é realizada.

---

## 2. Tecnologias utilizadas

O projeto foi desenvolvido no Databricks Free Edition, utilizando recursos serverless.

As principais tecnologias utilizadas foram:

- **Python:** utilizado principalmente na coleta de dados por APIs e no tratamento inicial das respostas;
- **Pandas:** utilizado na organização inicial dos dados coletados;
- **PySpark:** utilizado no processamento, transformação e validação dos dados dentro do Databricks;
- **Spark SQL:** utilizado para consultas, criação de indicadores e análises;
- **Delta Lake:** utilizado para persistência das tabelas das camadas Bronze, Silver e Gold;
- **Git e GitHub:** utilizados para versionamento dos notebooks e da documentação do projeto;
- **Markdown:** utilizado para documentação técnica e explicação das análises.

Os dados processados permanecem armazenados no Databricks, enquanto notebooks e documentação são versionados no GitHub.

---

## 3. Arquitetura do pipeline

O projeto segue uma arquitetura Medallion, organizada em três camadas principais:

**Fontes de dados → Coleta → Bronze → Silver → Gold → Análises**

### 3.1 Camada Bronze

A camada Bronze recebe os dados coletados das fontes externas.

Seu principal objetivo é manter uma versão persistida dos dados utilizados no projeto, preservando informações de origem e data de ingestão.

Nesta camada foram criadas as seguintes tabelas:

- `bronze.criptomoedas`
- `bronze.dolar`
- `bronze.selic`
- `bronze.ipca`
- `bronze.ibovespa`

A persistência em Delta permite que as etapas seguintes utilizem os dados já armazenados, sem necessidade de executar novamente as coletas.

---

### 3.2 Camada Silver

A camada Silver é responsável pelo tratamento e padronização dos dados.

Nesta etapa foram aplicadas transformações como:

- padronização de nomes de colunas;
- definição de tipos de dados;
- seleção de campos relevantes;
- organização das datas;
- manutenção da granularidade original de cada fonte;
- validação de valores nulos;
- verificação de duplicidades;
- validação de valores numéricos;
- verificação da cobertura temporal.

As tabelas Silver criadas foram:

- `silver.criptomoedas`
- `silver.dolar`
- `silver.selic`
- `silver.ipca`
- `silver.ibovespa`

Os dados inválidos não são gravados silenciosamente. As validações foram configuradas para interromper a execução caso seja identificada alguma inconsistência relevante.

---

### 3.3 Camada Gold

A camada Gold foi criada para concentrar os dados preparados para análise.

Nessa etapa foram calculados indicadores de retorno, volatilidade, retorno real, drawdown, correlação e comportamento em diferentes cenários de mercado.

As principais tabelas Gold são:

- `gold.precos_diarios`
- `gold.mercado_mensal`
- `gold.macro_mensal`
- `gold.analise_mensal`
- `gold.desempenho_ativos`
- `gold.volatilidade_anual`
- `gold.correlacoes`
- `gold.cenarios_mensais`
- `gold.desempenho_cenarios`
- `gold.evolucao_acumulada`

Essas tabelas são utilizadas diretamente no notebook de análises de negócio.

---

## 4. Fontes de dados

O projeto utiliza fontes públicas e oficiais ou amplamente utilizadas no mercado.

### Criptomoedas

Os dados de Bitcoin, Ethereum, Solana e XRP foram coletados por meio da Binance API.

Os pares analisados foram:

- BTCUSDT;
- ETHUSDT;
- SOLUSDT;
- XRPUSDT.

Os dados possuem frequência diária.

---

### Dólar

A cotação USD/BRL foi obtida por meio da API PTAX do Banco Central do Brasil.

Foi utilizada a cotação de venda como principal referência cambial nas análises.

---

### Meta Selic

A Meta Selic foi obtida por meio da série 432 do Sistema Gerenciador de Séries Temporais do Banco Central do Brasil.

Os valores representam a meta definida pelo Copom, expressa em percentual ao ano.

---

### IPCA

O IPCA foi obtido por meio da série 433 do Banco Central do Brasil.

O indicador possui frequência mensal e representa a variação percentual da inflação em cada mês.

---

### Ibovespa

Os dados do Ibovespa foram obtidos a partir dos arquivos oficiais da B3, utilizando o relatório BVBG.087.01 IndexReport.

A coleta foi automatizada por data de pregão, com tratamento de arquivos compactados e leitura dos dados em XML.

---

## 5. Período analisado

O período de coleta utilizado no projeto foi de:

**01/01/2021 a 31/12/2025**

As criptomoedas possuem dados diários em todos os dias do período.

O Ibovespa e o dólar seguem calendários de negociação e dias úteis, portanto não possuem observações em finais de semana e em determinados feriados.

O IPCA possui frequência mensal.

A Meta Selic apresenta registros compatíveis com o período analisado.

Para as análises mensais, o primeiro retorno comparável ocorre em fevereiro de 2021, pois janeiro de 2021 é utilizado como referência inicial.

---

## 6. Tratamento das diferentes frequências

Uma das principais decisões metodológicas do projeto foi evitar o preenchimento artificial das diferentes séries.

Os dados diários do Ibovespa não foram preenchidos em finais de semana ou dias sem pregão.

O dólar mantém apenas datas com cotação disponível.

O IPCA permanece em frequência mensal.

Para análises integradas, os dados foram agregados para uma granularidade mensal compatível.

O fechamento mensal de cada ativo corresponde ao último valor disponível em cada mês.

Essa abordagem evita criar observações que não existiam originalmente nas fontes.

---

## 7. Retornos

Os retornos foram calculados entre observações consecutivas.

Para análises mensais, foi utilizada a variação entre o último fechamento disponível de cada mês e o fechamento do mês anterior.

O retorno acumulado foi calculado de forma composta, utilizando a multiplicação dos fatores de retorno, e não a soma simples das variações mensais.

A mesma lógica foi utilizada na composição do retorno real.

---

## 8. Conversão das criptomoedas para reais

As criptomoedas foram coletadas em pares cotados em USDT.

Para permitir a comparação com ativos e indicadores brasileiros, foi criada uma aproximação dos retornos em reais utilizando a cotação PTAX USD/BRL.

A metodologia assume paridade entre USDT e USD.

Essa hipótese é utilizada apenas como aproximação analítica e está registrada como uma limitação do projeto, pois eventuais desvios entre USDT e USD não são considerados.

---

## 9. Retorno real e inflação

O retorno real foi calculado considerando os retornos mensais dos ativos e o IPCA mensal.

A relação utilizada segue a lógica:

`retorno real = (1 + retorno nominal) / (1 + inflação) - 1`

Os retornos reais mensais foram posteriormente compostos para obtenção do retorno real acumulado.

O IPCA não foi transformado em frequência diária, evitando incompatibilidade entre a frequência do indicador e a dos retornos de mercado.

---

## 10. Volatilidade e risco

A volatilidade foi utilizada como medida de dispersão dos retornos.

Para permitir comparação entre os ativos, as análises utilizaram retornos mensais e volatilidade anualizada.

Também foi calculado o drawdown máximo, que representa a maior queda observada entre um ponto de máximo acumulado e o valor posterior dentro do período analisado.

A relação retorno/volatilidade foi utilizada como indicador descritivo para comparar desempenho e risco.

Essa métrica não corresponde ao índice de Sharpe, pois não considera uma taxa livre de risco.

---

## 11. Correlações

As relações entre ativos e indicadores foram analisadas por meio da correlação de Pearson.

Foram avaliadas associações entre:

- criptomoedas;
- Bitcoin e Ibovespa;
- retornos dos ativos e variação do dólar;
- retornos dos ativos e Meta Selic;
- retornos dos ativos e variação da Meta Selic.

As correlações foram calculadas com observações mensais alinhadas no tempo.

Os resultados são utilizados apenas para análise de associação e não para estabelecer relações de causalidade.

---

## 12. Cenários de volatilidade

Para analisar o comportamento dos ativos em diferentes condições de mercado, os meses foram classificados em três grupos:

- baixa volatilidade;
- média volatilidade;
- alta volatilidade.

A classificação foi definida a partir da volatilidade realizada do Ibovespa observada em cada mês.

Os cenários são retrospectivos e servem apenas para comparação do comportamento histórico dos ativos.

Eles não representam sinais preditivos ou mecanismos de antecipação de crises.

---

## 13. Qualidade dos dados

As camadas Silver e Gold possuem verificações de qualidade antes da persistência.

Entre as principais regras aplicadas estão:

- ausência de valores nulos em campos obrigatórios;
- inexistência de duplicidades nas chaves lógicas;
- preços positivos;
- retornos dentro de limites válidos;
- volatilidade não negativa;
- correlações no intervalo entre -1 e 1;
- fator de inflação positivo;
- cobertura temporal esperada;
- validação da quantidade de registros persistidos.

Quando alguma regra não é atendida, a execução é interrompida para evitar gravação de dados inconsistentes.

---

## 14. Persistência e reprodutibilidade

As tabelas das três camadas são persistidas em formato Delta.

Os notebooks de Silver e Gold leem diretamente as tabelas já persistidas e não executam novamente as rotinas de coleta.

Isso reduz o tempo de execução e evita dependência desnecessária das APIs em cada etapa do pipeline.

O uso de `overwrite` nas tabelas processadas permite reproduzir integralmente as camadas a partir dos dados disponíveis na etapa anterior.

O código e a documentação são versionados no GitHub, permitindo acompanhar a evolução do projeto.

---

## 15. Rastreabilidade

A rastreabilidade dos dados é mantida por meio de:

- identificação da fonte;
- data de ingestão;
- separação das camadas Bronze, Silver e Gold;
- documentação das tabelas;
- comentários registrados no catálogo do Databricks;
- versionamento dos notebooks no GitHub.

O Data Catalog do projeto complementa essa documentação, apresentando as principais tabelas, granularidades, chaves e campos.

---

## 16. Considerações metodológicas

A metodologia foi definida buscando preservar as características originais das fontes e evitar transformações que pudessem gerar observações artificiais.

Ao mesmo tempo, foram criadas tabelas integradas em granularidade mensal para permitir comparação entre ativos financeiros e indicadores macroeconômicos.

As decisões adotadas priorizam clareza, rastreabilidade e reprodutibilidade, considerando o escopo acadêmico do MVP.