# UK Deprivation Platform
A data science platform that maps inequality across the UK by joining 9 free government data sources at neighbourhood level, running ML models across all of them, and surfacing the results through a public Streamlit dashboard.

---

## What it does

Type any UK postcode. Get back a full profile of that neighbourhood: deprivation rank, energy poverty risk, crime forecast, health indicators, flood risk, and house prices — all in one place.

Under the hood it is a hybrid batch and real-time system. Slow-changing data (deprivation scores, census, EPC ratings, house prices) is ingested on a schedule and stored in a feature store. Fast-changing data (flood warnings, recent crime) is fetched live at request time. Everything is joined on LSOA code.

---

## Why it exists

The UK government publishes a lot of open data. None of it talks to each other. A council trying to prioritise streets for a home insulation scheme has to pull from five different portals, clean everything manually, and hope someone can write the joins. This project does that work once, keeps it updated, and makes the results searchable by anyone.

---

## UK coverage

Each nation has its own deprivation index. All four are included.

| Nation | Index | Geography | Last Updated |
|---|---|---|---|
| England | Index of Multiple Deprivation (IMD) | LSOAs (~33,000) | 2019 |
| Scotland | Scottish IMD (SIMD) | Data Zones (~6,976) | 2020 |
| Wales | Welsh IMD (WIMD) | LSOAs (~1,909) | 2019 |
| Northern Ireland | NI Multiple Deprivation Measure (NIMDM) | Super Output Areas (~890) | 2017 |

All four use the same domain structure (income, employment, health, education, housing, crime, environment) so cross-nation comparisons are possible.

---

## Data sources

All free. One requires a free account.

| Domain | Source | Auth |
|---|---|---|
| Geography | [Postcodes.io](https://api.postcodes.io) | None |
| Deprivation | [MHCLG IMD](https://www.gov.uk/government/statistics/english-indices-of-deprivation-2019) | None |
| Crime | [data.police.uk](https://data.police.uk/api/) | None |
| Energy (EPC) | [MHCLG EPC API](https://epc.opendatacommunities.org) | Free account |
| Health | [NHS Fingertips](https://fingertips.phe.org.uk/api/) | None |
| Flooding | [EA Flood Monitoring](https://environment.data.gov.uk/flood-monitoring/) | None |
| Weather | [Open-Meteo](https://api.open-meteo.com) | None |
| House prices | [Land Registry Price Paid](https://www.gov.uk/government/statistical-data-sets/price-paid-data-downloads) | None |
| Demographics | [ONS Census 2021](https://api.beta.ons.gov.uk/v1/) | None |

---

## How the postcode join works

```
Postcode
    |
    v
Postcodes.io --> LSOA code + lat/lng
    |
    |-- LSOA code --> IMD, Census, NHS Fingertips, EPC, Land Registry
    |
    +-- lat/lng   --> Police API, EA Flood API, Open-Meteo
```

LSOA code is the join key for everything. Postcodes.io resolves any postcode to one instantly.

---

## Models

| Model | Type | Question it answers |
|---|---|---|
| Area typology | Clustering (HDBSCAN + PCA) | What kind of area is this? |
| Energy poverty risk | Classification (XGBoost + SHAP) | Is this area likely fuel poor? |
| Crime forecast | Time series (LightGBM) | How many crimes next month? |
| Health vulnerability index | Dimensionality reduction (PCA) | How does health need compare nationally? |
| House price model | Regression (XGBoost) | What drives prices here vs elsewhere? |
| Causal impact | Double ML (EconML) | Does deprivation actually cause worse outcomes? |

---

## Example output

```
Postcode:        E1 6RF
LSOA:            E01004204 (Tower Hamlets 023A)
IMD Rank:        423 / 32,844 -- top 1.3% most deprived nationally

Income deprivation:     82nd percentile
Employment deprivation: 78th percentile
Health deprivation:     65th percentile
Education deprivation:  80th percentile

Energy poverty risk:    HIGH (81% confidence)
Flood risk:             LOW
Crime forecast:         47 incidents next month (+/- 6)

Median house price:     485,000
National median:        285,000
```

---

## Stack

Python, Pandas, GeoPandas, XGBoost, LightGBM, scikit-learn, EconML, SHAP, MLflow, FastAPI, Streamlit, Folium, Plotly, APScheduler, DVC, Pytest

---

## Getting started

Requirements: Python 3.10+, Git, free EPC API account from [epc.opendatacommunities.org](https://epc.opendatacommunities.org/login)

```bash
git clone https://github.com/YOUR_USERNAME/uk-deprivation-platform.git
cd uk-deprivation-platform
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env
```

Add your EPC credentials to `.env` then run:

```bash
python ingestion/download_bulk_data.py
```

Launch the dashboard:

```bash
streamlit run dashboard/app.py
```

---

## Project structure

```
uk-deprivation-platform/
|
|-- data/
|   |-- raw/              downloaded bulk datasets
|   |-- processed/        cleaned and validated
|   |-- feature_store/    one row per LSOA, ~60 features
|   +-- boundaries/       GeoJSON for mapping
|
|-- ingestion/            one script per data source
|-- models/               one script per model + model cards
|-- dashboard/            Streamlit app and pages
|-- notebooks/            EDA and model development
|-- tests/
|-- requirements.txt
|-- .env.example
+-- config.yaml
```

---

## Roadmap

- [x] Project design and data source verification
- [ ] Ingestion pipelines for all 9 sources
- [ ] LSOA feature store
- [ ] EDA notebooks
- [ ] Clustering model
- [ ] Energy poverty classifier
- [ ] Crime forecasting model
- [ ] Causal model
- [ ] FastAPI serving layer
- [ ] Streamlit dashboard v1
- [ ] Model cards
- [ ] Tests and CI
- [ ] Public deployment

---

## Who uses it

Local councils, NHS teams, housing associations, researchers, journalists, and charities who need neighbourhood-level data without the data engineering overhead.

---

## Ethics

All data is open licence. EPC address-level data is used in aggregate only and never surfaced individually. Models score areas, not people.

---

## Contributing

Issues and PRs welcome, especially from anyone with domain knowledge in public health, housing, or local government.

```bash
git checkout -b feature/your-feature
git commit -m 'description'
git push origin feature/your-feature
```

Then open a pull request.

---

## Licence

MIT. Data sources are under Open Government Licence v3.0 (IMD, EPC, Land Registry, ONS, Police) and CC BY 4.0 (Open-Meteo).

---

## Acknowledgements

MHCLG, ONS, NHS England, Environment Agency, HM Land Registry, Postcodes.io, Open-Meteo
