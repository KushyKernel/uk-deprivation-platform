# UK Deprivation Platform

A data science platform that maps inequality across the UK by joining 12 free data sources at neighbourhood level, running ML and spatial models across all of them, and surfacing the results through a public Streamlit dashboard and API.

---

## What it does

Type any UK postcode, city, or county. Get back a full profile of that neighbourhood: deprivation rank, energy poverty risk, Bayesian crime forecast, health indicators, flood risk, house prices, green space coverage, and service accessibility — all in one place, all joined on LSOA code.

Under the hood it is a hybrid batch and real-time system. Slow-changing data (deprivation scores, census, EPC ratings, house prices, satellite imagery) is ingested on a schedule and stored in a feature store. Fast-changing data (flood warnings, recent crime) is fetched live at request time. Everything is joined on LSOA code.

---

## Why it exists

The UK government publishes a lot of open data. None of it talks to each other. A council trying to prioritise streets for a home insulation scheme has to pull from five different portals, clean everything manually, and hope someone can write the joins. This project does that work once, keeps it updated, and makes the results searchable by anyone via postcode, city, or county — with a free public API for developers and analysts.

---

## UK coverage

Each nation has its own deprivation index. All four are included.

| Nation | Index | Geography | Latest Release |
|---|---|---|---|
| England | Index of Multiple Deprivation (IMD) | LSOAs (33,755) | 2025 |
| Wales | Welsh IMD (WIMD) | LSOAs (1,917) | 2025 |
| Scotland | Scottish IMD (SIMD) | Data Zones (6,976) | 2020 |
| Northern Ireland | NI Multiple Deprivation Measure (NIMDM) | Super Output Areas (~890) | 2017 |

Cross-nation comparisons are done on normalised percentile rank, not raw scores, because each nation's methodology differs. The platform accepts postcode, city name, or county name as search input — all resolve to an LSOA code via Postcodes.io.

---

## IMD versions used

The platform uses the latest release as primary, joined to the previous two releases to enable trend analysis and the IMD trajectory forecast model.

### England

| Version | Boundaries | Status |
|---|---|---|
| IMD 2025 | 2021 LSOAs (33,755) | Primary — 55 indicators |
| IMD 2019 | Remapped to 2021 via ONS lookup | Trend comparison |
| IMD 2015 | Remapped to 2021 via ONS lookup | Trend comparison |

### Wales

| Version | Boundaries | Status |
|---|---|---|
| WIMD 2025 | 2021 LSOAs (1,917) | Primary |
| WIMD 2019 | 2011 LSOAs (1,909) — 8 boundary changes handled | Trend comparison |
| WIMD 2014 | 2011 LSOAs | Trend comparison |

### Scotland

| Version | Boundaries | Status |
|---|---|---|
| SIMD 2020 | 2011 Data Zones (6,976) | Primary — next release due late 2026 |
| SIMD 2016 | 2011 Data Zones — clean join | Trend comparison |
| SIMD 2012 | 2001 Data Zones — boundary lookup required | Trend comparison |

Versions are never joined on raw scores. Each release is normalised to percentile rank within that release before any cross-version comparison is made.

---

## Data sources

All free. One requires a free account.

