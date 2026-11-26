# Tech Challenge 3 · Brazil’s PNAD COVID-19 household survey

How did people who reported positive and negative COVID-19 tests describe their symptoms, distancing, healthcare use and earnings? This project combines a Google Cloud data pipeline with an exploratory analysis of the household survey conducted by Brazil’s national statistics institute, IBGE. [Download the illustrated report](docs/pnad-covid19-report.html?raw=1) · [Download the Power BI dashboard](dashboard/pnad-covid19.pbix?raw=1)

## Findings

From August through October 2020, **111,496 interviews** reported a positive or negative swab, finger-prick or venous blood test. **28,109** reported at least one positive result.

| Interviews reporting a positive result | August | September | October |
| --- | ---: | ---: | ---: |
| Female | 4,229 | 5,276 | 6,062 |
| Male | 3,410 | 4,243 | 4,889 |
| **Total** | **7,639** | **9,519** | **10,951** |

These are unweighted interview counts. Respondents could appear in multiple months. The analysis also compares reported symptoms, healthcare use, distancing, earnings and education. Each chart explains its denominator. [Analysis notebook](notebooks/tech_challenge_3.ipynb)

## Explore the project

| File | What it contains |
| --- | --- |
| [Power BI dashboard](dashboard/pnad-covid19.pbix) | Six pages covering demographics, healthcare, household finances, testing, symptoms and interview months |
| [Survey snapshot](data/survey.parquet) | English analytical fields and responses for 2,650,459 interview records from May through November 2020 |
| [Survey dictionary](data/survey_dictionary.csv) | English questions and response labels with the original IBGE variable and category codes |
| [Data pipeline](notebooks/pnad_covid_pipeline.ipynb) | Spark transformations and BigQuery loading steps |
| [Architecture diagram](docs/architecture.png) | How the source files, cloud storage, processing and reporting fit together |

Open the downloaded dashboard in Power BI Desktop. Its imported tables work offline. Refresh uses the public English snapshot and dictionary from this repository. The original report-wide filters select August–October and a positive or negative **finger-prick** result. The notebook accepts a valid result from **any of the three tests**, so its count covers a broader group.

## Reproduce the charts locally

In an activated Python 3.12 environment, run the commands below from the repository root. Choose **Run → Run All Cells** in JupyterLab. The default input is the included Parquet snapshot.

```shell
python -m pip install -r requirements.txt
python -m jupyterlab notebooks/tech_challenge_3.ipynb
```

## Reproduce the Google Cloud pipeline

1. Create a dedicated Cloud Storage bucket and BigQuery dataset. In Dataproc Spark Connect with the Spark–BigQuery connector, set `PNAD_GCS_BUCKET` to the bucket name and `PNAD_BQ_DATASET` to `project.dataset`.
2. Extract the three monthly source CSVs for August–October 2020 and upload them to `gs://YOUR_BUCKET/pnad/raw/survey/`. Upload this repository’s English dictionary to `gs://YOUR_BUCKET/pnad/raw/dictionary/`. [IBGE monthly microdata](https://ftp.ibge.gov.br/Trabalho_e_Rendimento/Pesquisa_Nacional_por_Amostra_de_Domicilios_PNAD_COVID19/Microdados/Dados/)
3. Run the pipeline cells in order with a cloud identity that can read and write these destinations. The notebook replaces its bronze and silver outputs and the BigQuery tables `survey` and `survey_dictionary`.
4. To read your resulting BigQuery table from the analysis notebook, install `google-cloud-bigquery[pandas]`, configure Application Default Credentials and set `PNAD_BQ_DATASET` before starting JupyterLab.

Luiz Carvalho · FIAP postgraduate program in Data Analytics. [GitHub profile](https://github.com/latcj)
