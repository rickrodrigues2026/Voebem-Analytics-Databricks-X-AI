# ANAC Aviation — VoeBem Analytics

Study materials from the **Data Engineering Immersion — September 2026**, using ANAC data and practices with Python, SQL, and Databricks.

## About the project

**VoeBem Analytics** is the fictional consultancy used in this project. The goal is to analyze flight data and understand:

- delays;
- cancellations; and
- on-time performance.

This is an educational, collaborative project created by the class. It has no official affiliation with Alura.

> **Notice:** the material is ready to support the classes, but a complete run in a new Databricks workspace has not yet been fully validated. Some loads overwrite or replace tables, so use a workspace intended for this project.

## Is this your first time here?

You can get started from your browser without installing Python or Git:

1. Download this repository as a ZIP file or fork it.
2. Read the [Preparation](#preparation) section.
3. Follow the [study and execution sequence](#study-and-execution-sequence).
4. Explore the business questions and example SQL queries.

The CSV files are already included in the repository, so you do not need to download them again to follow the classes.

## What is in each folder?

| Folder | Contents |
| --- | --- |
| `dados/` | 15 ANAC CSV files: 12 months of VRA data, from August 2025 through July 2026, plus three reference registries. |
| `notebooks/` | Bronze ingestion, Silver transformation, and Gold governance. The `.py` files use Databricks notebook format. |
| `pipelines/qualidade/` | Data-quality rules and quarantine data for diagnostics. |
| `sql/gold/` | Scripts for the airport dimension, flights fact table, and consumption table (OBT). |
| `sql/gabarito/` | Queries that answer the business questions. |
| `scripts/` | Data download and optional execution and Genie utilities. |
| `genie/` | Instructions and example configuration for natural-language questions. |
| `docs/` | Sources, business questions, and the acceptance record from the original material. |

## Preparation

Local installation is only required if you want to use the terminal scripts. In that case:

- use Python 3.10 or later;
- no additional packages are required because the scripts use the standard library; and
- run the notebooks in a Spark/Databricks environment, not as local Python scripts.

### 1. Get the files

Download or clone this repository and open a terminal in the project folder.

The CSV files are already in `dados/`. To recover missing files, run:

```bash
python scripts/baixar_anac.py
```

The script attempts to download data for August 2025 through July 2026, reuses existing files, and records the download in `docs/fontes.md`. The availability of new files depends on the source portal.

### 2. Prepare Databricks

In your study workspace:

1. Run `sql/00_preparar_ambiente.sql`.
2. Make sure you have permission to create a catalog, schemas, and a volume.
3. Upload the contents of `dados/vra/` to `/Volumes/voebem/bronze/arquivos/vra/`.
4. Upload the contents of `dados/referencias/` to `/Volumes/voebem/bronze/arquivos/referencias/`.
5. Import the files from `notebooks/` as Databricks notebooks.

The code uses the `voebem` catalog. If you choose another name, update the references in the notebooks, SQL scripts, and Genie configuration.

## Study and execution sequence

Run the following steps in order:

1. `notebooks/03_bronze_vra.py`
2. `notebooks/04_bronze_referencias.py`
3. `notebooks/05_silver_espelho.py`
4. Set up and run a quality pipeline with the three files in `pipelines/qualidade/`, using the `voebem` catalog and the `silver` schema.
5. To use `sql/metricas_qualidade.sql`, also configure the pipeline event-log publication to the `voebem.silver.eventos_qualidade` table.
6. Run the Gold SQL scripts in this order:
   - `sql/gold/01_dim_aeroporto.sql`
   - `sql/gold/02_fato_voos.sql`
   - `sql/gold/03_obt_voos.sql`
7. Run `notebooks/09_governanca_gold.py`.
8. Explore the queries in `sql/gabarito/` and the questions in `docs/perguntas-de-negócio.md`.

### Genie (optional)

You can set up a Genie space using the `voebem.gold.obt_voos` table. See the examples and instructions in `genie/`.

The script below only regenerates the configuration JSON; it does not create the space in Databricks:

```bash
python scripts/montar_genie_space.py
```

The original file numbering was preserved. Missing numbers therefore do not mean that files are missing from this package.

## Optional utilities

The scripts that access Databricks require the CLI to be installed and authenticated in your own workspace. The default profile is `alura-imersao`; set `DATABRICKS_CONFIG_PROFILE` to use another profile.

In PowerShell, query your Genie space as follows:

```powershell
$env:DATABRICKS_CONFIG_PROFILE = "alura-imersao"
$env:GENIE_SPACE_ID = "YOUR_SPACE_ID"
python scripts/perguntar_genie.py "Which airports have the highest concentration of delays?"
```

The `.sh` scripts require Bash, such as Git Bash or WSL. For `rodar_notebook.sh`, also set `DATABRICKS_WORKSPACE_PATH` to the notebooks folder in the workspace. The `rodar_pipeline.sh` script takes the pipeline ID as its first argument. Set the variables in the same terminal where the script will run.

## Validation and contributions

Compare Genie answers with the corresponding SQL queries. The numbers in `docs/teste-aceitacao.md` are records from the original material; they are not a validation of this environment and may change when the data is updated.

To contribute:

1. describe the change;
2. explain how you verified the result; and
3. open a pull request.

Do not include credentials or personal settings. When changing CSV files, record the source, period, and reason for the update so the exercises remain reproducible.

## Credits

- **Educational context:** Alura's Data Engineering Immersion, September 2026.
- **Data source:** ANAC, as documented in `docs/fontes.md`.
- **Base code:** preserved from the material shared during the immersion.
