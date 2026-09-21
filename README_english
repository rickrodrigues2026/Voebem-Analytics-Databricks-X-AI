ANAC Aviation — VoeBem Analytics Study materials for the Data Engineering Immersion class 
September/2026, with ANAC data, Python, SQL and Databricks.

VoeBem Analytics is the fictional consultancy used in the project: the goal is to investigate flight delays, cancellations and on-time performance. 
This repository is a collaborative initiative of the class, with no official affiliation with Alura.

First time here? Follow the Start here — beginner's guide: from downloading the materials to your first query. 
The path is browser-based, with the data included and without installing Python or Git on your computer.

Material status: prepared to support the classes. Full execution in a new workspace still needs to be validated.

Structure dados/: 15 ANAC CSVs included in the original material: 12 months of VRA (August/2025 to July/2026) and three reference registries. notebooks/: 
Bronze ingestion, Silver transformation and Gold governance. The .py files are in Databricks notebook format. pipelines/qualidade/: 
quality rules and quarantine for diagnostics. sql/gold/: airports dimension, flights fact and consumption table (OBT). sql/gabarito/: 
queries for the business questions. scripts/: data download and optional execution and Genie utilities. genie/: instructions and example configuration for natural language questions. 
docs/: sources, business questions and acceptance record provided in the original material. 
Preparation Local installation is only necessary if you choose the terminal scripts. Use Python 3.10 or higher for these scripts. 
They use the standard library. The notebooks depend on the Spark/Databricks environment and should not be run as local Python scripts.

Download or clone this repository and open a terminal in the project folder. The CSVs are already included in dados/, so there is no need to download them again. 
To recover missing files, run python scripts/baixar_anac.py. The script uses the August/2025 to July/2026 window, reuses existing files and updates docs/fontes.md with the download record. 
The availability of new downloads depends on the source portal. In your Databricks study environment, run sql/00_preparar_ambiente.sql. You need permission to create catalog, schemas and volume. 
Send the contents of dados/vra/ to /Volumes/voebem/bronze/arquivos/vra/ and the contents of dados/referencias/ to /Volumes/voebem/bronze/arquivos/referencias/. 
Import the files from notebooks/ as Databricks notebooks. The code uses the voebem catalog. If you choose another name, adjust the references in the notebooks, SQL and Genie configuration. 
The loads use overwrite or CREATE OR REPLACE: run in the environment intended for this project.

Study and execution sequence notebooks/03_bronze_vra.py notebooks/04_bronze_referencias.py notebooks/05_silver_espelho.py Set up a quality pipeline with the three files from pipelines/qualidade/, 
voebem catalog and silver schema, and run it in Databricks. To use sql/metricas_qualidade.sql, also configure the pipeline event log publication to the voebem.silver.eventos_qualidade table. 
Run sql/gold/01_dim_aeroporto.sql, 02_fato_voos.sql and 03_obt_voos.sql, in that order. Run notebooks/09_governanca_gold.py. Explore the queries in sql/gabarito/ and the questions in docs/perguntas-de-negocio.md. 
Optional: set up a Genie space with voebem.gold.obt_voos, using the examples and instructions in genie/. The python script scripts/montar_genie_space.py regenerates the JSON; it does not create the space in the service. 
The original numbering was preserved; missing numbers do not represent files missing from this package.

Optional utilities The scripts that access Databricks require the CLI installed and authenticated in your own workspace. 
The default profile is alura-imersao, replaceable via the DATABRICKS_CONFIG_PROFILE variable.

To query your Genie space in PowerShell:

$env:DATABRICKS_CONFIG_PROFILE = "alura-imersao" $env:GENIE_SPACE_ID = "ID_DO_SEU_ESPACO" python scripts/perguntar_genie.py "Which airports concentrate the biggest delays?" 
The .sh scripts require Bash, such as Git Bash or WSL. For rodar_notebook.sh, also set DATABRICKS_WORKSPACE_PATH with the notebooks folder in the workspace. 
The rodar_pipeline.sh script takes your pipeline ID as the first argument. The variables must be set in the terminal where the script will run.

Validation and contributions Compare the Genie answers with the SQL queries. The numbers and results in docs/teste-aceitacao.md are records from the original material; 
they are not a validation of an execution in your environment and may vary as the data is updated.

To contribute, describe the change and how you verified the result in a pull request. Do not include credentials or personal settings. When changing the CSVs, state the source, 
the period and the reason for the update to preserve the reproducibility of the exercises.

Credits Educational context: Alura's Data Engineering Immersion, September/2026. Data source: ANAC, as per docs/fontes.md. The base code was preserved from the material shared in the immersion, with preparation adjustments for publication.
