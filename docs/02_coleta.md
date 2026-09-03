# 2. Coleta dos Dados

O projeto utilizará dados provenientes de diferentes fontes públicas, integrando informações do mercado de criptomoedas, mercado financeiro brasileiro e indicadores macroeconômicos.

## Fontes previstas

| Dados | Fonte | Formato | Periodicidade |
|---|---|---|---|
| Bitcoin, Ethereum, Solana e XRP | Binance API | API / JSON | Diária |
| Ibovespa | B3 | Arquivo histórico | Diária |
| Dólar/Real | Banco Central do Brasil | API / JSON | Diária |
| Selic | Banco Central do Brasil – SGS | API / JSON | Diária |
| IPCA | Banco Central do Brasil – SGS | API / JSON | Mensal |

A Binance API foi utilizada como fonte para os dados históricos de criptomoedas por disponibilizar informações diárias de mercado compatíveis com o período analisado, compreendido entre janeiro de 2021 e dezembro de 2025.

Os dados obtidos serão persistidos no ambiente Databricks, inicialmente em sua forma bruta, preservando as informações originais das fontes. Posteriormente serão realizados os processos de tratamento, padronização e modelagem.

Os códigos utilizados para coleta e transformação serão versionados no GitHub. Os arquivos de dados brutos não serão armazenados no repositório.
