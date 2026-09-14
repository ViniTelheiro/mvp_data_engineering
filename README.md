# MVP de Engenharia de Dados - Olist E-Commerce

Pipeline de Engenharia de Dados desenvolvida em Databricks para processar o dataset brasileiro de e-commerce da Olist, seguindo a arquitetura Medallion com camadas Bronze, Silver e Gold.

## Visão geral

Este projeto tem como objetivo transformar dados brutos de e-commerce em um conjunto de dados pronto para análise e tomada de decisão. A estrutura foi pensada para demonstrar um fluxo realista de dados em um ambiente analítico, com:

- ingestão de arquivos CSV em um catálogo de dados;
- padronização e limpeza de dados;
- tipagem correta das colunas;
- criação de camadas analíticas orientadas a negócio;
- resposta a perguntas estratégicas de e-commerce.

### Dataset

A base utilizada é o Brazilian E-Commerce Public Dataset by Olist, um conjunto de dados que reúne informações sobre clientes, vendedores, produtos, pedidos, pagamentos, avaliações e geolocalização.
- dataset: Brazilian E-Commerce Public Dataset by Olist
- dataset link: https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce
- dataset licence: [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)

#### Estrutura do Dataset

- `olist_customers_dataset.csv`
  - informações dos clientes
- `olist_geolocation_dataset.csv`
  - geolocalização dos clientes e vendedores
- `olist_order_items_dataset.csv`
  - itens incluídos em cada pedido
- `olist_order_payments_dataset.csv`
  - métodos e valores de pagamento
- `olist_order_reviews_dataset.csv`
  - avaliações e comentários dos clientes
- `olist_orders_dataset.csv`
  - pedidos e status de entrega
- `olist_products_dataset.csv`
  - catálogo de produtos
- `olist_sellers_dataset.csv`
  - informações dos vendedores
- `product_category_name_translation.csv`
  - tradução dos nomes de categorias

## Objetivo do projeto

O projeto foi construído para responder perguntas de negócio relevantes em um cenário de e-commerce, como:

1. Quais categorias de produtos geram maior faturamento?
2. Produtos com mais fotos em sua descrição vendem mais?
3. Produtos entregues com atrazo impactam na avaliação do cliente?
4. Quais estados apresentam maior volume de vendas?

Essas perguntas são respondidas a partir de tabelas analíticas preparadas na camada Gold.

## Arquitetura Medallion

A solução segue o modelo de arquitetura Medallion:

### 1. Bronze
A camada Bronze recebe os dados brutos em formato CSV e os persiste no catálogo do Databricks sem perder a origem original. Nessa etapa:

- os arquivos são lidos diretamente da pasta `dataset/`;
- cada tabela é salva como tabela Delta no esquema `mvp.bronze`;
- comentários de tabela e colunas são adicionados para facilitar a governança e a compreensão dos dados;
- a camada mantém o histórico bruto, em formato próximo ao fonte.

### 2. Silver
A camada Silver representa a etapa de limpeza e padronização dos dados. Nessa fase:

- colunas são renomeadas para nomes mais claros e consistentes;
- tipos de dados são ajustados para refletir o significado real de cada campo;
- inconsistências e problemas de formato são corrigidos;
- dados irrelevantes para o MVP são removidos ou não utilizados;
- a tabela de tradução de categorias é descartada para manter o conjunto analítico focado no objetivo do projeto.

### 3. Gold
A camada Gold é a camada analítica final, preparada para consultas e relatórios. Nela são montadas tabelas agregadas e modeladas para consumo de negócio, reduzindo a necessidade de joins complexos e otimizando a análise.


## Estrutura do repositório

```text
mvp_data_engineering/
├── dataset/
│   ├── olist_customers_dataset.csv
│   ├── olist_geolocation_dataset.csv
│   ├── olist_order_items_dataset.csv
│   ├── olist_order_payments_dataset.csv
│   ├── olist_order_reviews_dataset.csv
│   ├── olist_orders_dataset.csv
│   ├── olist_products_dataset.csv
│   ├── olist_sellers_dataset.csv
│   └── product_category_name_translation.csv
├── img/
│   └── assets e imagens relacionadas ao projeto
├── exploratory_analysis.ipynb
├── main.ipynb
├── main_with_output.ipynb
├── README.md
├── LICENSE
└── .gitignore
```

## Tecnologias e ferramentas

