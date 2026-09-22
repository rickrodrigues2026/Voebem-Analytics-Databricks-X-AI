# ✈️ VoeBem Analytics

> **Projeto de Engenharia de Dados — Imersão Alura | Setembro de 2026**
>
> Análise de atrasos, cancelamentos e pontualidade de voos com dados da ANAC, Python, SQL e Databricks.

[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Databricks](https://img.shields.io/badge/Databricks-Analytics-EF3E42?logo=databricks&logoColor=white)](https://www.databricks.com/)
[![SQL](https://img.shields.io/badge/SQL-Data%20Engineering-336791?logo=databricks&logoColor=white)](https://www.sql.org/)
[![Status](https://img.shields.io/badge/status-material%20de%20estudo-2ea44f)](#status-do-projeto)

## 📌 Sobre o projeto

A **VoeBem Analytics** é uma consultoria fictícia criada para o projeto. O objetivo é transformar dados públicos da **ANAC** em informações úteis para investigar:

- atrasos de partida;
- cancelamentos e irregularidades;
- desempenho de pontualidade;
- aeroportos e rotas com maior impacto operacional.

Este repositório é uma iniciativa colaborativa da turma e **não possui afiliação oficial com a Alura**.

## 🎯 Objetivos de aprendizagem

- Construir um fluxo de dados em camadas **Bronze, Silver e Gold**;
- Ingerir e tratar arquivos CSV da ANAC;
- Aplicar regras de qualidade e quarentena de registros;
- Modelar dados para consumo analítico;
- Criar consultas SQL para responder perguntas de negócio;
- Explorar o uso do Databricks e do Genie com linguagem natural.

## 🗂️ Estrutura do repositório

| Diretório | Descrição |
|---|---|
| [`Dados/`](Dados/) | Arquivos CSV da ANAC incluídos no material de estudo. |
| [`notebooks/`](notebooks/) | Notebooks Databricks para ingestão, transformação e governança. |
| [`pipelines/qualidade/`](pipelines/qualidade/) | Regras de qualidade, validações e quarentena. |
| [`sql/gold/`](sql/gold/) | Scripts da dimensão de aeroportos, fato de voos e tabela OBT. |
| [`sql/gabarito/`](sql/gabarito/) | Consultas SQL de referência para as perguntas de negócio. |
| [`scripts/`](scripts/) | Utilitários para download, execução e integração com o Genie. |
| [`genie/`](genie/) | Instruções e configuração de exemplo para perguntas em linguagem natural. |
| [`docs/`](docs/) | Fontes, perguntas de negócio e registro de aceitação. |

## 🏗️ Arquitetura do fluxo de dados

```text
Arquivos CSV da ANAC
        │
        ▼
Bronze ──► ingestão dos dados brutos
        │
        ▼
Silver ──► padronização, transformação e qualidade
        │
        ▼
Gold ────► dimensão, fato e tabela OBT para análise
        │
        ├──► Consultas SQL
        └──► Databricks Genie
```

## 🚀 Como começar

### Opção 1 — Usando apenas o navegador

Os dados já estão incluídos no repositório. Para estudar sem instalar Python ou Git:

1. Abra o repositório no GitHub;
2. Acesse a pasta [`docs/`](docs/) para entender as perguntas de negócio;
3. Explore os notebooks e as consultas em [`sql/gabarito/`](sql/gabarito/);
4. Execute os scripts em um ambiente Databricks.

### Opção 2 — Execução local dos scripts

A instalação local só é necessária para os scripts de terminal. É recomendado usar **Python 3.10 ou superior**.

```bash
git clone https://github.com/rickrodrigues2026/Voebem-Analytics-Databricks-X-AI.git
cd Voebem-Analytics-Databricks-X-AI
```

Os CSVs já estão disponíveis na pasta `Dados/`. Para recuperar arquivos ausentes, execute:

```bash
python scripts/baixar_anac.py
```

> Os notebooks dependem do ambiente Spark/Databricks e não devem ser executados como scripts Python locais comuns.

## 🧱 Execução no Databricks

### 1. Preparar o ambiente

Execute:

```text
sql/00_preparar_ambiente.sql
```

É necessário ter permissão para criar o catálogo, os schemas e o volume. O projeto utiliza o catálogo `voebem` por padrão.

### 2. Enviar os arquivos para os volumes

Envie os arquivos para os caminhos correspondentes:

```text
Dados/vra/          → /Volumes/voebem/bronze/arquivos/vra/
Dados/referencias/  → /Volumes/voebem/bronze/arquivos/referencias/
```

### 3. Executar na ordem recomendada

#### Notebooks de ingestão e transformação

```text
notebooks/03_bronze_vra.py
notebooks/04_bronze_referencias.py
notebooks/05_silver_espelho.py
```

#### Pipeline de qualidade

Configure um pipeline com os arquivos de [`pipelines/qualidade/`](pipelines/qualidade/), utilizando o catálogo `voebem` e o schema `silver`.

Para utilizar [`sql/metricas_qualidade.sql`](sql/metricas_qualidade.sql), configure também a publicação do event log do pipeline na tabela:

```text
voebem.silver.eventos_qualidade
```

#### Camada Gold

Execute os scripts nesta ordem:

```text
sql/gold/01_dim_aeroporto.sql
sql/gold/02_fato_voos.sql
sql/gold/03_obt_voos.sql
```

Depois, execute:

```text
notebooks/09_governanca_gold.py
```

> Os processos utilizam `OVERWRITE` ou `CREATE OR REPLACE`. Execute-os no ambiente correto, pois os dados das tabelas de destino podem ser substituídos.

## 🔎 Consultas e perguntas de negócio

Depois de construir a camada Gold, explore:

- as consultas em [`sql/gabarito/`](sql/gabarito/);
- as perguntas em [`docs/perguntas-de-negocio.md`](docs/perguntas-de-negocio.md);
- os critérios de validação em [`docs/teste-aceitacao.md`](docs/teste-aceitacao.md).

A tabela principal para consumo analítico é:

```text
voebem.gold.obt_voos
```

## 🤖 Databricks Genie — opcional

É possível configurar um espaço Genie utilizando a tabela `voebem.gold.obt_voos`.

Consulte os exemplos e as instruções em [`genie/`](genie/). Para gerar novamente o arquivo de configuração:

```bash
python scripts/montar_genie_space.py
```

O script gera o JSON, mas não cria o espaço automaticamente.

Para consultar o Genie pelo PowerShell:

```powershell
$env:DATABRICKS_CONFIG_PROFILE = "alura-imersao"
$env:GENIE_SPACE_ID = "ID_DO_SEU_ESPACO"
python scripts/perguntar_genie.py "Quais aeroportos concentram os maiores atrasos?"
```

Os scripts `.sh` exigem Bash, Git Bash ou WSL. Para executar notebooks, configure também `DATABRICKS_WORKSPACE_PATH`. O script `rodar_pipeline.sh` recebe o ID do pipeline como primeiro argumento.

## 📊 Dados utilizados

- **Fonte:** Agência Nacional de Aviação Civil — ANAC;
- **Período incluído:** agosto de 2025 a julho de 2026;
- **Conteúdo:** 12 arquivos mensais de VRA e três cadastros de referência;
- **Formato:** arquivos CSV.

Os detalhes das fontes e dos downloads estão registrados em [`docs/fontes.md`](docs/fontes.md).

## ✅ Status do projeto

O material está preparado para apoiar as aulas e os estudos. A execução completa em um workspace novo do Databricks ainda precisa ser validada.

Os números apresentados em [`docs/teste-aceitacao.md`](docs/teste-aceitacao.md) são registros do material original. Eles podem variar conforme a atualização dos dados e o ambiente de execução.

## 🤝 Como contribuir

1. Crie uma branch para sua alteração;
2. Descreva claramente o que foi modificado;
3. Valide a alteração no ambiente adequado;
4. Abra um pull request com a descrição dos testes realizados.

Ao alterar os CSVs, informe sempre a **fonte, o período e o motivo da atualização**. Nunca inclua credenciais, tokens ou configurações pessoais no repositório.

## 📚 Créditos e licença

- **Contexto educacional:** Imersão em Engenharia de Dados da Alura — setembro de 2026;
- **Fonte dos dados:** ANAC, conforme [`docs/fontes.md`](docs/fontes.md);
- **Código-base:** preservado a partir do material compartilhado durante a imersão.

Consulte o arquivo [`LICENSE`](LICENSE) para as informações de licença.
