# Autoavaliação

O desenvolvimento deste MVP permitiu aplicar, de forma prática, conceitos de Engenharia de Dados envolvendo coleta, organização, transformação, persistência, qualidade, modelagem e análise.

Ao longo do projeto, foi necessário trabalhar com diferentes fontes, formatos e frequências de dados, além de tomar decisões metodológicas para tornar as informações comparáveis e adequadas às perguntas de negócio definidas no início.

## 1. Principais aprendizados

Um dos principais aprendizados foi compreender a importância da organização do pipeline em camadas.

A separação entre Bronze, Silver e Gold ajudou a estruturar melhor o fluxo de trabalho e deixou mais claro o papel de cada etapa. A Bronze ficou responsável pela persistência dos dados coletados, a Silver pelo tratamento e padronização, e a Gold pela integração e preparação dos indicadores utilizados nas análises.

Também foi possível perceber, na prática, como diferentes ferramentas se complementam. Python e Pandas foram mais úteis na coleta inicial, enquanto PySpark e Spark SQL foram mais adequados para transformação, validação e análise no Databricks.

Outro aprendizado importante foi relacionado à qualidade dos dados. Durante o desenvolvimento, ficou evidente que não basta apenas coletar e transformar informações. É necessário validar tipos, duplicidades, valores nulos, consistência numérica, cobertura temporal e chaves antes de utilizar os dados em análises.

## 2. Principais desafios

A coleta dos dados do Ibovespa foi uma das etapas mais desafiadoras do projeto.

Foi necessário entender a estrutura dos arquivos oficiais da B3, trabalhar com arquivos compactados, XML e diferentes tentativas de download até chegar a uma rotina que funcionasse de forma automatizada para o período analisado.

Outro desafio foi a coleta histórica das criptomoedas. A fonte testada inicialmente não atendia ao período necessário no plano gratuito, o que exigiu a substituição pela Binance API e a adaptação da lógica de coleta.

Também foi necessário lidar com diferenças de frequência entre as fontes. Criptomoedas possuem dados diários inclusive em finais de semana, enquanto Ibovespa e dólar seguem calendários específicos, e o IPCA possui frequência mensal. A decisão foi preservar essas características nas camadas iniciais e realizar a compatibilização apenas na etapa analítica.

## 3. Decisões que precisaram ser revisadas

Durante o desenvolvimento, algumas decisões foram ajustadas à medida que o comportamento das fontes e das ferramentas foi sendo compreendido.

A escolha da fonte de dados de criptomoedas foi revisada após a identificação de uma limitação de acesso ao histórico necessário.

A coleta do Ibovespa também passou por ajustes até que fosse possível automatizar corretamente o acesso aos arquivos da B3 e interpretar os campos utilizados no XML.

Na camada analítica, também foram feitos ajustes metodológicos para diferenciar retornos originais em USDT dos retornos aproximados em reais e para evitar interpretações incorretas das correlações.

Essas revisões fizeram parte do processo de desenvolvimento e contribuíram para uma solução mais consistente.

## 4. Limitações do projeto

Apesar de atender aos objetivos definidos para o MVP, o projeto apresenta algumas limitações.

A conversão dos retornos das criptomoedas para reais utiliza a PTAX USD/BRL e assume paridade entre USDT e USD. Essa abordagem é adequada como aproximação para o escopo do trabalho, mas não representa exatamente uma operação real de conversão.

Também não foram considerados custos de negociação, impostos, spreads ou taxas de corretagem.

O Ibovespa foi analisado como índice em pontos e não como um produto de investimento que poderia incluir outras características, como dividendos, taxas de administração ou tracking error.

As correlações utilizadas são medidas de associação linear e não permitem estabelecer relações de causa e efeito.

Além disso, os resultados estão restritos ao período entre 2021 e 2025 e não devem ser interpretados como previsão de comportamento futuro.

## 5. Pontos que poderiam ser melhorados

Em uma evolução do projeto, seria interessante ampliar a quantidade de ativos analisados e incluir outros índices ou classes de investimento.

Também seria possível incorporar dados adicionais, como:

- CDI;
- taxa de câmbio intradiária;
- índices internacionais;
- preços de ouro;
- indicadores de mercado de trabalho;
- índices de volatilidade;
- informações de volume e liquidez.

Outra melhoria seria tornar a camada Bronze ainda mais próxima de uma estrutura de dados brutos, preservando os arquivos ou respostas originais das fontes antes de qualquer padronização.

Também seria interessante incluir testes automatizados de qualidade e mecanismos de monitoramento do pipeline.

## 6. Evoluções futuras

Como próximos passos, o projeto poderia ser transformado em um pipeline recorrente, com atualização automática dos dados.

Também seria possível criar dashboards para acompanhar os indicadores de forma visual e interativa.

Outra possibilidade seria estudar relações temporais com defasagens entre variáveis macroeconômicas e retornos dos ativos, além de testar métodos estatísticos mais avançados.

Essas evoluções não foram incluídas neste MVP por questão de escopo, mas representam caminhos naturais de continuidade.

## 7. Avaliação do resultado

Considero que o MVP atingiu o objetivo proposto de construir um pipeline de dados na nuvem integrando informações de mercado financeiro, criptomoedas e indicadores macroeconômicos.

O projeto foi além da simples coleta de dados, pois incluiu persistência em camadas, tratamento, validação de qualidade, modelagem analítica, documentação e respostas às perguntas de negócio.

Ao longo do desenvolvimento, algumas etapas exigiram revisão e ajustes, principalmente nas fontes de dados e na forma de integração das séries. No entanto, essas situações contribuíram para o aprendizado e para uma compreensão mais prática dos desafios envolvidos em projetos de Engenharia de Dados.

De forma geral, o resultado final atende ao escopo definido e cria uma base que pode ser expandida em trabalhos futuros.