- Databricks
- Apache Spark / PySpark
- Delta Lake
- Python
- Polars
- KaggleHub
- Jupyter / Databricks notebooks

## Como reproduzir o projeto

### Pré-requisitos

- acesso ao Databricks ou ambiente com Spark configurado;
- Python 3.11+;
- dependências do projeto instaladas;
- arquivos do dataset disponíveis localmente ou no armazenamento do Databricks.

### 1. Clonar o repositório

```bash
git clone <url-do-repositorio>
cd mvp_data_engineering
```

### 2. Carregar os dados

Os arquivos CSV devem estar disponíveis dentro da pasta `dataset/` ou acessíveis no ambiente do Databricks.

Se necessário, o dataset pode ser obtido via KaggleHub seguindo a referência usada no notebook.

## Fluxo do pipeline

### Etapa 1: ingestão na camada Bronze

- leitura dos CSVs;
- criação do catálogo `mvp`;
- criação do esquema `mvp.bronze`;
- persistência das tabelas em Delta;
- registro de comentários nas tabelas.

### Etapa 2: limpeza e padronização na camada Silver

- renomeação de colunas para nomes mais amigáveis;
- ajuste de tipos;
- eliminação de colunas ou tabelas que não serão usadas na camada Gold;
- preparação dos dados para joins e análise.

### Etapa 3: construção da camada Gold

- criação de tabelas analíticas prontas para consumo;
- organização dos dados em uma estrutura simplificada;
- suporte às perguntas de negócio definidas no projeto.

## Modelagem de dados e principais tabelas

A base foi modelada para contemplar relações entre clientes, vendedores, pedidos, itens, pagamentos e avaliações. A estrutura do projeto contempla fluxos típicos de e-commerce:

- `customers` → clientes
- `sellers` → vendedores
- `orders` → pedidos
- `order_items` → itens do pedido
- `payments` → pagamentos
- `reviews` → avaliações
- `products` → produtos
- `geolocation` → localização

Na camada Gold, as tabelas são organizadas para responder às métricas desejadas sem exigir muitas transformações ad hoc.

## Perguntas de negócio respondidas

O projeto foi pensado para responder de maneira direta as seguintes análises:

- faturamento por categoria de produto;
- volume de vendas por quantidade de fotos do produto;
- correlação entre atrasos nas entregas e score de avaliação;
- volume de vendas por estado.

Essas perguntas são executadas por consultas SQL e transformações em Spark sobre as camadas de dados preparadas.

## Como usar os notebooks

### `main.ipynb`

Notebook principal do projeto. Ele contém a lógica completa do pipeline, incluindo:

- criação de catálogo e esquemas;
- leitura dos arquivos CSV;
- montagem do Bronze;
- transformação para Silver;
- preparação da camada Gold;
- execução das consultas analíticas.

### `exploratory_analysis.ipynb`

Notebook complementar usado para explorar o dataset, validar colunas, entender os relacionamentos entre as tabelas e identificar problemas e oportunidades de limpeza.

### `main_with_output.ipynb`

Versão com outputs executados, útil para revisão visual da pipeline e resultados obtidos.

## Observações importantes

- O projeto foi desenvolvido em um contexto de Databricks, portanto o uso de `spark.sql` e `saveAsTable` é central na implementação;
- alguns campos de texto e review podem exigir tratamento especial de multiline e escaping ao importar CSVs;
- a tabela `product_category_name_translation` foi descartada no fluxo do MVP por não ser essencial para as quatro perguntas de negócio escolhidas;
- a intenção deste projeto foi demonstrar uma pipeline analítica prática, não necessariamente uma solução de produção completa com orquestração de jobs e automações avançadas.

## Próximos passos possíveis

- automatizar a pipeline com jobs programados no Databricks;
- criar tabelas adicionais na camada Gold para outros indicadores;
- adicionar testes de qualidade de dados;
- gerar dashboards com Power BI, Tableau ou Databricks SQL;
- integrar o projeto a um pipeline de CI/CD para versionamento e deploy de artefatos de dados.

## Licença

Este projeto usa o dataset publicamente disponível do Brazilian E-Commerce Public Dataset by Olist.

Para uso do código e dos notebooks, observe também a licença do repositório e a política de uso dos dados da fonte original.

---

Projeto desenvolvido como exemplo de pipeline de dados em arquitetura Medallion com foco em análise de e-commerce.
