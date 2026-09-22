# Aviação ANAC — VoeBem Analytics

Materiais de estudo da turma da **Imersão Engenharia de Dados — setembro de 2026**, com dados da ANAC e práticas de Python, SQL e Databricks.

## Sobre o projeto

A **VoeBem Analytics** é a consultoria fictícia usada neste projeto. A proposta é analisar dados de voos para entender:

- atrasos;
- cancelamentos; e
- pontualidade.

Este é um projeto colaborativo e educacional da turma, sem vínculo oficial com a Alura.

> **Aviso:** o material foi preparado para apoiar as aulas, mas a execução completa em um workspace Databricks novo ainda precisa ser validada. As cargas podem substituir tabelas existentes; use um ambiente destinado ao projeto.

## Primeira vez aqui?

Você pode começar pelo navegador, sem instalar Python ou Git:

1. Baixe este repositório como ZIP ou faça um fork.
2. Leia a seção [Preparação](#preparação).
3. Siga a [sequência de estudo e execução](#sequência-de-estudo-e-execução).
4. Depois, explore as perguntas de negócio e as consultas SQL de exemplo.

Os arquivos CSV já estão incluídos no repositório. Portanto, você não precisa fazer um novo download para acompanhar as aulas.

## O que há em cada pasta?

| Pasta | Conteúdo |
| --- | --- |
| `dados/` | 15 CSVs da ANAC: 12 meses de VRA, de agosto de 2025 a julho de 2026, e três cadastros de referência. |
| `notebooks/` | Ingestão da camada Bronze, transformação da Silver e governança da Gold. Os arquivos `.py` estão no formato de notebooks Databricks. |
| `pipelines/` | Regras de qualidade e dados em quarentena para diagnóstico. |
| `sql/gold/` | Scripts para criar a dimensão de aeroportos, o fato de voos e a tabela de consumo (OBT). |
| `sql/gabarito/` | Consultas que respondem às perguntas de negócio. |
| `scripts/` | Download dos dados e utilitários opcionais para execução e Genie. |
| `genie/` | Instruções e configuração de exemplo para fazer perguntas em linguagem natural. |
| `docs/` | Fontes, perguntas de negócio e registro de aceitação do material original. |

## Preparação

A instalação local só é necessária se você quiser usar os scripts pelo terminal. Nesse caso:

- use Python 3.10 ou superior;
- não é necessário instalar bibliotecas adicionais: os scripts usam a biblioteca padrão;
- os notebooks devem ser executados no ambiente Spark/Databricks, não como scripts Python locais.

### 1. Obtenha os arquivos

Baixe ou clone este repositório e abra um terminal na pasta do projeto.

Os CSVs já estão em `dados/`. Para recuperar arquivos ausentes, execute:

```bash
python scripts/baixar_anac.py
```

O script tenta obter os dados do período de agosto de 2025 a julho de 2026, reaproveita arquivos existentes e registra o download em `docs/fontes.md`. A disponibilidade dos arquivos depende do portal de origem.

### 2. Prepare o Databricks

No seu workspace de estudos:

1. Execute `sql/00_preparar_ambiente.sql`.
2. Confirme que você tem permissão para criar catálogo, schemas e volume.
3. Envie o conteúdo de `dados/vra/` para `/Volumes/voebem/bronze/arquivos/vra/`.
4. Envie o conteúdo de `dados/referencias/` para `/Volumes/voebem/bronze/arquivos/referencias/`.
5. Importe os arquivos de `notebooks/` como notebooks Databricks.

O código usa o catálogo `voebem`. Se você utilizar outro nome, atualize as referências nos notebooks, nos scripts SQL e na configuração do Genie.

## Sequência de estudo e execução

Execute as etapas na ordem abaixo:

1. `notebooks/03_bronze_vra.py`
2. `notebooks/04_bronze_referencias.py`
3. `notebooks/05_silver_espelho.py`
4. Configure e execute um pipeline de qualidade com os três arquivos de `pipelines/qualidade/`, usando o catálogo `voebem` e o schema `silver`.
5. Para usar `sql/metricas_qualidade.sql`, configure também a publicação do log de eventos do pipeline na tabela `voebem.silver.eventos_qualidade`.
6. Execute, nesta ordem, os scripts SQL da camada Gold:
   - `sql/gold/01_dim_aeroporto.sql`
   - `sql/gold/02_fato_voos.sql`
   - `sql/gold/03_obt_voos.sql`
7. Execute `notebooks/09_governanca_gold.py`.
8. Explore as consultas em `sql/gabarito/` e as perguntas em `docs/perguntas-de-negócio.md`.

### Genie (opcional)

Você pode configurar um espaço Genie usando a tabela `voebem.gold.obt_voos`. Consulte os exemplos e as instruções em `genie/`.

O script abaixo apenas regenera o arquivo JSON de configuração; ele não cria o espaço no Databricks:

```bash
python scripts/montar_genie_space.py
```

A numeração original dos arquivos foi preservada. Por isso, números ausentes não significam que existam arquivos faltando neste pacote.

## Utilitários opcionais

Os scripts que acessam o Databricks exigem a CLI instalada e autenticada no seu próprio workspace. O perfil padrão é `alura-imersao`; para usar outro perfil, defina `DATABRICKS_CONFIG_PROFILE`.

No PowerShell, consulte seu espaço Genie assim:

```powershell
$env:DATABRICKS_CONFIG_PROFILE = "alura-imersao"
$env:GENIE_SPACE_ID = "ID_DO_SEU_ESPACO"
python scripts/perguntar_genie.py "Quais aeroportos concentram os maiores atrasos?"
```

Os scripts `.sh` exigem Bash, como Git Bash ou WSL. Para `rodar_notebook.sh`, defina também `DATABRICKS_WORKSPACE_PATH` com a pasta dos notebooks no workspace. O script `rodar_pipeline.sh` recebe o ID do pipeline como primeiro argumento. Defina as variáveis no mesmo terminal em que o script será executado.

## Validação e contribuições

Compare as respostas do Genie com as consultas SQL correspondentes. Os números em `docs/teste-aceitacao.md` são registros do material original; eles não representam uma validação deste ambiente e podem mudar quando os dados forem atualizados.

Para contribuir:

1. descreva a alteração;
2. informe como você verificou o resultado; e
3. abra um pull request.

Nunca inclua credenciais ou configurações pessoais. Ao alterar os CSVs, registre a fonte, o período e o motivo da atualização para manter os exercícios reproduzíveis.

## Créditos

- **Contexto educacional:** Imersão Engenharia de Dados da Alura, setembro de 2026.
- **Fonte dos dados:** ANAC, conforme `docs/fontes.md`.
- **Código-base:** preservado a partir do material compartilhado durante a imersão.
