# Tech Challenge 3 - PNAD COVID-19

Projeto da Fase 3 da pós-graduação em Data Analytics da FIAP. A entrega combina engenharia de dados no Google Cloud e análise exploratória da PNAD COVID-19, pesquisa domiciliar do IBGE. O relatório reúne os gráficos, e o dashboard permite explorar as respostas no Power BI. [Relatório em HTML](docs/relatorio_pnad_covid.html) · [Dashboard em Power BI](dashboard/analise_exploratoria.pbix)

## Objetivo

Investigar como sintomas, isolamento social, procura por atendimento, escolaridade e renda se distribuem entre os registros com resultado de teste em agosto, setembro e outubro de 2020. O notebook considera positivo o registro com pelo menos um resultado positivo nos exames de swab, sangue do dedo ou sangue da veia.

## Resultados

| Registros com resultado positivo | Agosto | Setembro | Outubro |
| --- | ---: | ---: | ---: |
| Mulheres | 4.229 | 5.276 | 6.062 |
| Homens | 3.410 | 4.243 | 4.889 |
| **Total** | **7.639** | **9.519** | **10.951** |

O número de respostas com algum exame positivo cresceu ao longo dos três meses. Os valores são contagens de entrevistas, sem aplicação dos pesos amostrais. A mesma pessoa pode aparecer em mais de um mês. O notebook também compara a frequência de sintomas e os níveis de isolamento entre os grupos com resultado positivo e negativo. [Notebook de análise com gráficos](notebooks/tech_challenge_3.ipynb)

## Tecnologias e estrutura

O pipeline usa Cloud Storage, PySpark no Dataproc e BigQuery. A análise usa Python, pandas e Matplotlib. O dashboard foi desenvolvido no Power BI Desktop.

| Arquivo | Conteúdo |
| --- | --- |
| [notebooks/tech_challenge_3.ipynb](notebooks/tech_challenge_3.ipynb) | Análise de sintomas, isolamento, atendimento, renda e escolaridade |
| [notebooks/pnad_covid_pipeline.ipynb](notebooks/pnad_covid_pipeline.ipynb) | Ingestão, tradução de códigos e carga das tabelas de pesquisa e dicionário |
| [dashboard/analise_exploratoria.pbix](dashboard/analise_exploratoria.pbix) | Dashboard com seis páginas e modelo de dados incorporado |
| [data/dicionario_selecionado.csv](data/dicionario_selecionado.csv) | Dicionário de variáveis e categorias recuperado do modelo da entrega |
| [docs/relatorio_pnad_covid.html](docs/relatorio_pnad_covid.html) | Relatório que pode ser baixado e aberto diretamente no navegador |
| [docs/arquitetura.png](docs/arquitetura.png) | Diagrama de arquitetura da entrega |

## Execução local

Para consultar o dashboard, baixe o arquivo PBIX e abra-o no Power BI Desktop. As visualizações usam as tabelas já carregadas. Para atualizar o modelo a partir de outro projeto, ajuste a origem das duas consultas no editor do Power Query e autentique-se no BigQuery.

Para reproduzir os gráficos em Python, use o modelo incorporado ao PBIX. Na raiz do repositório, execute em PowerShell com Python 3.12:

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m jupyterlab notebooks/tech_challenge_3.ipynb
```

No JupyterLab, execute as células em ordem. O notebook lê as colunas usadas na análise diretamente da tabela `f_pesquisa_covid` do dashboard.

## Reprodução do pipeline no Google Cloud

1. Crie um bucket e um dataset do BigQuery dedicados ao exercício. No ambiente Dataproc com Spark Connect e conector Spark–BigQuery, defina `PNAD_GCS_BUCKET` como o nome do bucket e `PNAD_BQ_DATASET` no formato `projeto.dataset`.
2. Descompacte os microdados mensais de agosto, setembro e outubro de 2020 e envie os três CSVs para `gs://SEU_BUCKET/pnad/raw/pesquisa/`. Envie o dicionário deste repositório para `gs://SEU_BUCKET/pnad/raw/dicionario/`. [Microdados mensais do IBGE](https://ftp.ibge.gov.br/Trabalho_e_Rendimento/Pesquisa_Nacional_por_Amostra_de_Domicilios_PNAD_COVID19/Microdados/Dados/)
3. Execute o notebook de pipeline em ordem com uma identidade que tenha acesso de leitura e escrita ao bucket e ao dataset. Ele substitui os dados de bronze e silver nos prefixos de saída e as tabelas `f_pesquisa_covid` e `d_dicionario_covid` no BigQuery.
4. Para executar a análise sobre sua tabela do BigQuery, instale `google-cloud-bigquery[pandas]` no ambiente local, configure Application Default Credentials e defina `PNAD_BQ_DATASET` antes de iniciar o JupyterLab.

## Autor

Luiz Carvalho · Pós-graduação em Data Analytics, FIAP. [GitHub](https://github.com/latcj)