| Domain | Source | Update cadence | Auth |
|---|---|---|---|
| Geography | [Postcodes.io](https://api.postcodes.io) | Live | None |
| Deprivation (England) | [MHCLG IMD 2025](https://www.gov.uk/government/statistics/english-indices-of-deprivation-2025) | Every 4-5 years | None |
| Deprivation (Wales) | [WIMD 2025](https://www.gov.wales/welsh-index-multiple-deprivation-2025) | Every 4-5 years | None |
| Deprivation (Scotland) | [SIMD 2020](https://www.gov.scot/collections/scottish-index-of-multiple-deprivation-2020) | Every 4-5 years | None |
| Crime | [data.police.uk](https://data.police.uk/api/) | Monthly + live | None |
| Energy (EPC) | [MHCLG EPC API](https://epc.opendatacommunities.org) | Quarterly | Free account |
| Health | [NHS Fingertips](https://fingertips.phe.org.uk/api/) | Monthly | None |
| Flooding | [EA Flood Monitoring](https://environment.data.gov.uk/flood-monitoring/) | Daily + live | None |
| Weather | [Open-Meteo](https://api.open-meteo.com) | Daily | None |
| House prices | [Land Registry Price Paid](https://www.gov.uk/government/statistical-data-sets/price-paid-data-downloads) | Annual | None |
| Demographics | [ONS Census 2021](https://api.beta.ons.gov.uk/v1/) | One-off + updates | None |
| Green space | [Copernicus Sentinel-2](https://dataspace.copernicus.eu) | Quarterly composite | None |
| Service access | [OpenStreetMap via OSMnx](https://osmnx.readthedocs.io) + NHS Digital | Annual | None |

---

## How the postcode join works

```
Postcode / City / County
        |
        v
Postcodes.io --> LSOA code + lat/lng
        |
        |-- LSOA code --> IMD, Census, NHS Fingertips, EPC, Land Registry,
        |                 NDVI (Sentinel-2), Spatial weights, Accessibility
        |
        +-- lat/lng   --> Police API, EA Flood API, Open-Meteo
```

LSOA code is the join key for everything. Postcodes.io resolves any UK postcode to one instantly. City and county inputs resolve to the most deprived LSOA in that area, with the option to explore the full local authority distribution.

---

## Models

| # | Model | Type | Question it answers |
|---|---|---|---|
| 1 | Area typology | Clustering (HDBSCAN + PCA + UMAP) | What kind of area is this? |
| 2 | Energy poverty risk | Classification (XGBoost + SHAP) | Is this area likely fuel poor? |
| 3 | Crime forecast | Bayesian nowcast (LightGBM prior + Gamma-Poisson update) | How many crimes next month, updated in real time? |
| 4 | Health vulnerability index | Dimensionality reduction (PCA) | How does health need compare nationally? |
| 5 | House price model | Regression (XGBoost + SHAP) | What drives prices here vs elsewhere? |
| 6 | Causal impact | Double ML (EconML / DoWhy) | Does deprivation actually cause worse outcomes? |
| 7 | IMD trajectory forecast | Classification (XGBoost + temporal CV) | Will this area improve, stay stable, or worsen by the next IMD release? |
| 8 | Green space index | Remote sensing (Sentinel-2 NDVI via Rasterio + GDAL) | How much vegetation coverage does this area have? |
| 9 | Spatial autocorrelation | Spatial statistics (PySAL — Moran's I + LISA) | Does deprivation cluster spatially, and where are the hot spots? |
| 10 | Service accessibility | Network analysis (OSMnx) | How far is this area from a GP, hospital, school, and green space? |

**Model 3 — Bayesian crime nowcast.** LightGBM produces a monthly prior forecast. As live Police API data arrives during the month, a Gamma-Poisson conjugate update revises the posterior. Output is a posterior mean with asymmetric credible intervals, updated in real time.

**Model 7 — IMD trajectory forecast.** Trained on rank changes between historical IMD releases, using changes in crime, house prices, EPC ratings, health indicators, NDVI, and economic activity as features. Temporal validation: train on 2015 to 2019 change, validate on 2019 to 2025 change. Output is a three-class prediction (improving / stable / worsening) with confidence score and SHAP top-3 drivers per LSOA.

**Model 9 — Spatial autocorrelation.** Uses a Queen contiguity spatial weights matrix across all LSOAs to compute Global Moran's I and Local Indicators of Spatial Association (LISA), identifying statistically significant hot spots, cold spots, and spatial outliers in the deprivation surface. LISA cluster type is also used as a feature in models 2 and 7.

---

## Example output

```
Search:          Liverpool

Postcode:        L1 1JG
LSOA:            E01006615 (Liverpool 045A)
IMD 2025 Rank:   312 / 33,755 -- top 0.9% most deprived nationally
LISA cluster:    High-High hot spot (p < 0.001)

Income deprivation:     91st percentile
Employment deprivation: 88th percentile
Health deprivation:     79th percentile
Education deprivation:  84th percentile

Energy poverty risk:    HIGH (87% confidence)
Flood risk:             MEDIUM
Crime forecast:         61 incidents next month (95% CI: 51-74)
IMD trajectory:         WORSENING (81% confidence)

NDVI green space:       0.18 (national median: 0.38 -- well below average)
Nearest GP:             0.3 km
Nearest hospital:       1.8 km
Nearest green space:    1.1 km

Median house price:     £142,000
National median:        £285,000
```

---

## Spatial data pipeline

The platform handles both vector and raster data across the full geospatial stack.

```
Vector pipeline:
  LSOA boundaries (GeoJSON / Shapefile)
      --> GeoPandas spatial joins
      --> Boundary harmonisation (2011 -> 2021 via ONS lookup tables)
      --> Spatial weights matrix (PySAL Queen contiguity)
      --> Spatial lag features added to ML feature store
      --> LISA cluster labels per LSOA

Raster pipeline:
  Sentinel-2 GeoTIFF (Band 4 red + Band 8 NIR)
      --> GDAL reproject to EPSG:27700 (British National Grid)
      --> Rasterio band arithmetic --> NDVI = (B8 - B4) / (B8 + B4)
      --> Zonal statistics (rasterstats) --> mean NDVI per LSOA polygon
      --> Written to feature store: ndvi_mean, ndvi_std, ndvi_low_pct

Network pipeline:
  OSMnx road network + NHS Digital service locations
      --> Graph-based routing from each LSOA centroid
      --> Nearest distance to GP, hospital, school, supermarket, green space
      --> Written to feature store: dist_gp_km, dist_hospital_km, etc.
```

---

## Stack

Python, Pandas, GeoPandas, Rasterio, GDAL, PySAL, OSMnx, rasterstats, XGBoost, LightGBM, scikit-learn, EconML, SHAP, scipy, MLflow, FastAPI, Streamlit, Folium, Plotly, APScheduler, DVC, Pytest

---

## Getting started

Requirements: Python 3.10+, Git, GDAL system library, free EPC API account from [epc.opendatacommunities.org](https://epc.opendatacommunities.org/login)

```bash
git clone https://github.com/KushyKernel/uk-deprivation-platform.git
cd uk-deprivation-platform
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env
```

Add your EPC credentials to `.env` then run the bulk ingestion:

```bash
python ingestion/download_bulk_data.py
```

Build the feature store (includes NDVI and accessibility):

```bash
python features/build_feature_store.py
```

Launch the dashboard:

```bash
streamlit run dashboard/app.py
```

Or start the API locally:

```bash
uvicorn api.main:app --reload
```

---

## API

The platform exposes a public REST API. All results are pre-computed from the feature store and served in under 100ms. Live flood and crime data is fetched at request time.

```
GET  /profile/{postcode}         full area profile
GET  /risk/energy/{postcode}     energy poverty risk score + SHAP drivers
GET  /forecast/crime/{lsoa}      Bayesian posterior forecast + credible interval
GET  /trajectory/{lsoa}          IMD trajectory prediction + SHAP drivers
GET  /spatial/hotspots           LISA hot spot map as GeoJSON
GET  /accessibility/{lsoa}       service distance profile
POST /bulk                       list of postcodes --> ranked CSV
GET  /compare/{lsoa}             peer LSOA comparison by similar IMD score
```

---

## Project structure

```
uk-deprivation-platform/
|
|-- data/
|   |-- raw/                  downloaded bulk datasets
|   |-- processed/            cleaned and validated
|   |-- feature_store/        one row per LSOA, ~80 features
|   |-- boundaries/           GeoJSON and shapefiles
|   +-- raster/               Sentinel-2 GeoTIFF tiles (quarterly composite)
|
|-- ingestion/
|   |-- postcodes.py
|   |-- crime.py
|   |-- epc.py
|   |-- health.py
|   |-- flood.py
|   |-- weather.py
|   |-- land_registry.py
|   |-- census.py
|   |-- sentinel2.py          Copernicus STAC download + cloud-free composite
|   |-- accessibility.py      OSMnx road network + NHS service locations
|   +-- imd/
|       |-- england.py
|       |-- wales.py
|       |-- scotland.py
|       +-- harmonise.py      boundary remapping to latest geography
|
|-- features/
|   |-- build_feature_store.py
|   |-- ndvi.py               NDVI per LSOA from Sentinel-2 raster
|   |-- spatial_weights.py    Queen contiguity matrix for all LSOAs
|   +-- accessibility.py      nearest service distances from LSOA centroids
|
|-- models/
|   |-- 1_clustering.py
|   |-- 2_energy_poverty.py
|   |-- 3_crime_forecast.py        LightGBM prior + Gamma-Poisson Bayesian update
|   |-- 4_health_index.py
|   |-- 5_house_price.py
|   |-- 6_causal_model.py
|   |-- 7_imd_trajectory.py
|   |-- 8_ndvi_index.py            raster-derived green space index
|   |-- 9_spatial_autocorrelation.py  Moran's I + LISA clusters
|   +-- model_cards/               one markdown card per model
|
|-- api/
|   +-- main.py               FastAPI app
|
|-- dashboard/
|   |-- app.py
|   +-- pages/                one file per dashboard page
|
|-- notebooks/
|   |-- 01_imd_eda.ipynb
|   |-- 02_crime_eda.ipynb
|   |-- 03_energy_eda.ipynb
|   |-- 04_ndvi_eda.ipynb          NDVI vs IMD decile, choropleth, green space gap
|   |-- 05_spatial_clusters.ipynb  Moran's I, LISA hot spot map, spatial lag effect
|   +-- 06_accessibility_eda.ipynb accessibility vs deprivation, service deserts
|
|-- tests/
|-- requirements.txt
|-- .env.example
+-- config.yaml
```

---

## Roadmap

### Phase 1 — Core data and models
- [x] Project design and data source verification
- [x] IMD version research and joinability assessment
- [ ] Boundary harmonisation (2011 to 2021 LSOA lookups for England and Wales)
- [ ] Ingestion pipelines for all 9 tabular sources
- [ ] IMD historical dataset joins (2015, 2019, 2025)
- [ ] LSOA feature store (tabular)
- [ ] EDA notebooks 01 to 03
- [ ] Model 1 — area typology clustering
- [ ] Model 2 — energy poverty classifier
- [ ] Model 3 — crime forecast (LightGBM batch, Bayesian upgrade in Phase 2)
- [ ] Model 4 — health vulnerability index
- [ ] Model 5 — house price regression
- [ ] Model 6 — causal impact (Double ML)
- [ ] Model 7 — IMD trajectory forecast

### Phase 2 — Geospatial additions
- [ ] Sentinel-2 ingestion pipeline (Copernicus STAC API, quarterly composite)
- [ ] NDVI raster processing (Rasterio + GDAL + zonal statistics)
- [ ] Model 8 — NDVI green space index added to feature store
- [ ] EDA notebook 04 — NDVI vs IMD decile and health outcomes
- [ ] Spatial weights matrix (PySAL Queen contiguity, all LSOAs)
- [ ] Model 9 — spatial autocorrelation (Global Moran's I + LISA clusters)
- [ ] EDA notebook 05 — spatial clusters, hot spots, spillover effects
- [ ] OSMnx accessibility pipeline (road network + service locations)
- [ ] Model 10 — service accessibility distances
- [ ] EDA notebook 06 — service deserts and deprivation
- [ ] Model 3 upgrade — Gamma-Poisson Bayesian nowcast with live Police API

### Phase 3 — Production
- [ ] FastAPI serving layer (all endpoints)
- [ ] Streamlit dashboard v1 (all pages)
- [ ] Model cards for all 10 models
- [ ] Tests and CI
- [ ] Public deployment

---

## Who uses it

Local councils, NHS teams, housing associations, researchers, journalists, and charities who need neighbourhood-level data without the data engineering overhead. The free public API makes it accessible to any developer or analyst working with UK geographies.

---

## Ethics

All data is open licence. EPC address-level data is used in aggregate only and never surfaced individually. Satellite imagery is processed to LSOA-level statistics only — no individual properties are identifiable. Models score areas, not people.

---

## Contributing

Issues and PRs welcome, especially from anyone with domain knowledge in public health, housing, local government, or geospatial data science.

```bash
git checkout -b feature/your-feature
git commit -m 'description'
git push origin feature/your-feature
```

Then open a pull request.

---

## Licence

MIT. Data sources are under Open Government Licence v3.0 (IMD, EPC, Land Registry, ONS, Police) and CC BY 4.0 (Open-Meteo). Sentinel-2 data is under the Copernicus Data Space open access licence. OpenStreetMap data is under ODbL.

---

## Acknowledgements

MHCLG, ONS, NHS England, Environment Agency, HM Land Registry, Postcodes.io, Open-Meteo, Copernicus / ESA, OpenStreetMap contributors
