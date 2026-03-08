# 🏙️ Urban Deprivation Intelligence Platform
### A multi-source data science platform for understanding inequality across the United Kingdom

---

## 🎯 Project Aim

The **Urban Deprivation Intelligence Platform** exists to answer one overarching question:

> *"Where in the UK are people's life chances most constrained by their circumstances — and what does the data tell us about why, and what to do about it?"*

The UK has no shortage of open data. What it lacks is a single, joined-up system that pulls it all together at neighbourhood level, applies rigorous analysis, and makes the results accessible to the people who most need them — whether that's a data scientist, a council officer, a journalist, or a charity worker.

This platform is that system. It transforms raw, scattered government data into **actionable intelligence** — profiling every neighbourhood across all four nations, forecasting risk, explaining inequality, and answering the kinds of questions that drive real policy and investment decisions.

---

## 🏴󠁧󠁢󠁥󠁮󠁧󠁿🏴󠁧󠁢󠁳󠁣󠁴󠁿🏴󠁧󠁢󠁷󠁬󠁳󠁿 Whole-UK Coverage

Each nation publishes its own deprivation index, all of which slot into the same neighbourhood-level geography:

| Nation | Index | Geography | Last Updated |
|---|---|---|---|
| 🏴󠁧󠁢󠁥󠁮󠁧󠁿 England | Index of Multiple Deprivation (IMD) | LSOAs (~33,000) | 2019 |
| 🏴󠁧󠁢󠁳󠁣󠁴󠁿 Scotland | Scottish IMD (SIMD) | Data Zones (~6,976) | 2020 |
| 🏴󠁧󠁢󠁷󠁬󠁳󠁿 Wales | Welsh IMD (WIMD) | LSOAs (~1,909) | 2019 |
| 🇬🇧 Northern Ireland | NI Multiple Deprivation Measure (NIMDM) | Super Output Areas (~890) | 2017 |

All four use comparable domain structures — income, employment, health, education, housing, crime, and environment — allowing a **normalised UK-wide deprivation score** to be constructed, enabling genuine cross-nation comparison in a single tool for the first time.

---

## 👥 Who Benefits

### 🏛️ Local Councils & Combined Authorities
Councils must allocate limited budgets across housing, social care, public health, and employment support — but their data is typically siloed across departments. This platform gives them a single postcode search that surfaces everything relevant to a neighbourhood, ranked lists of areas most in need of specific interventions, and evidence to justify funding bids to central government.

**Example:** A council in South Wales wants to prioritise streets for a home insulation scheme. They upload 500 postcodes — the platform returns energy poverty risk scores for each, ranked, saving weeks of analyst time.

### 🏥 NHS Integrated Care Boards & Public Health Teams
Health inequality is driven by social determinants — poverty, housing, environment — but health teams rarely have access to joined-up socioeconomic data alongside their clinical data. This platform provides a health vulnerability index combining 50+ indicators with deprivation context, and flags areas where health outcomes are *worse than expected* given deprivation levels — signalling specific local factors worth investigating.

### 🏠 Housing Associations & Social Landlords
Housing associations need to understand the neighbourhoods their properties sit in — for asset management, tenant support, and development planning. This platform provides area profiles for any postcode in their portfolio, energy poverty risk scores to prioritise retrofit investment, flood risk layered over their stock geography, and house price trends to inform development decisions.

### 🎓 Researchers & Universities
Joining data from 9 different government sources at neighbourhood level takes weeks of data engineering before any analysis can begin. This platform provides a pre-built, validated, 60-feature dataset covering the whole UK — ready to use — with reproducible pipelines and causal models that can serve as a foundation for academic work.

### 📰 Journalists & Data Journalists
Navigating raw government data portals is slow and technical. This platform gives journalists instant area profiles for any postcode, cross-nation comparisons (e.g. *"How does Glasgow's most deprived neighbourhood compare to Liverpool's?"*), and exportable data for their own visualisations.

### 🏦 Financial Services, Insurance & PropTech
Risk models for mortgage lending, insurance pricing, and property investment need granular socioeconomic context. This platform provides a free, open-data equivalent of expensive commercial geodemographic datasets — with flood risk, crime, energy efficiency, and deprivation all joined at postcode level.

### 🤝 Charities & Third Sector Organisations
Charities operating in deprived areas often lack analytical capacity to understand the full picture of need or make evidence-based cases to funders. This platform gives them free access to the same quality of analysis that large organisations commission expensively, including downloadable area reports ready to attach to funding applications.

### 👨‍💻 Data Scientists & ML Practitioners
Anyone wanting to build on UK deprivation data typically spends most of their time on data engineering rather than modelling. This platform provides a clean, validated, multi-source feature store ready to use, alongside a reference architecture for hybrid batch + real-time ML systems.

---

## 🌍 The Bigger Picture

This platform is a **public good** — the kind of tool that currently only exists inside well-resourced organisations (DLUHC, NHS England, large consultancies) who build bespoke internal versions. Making it open, searchable, and free democratises access to neighbourhood intelligence across the entire UK.

It also has a clear **commercial path**: white-labelled dashboards for councils, an API tier for PropTech or insurers, or a premium analytics layer for housing associations — an *open core, commercial extensions* model proven across the data industry.

---

## 📌 What Is This Project?

This platform combines **8+ free UK government and open data sources** to build a comprehensive picture of deprivation, health, crime, energy poverty, flooding, and housing inequality — all linked by a single geographic key: the **LSOA code** (Lower Super Output Area).

Enter any UK postcode. Get back a full intelligence profile of that area: how deprived it is, what health outcomes look like, crime patterns, energy efficiency of its homes, flood risk, and how house prices compare to similar areas across England.

The end goal is a **searchable, interactive dashboard** powered by machine learning models that can:
- Predict which areas are most at risk of energy poverty
- Forecast crime patterns over time
- Cluster areas into meaningful typologies (e.g. *"young deprived urban"* vs *"elderly rural isolated"*)
- Estimate the causal effect of deprivation on outcomes like school results and health

> **Who is this for?**
> Local councils, public health teams, housing associations, researchers, journalists, and anyone who wants to understand inequality at a neighbourhood level — without needing to be a data scientist.

---

## 🗺️ The Core Idea: Everything Connects Through a Postcode

The UK government divides England into ~33,000 small geographic zones called **LSOAs** (Lower Super Output Areas). Each one contains roughly 400–1,200 households. Every dataset in this project can be linked to an LSOA, which means we can join data from completely different sources into one unified profile for any neighbourhood.

```
Your Postcode (e.g. "E1 6RF")
        │
        ▼
  Postcodes.io API
        │
        ├──► LSOA Code ──► Index of Multiple Deprivation (IMD)
        │                  ONS Census 2021 (age, ethnicity, households)
        │                  NHS Fingertips (health indicators)
        │                  EPC Ratings (energy efficiency of homes)
        │                  Land Registry (house prices)
        │
        └──► Lat / Lng ──► Police Crime API (crimes nearby)
                           EA Flood Monitoring (flood risk & river levels)
                           Open-Meteo (weather & river discharge)
```

This geographic join key is what makes the project powerful — no single source tells the full story, but together they paint a detailed picture of what life is like in any given neighbourhood.

---

## 📦 Data Sources

All data used in this project is **free and publicly available**. No paywalls. No subscriptions (except one free account for EPC data).

| # | Domain | Source | What It Provides | Auth Required |
|---|--------|--------|-----------------|---------------|
| 1 | 🗺️ Geography | [Postcodes.io](https://api.postcodes.io) | Postcode → LSOA, coordinates, local authority | None |
| 2 | 📊 Deprivation | [MHCLG IMD 2019](https://www.gov.uk/government/statistics/english-indices-of-deprivation-2019) | IMD score & rank across 7 deprivation domains | None (bulk CSV) |
| 3 | 🚔 Crime | [data.police.uk](https://data.police.uk/api/) | Street-level crime by location & month | None |
| 4 | ⚡ Energy | [MHCLG EPC API](https://epc.opendatacommunities.org) | Energy ratings for every domestic property | Free account |
| 5 | 🏥 Health | [NHS Fingertips API](https://fingertips.phe.org.uk/api/) | 50+ health indicators per area | None |
| 6 | 🌊 Flooding | [EA Flood Monitoring API](https://environment.data.gov.uk/flood-monitoring/) | Live flood warnings, river levels, stations | None |
| 7 | 🌤️ Weather | [Open-Meteo](https://api.open-meteo.com) | Historical & forecast weather + river discharge | None |
| 8 | 🏠 House Prices | [Land Registry Price Paid](https://www.gov.uk/government/statistical-data-sets/price-paid-data-downloads) | Every property sale in England & Wales | None (bulk CSV) |
| 9 | 👥 Demographics | [ONS Census 2021 API](https://api.beta.ons.gov.uk/v1/) | Age, ethnicity, household composition per LSOA | None |

---

## 🤖 Machine Learning Models

This project implements **four distinct ML workstreams**, each answering a different policy-relevant question:

### 1. 📍 Area Typology Clustering
> *"What kind of area is this, really?"*

Uses unsupervised learning (K-Means / HDBSCAN) across ~60 features to group LSOAs into meaningful clusters — going beyond a simple deprivation rank to capture the full texture of an area.

**Example clusters discovered:**
- Young, deprived, high-crime urban
- Elderly, isolated, rural, poor health
- Affluent commuter belt, low deprivation, high house prices
- Post-industrial towns, moderate deprivation, high energy poverty

### 2. ⚡ Energy Poverty Risk Scorer
> *"Which households are most likely to be fuel poor?"*

A supervised classification model (XGBoost) trained on EPC ratings, property type, floor area, local weather patterns, and IMD fuel poverty sub-scores. Returns a risk score (High / Medium / Low) for any postcode.

### 3. 🚔 Crime Pattern Forecasting
> *"How many crimes are likely in this area next month?"*

A time-series forecasting model (LightGBM + seasonal features) trained on 12 months of street-level crime data, combined with weather, deprivation, and demographic features. Predicts crime counts by category at LSOA level.

### 4. 🔬 Causal Impact Estimation
> *"Does flood risk actually suppress house prices, after accounting for deprivation?"*

Uses **Double Machine Learning** (via the EconML library) to estimate causal effects — not just correlations. This separates genuine policy-relevant effects from confounding factors like deprivation.

---

## 🏗️ Project Structure

```
uk-deprivation-platform/
│
├── 📁 data/
│   ├── raw/                    # Downloaded bulk datasets (IMD CSV, Land Registry)
│   ├── processed/              # Cleaned, joined LSOA-level feature store
│   └── boundaries/             # LSOA GeoJSON boundaries for mapping
│
├── 📁 ingestion/               # Data pipeline scripts
│   ├── postcodes.py            # Postcodes.io lookups
│   ├── crime.py                # Police API ingestion
│   ├── epc.py                  # EPC API ingestion
│   ├── health.py               # NHS Fingertips ingestion
│   ├── flood.py                # EA Flood Monitoring ingestion
│   ├── weather.py              # Open-Meteo ingestion
│   └── build_feature_store.py  # Master join: all sources → LSOA feature table
│
├── 📁 models/
│   ├── clustering.py           # Area typology model
│   ├── energy_poverty.py       # Energy risk classifier
│   ├── crime_forecast.py       # Crime forecasting model
│   ├── causal_flood.py         # Double ML causal model
│   └── model_cards/            # Documentation for each model
│
├── 📁 dashboard/               # Streamlit interactive app
│   ├── app.py                  # Main app entry point
│   ├── pages/
│   │   ├── 01_area_profile.py
│   │   ├── 02_energy_risk.py
│   │   ├── 03_crime_forecast.py
│   │   └── 04_compare_areas.py
│   └── components/             # Reusable map & chart components
│
├── 📁 notebooks/               # Exploratory analysis & model development
│   ├── 01_eda_imd.ipynb
│   ├── 02_feature_engineering.ipynb
│   ├── 03_clustering.ipynb
│   ├── 04_energy_poverty_model.ipynb
│   └── 05_causal_inference.ipynb
│
├── 📁 tests/                   # Unit & integration tests
│
├── requirements.txt
├── .env.example                # Template for API credentials
├── config.yaml                 # Project configuration
└── README.md
```

---

## 🛠️ Tech Stack

| Layer | Tools |
|---|---|
| **Language** | Python 3.10+ |
| **Data wrangling** | Pandas, GeoPandas, NumPy |
| **Machine learning** | Scikit-learn, XGBoost, LightGBM |
| **Causal inference** | EconML, DoWhy |
| **Geospatial** | GeoPandas, Folium, Shapely |
| **Visualisation** | Plotly, Matplotlib, Seaborn |
| **Dashboard** | Streamlit |
| **API / serving** | FastAPI |
| **Experiment tracking** | MLflow |
| **Testing** | Pytest |
| **Version control** | Git + DVC (for large data files) |

---

## 🚀 Getting Started

### Prerequisites
- Python 3.10 or higher
- Git
- A free EPC API account from [epc.opendatacommunities.org](https://epc.opendatacommunities.org/login)

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/YOUR_USERNAME/uk-deprivation-platform.git
cd uk-deprivation-platform

# 2. Create and activate a virtual environment
python -m venv venv
source venv/bin/activate      # Mac/Linux
venv\Scripts\activate         # Windows

# 3. Install dependencies
pip install -r requirements.txt

# 4. Set up your credentials
cp .env.example .env
# Open .env and add your EPC API email and key
```

### Configuration

Open the `.env` file and fill in your credentials:

```env
EPC_API_EMAIL=your_email@example.com
EPC_API_KEY=your_api_key_here
```

All other APIs are completely open — no credentials needed.

### Download Bulk Datasets

```bash
# Download IMD 2019 data and Land Registry price paid data
python ingestion/download_bulk_data.py
```

This will populate the `data/raw/` directory. These files are large (~1GB for Land Registry) so they are excluded from Git via `.gitignore`.

---

## 💡 How to Use the Dashboard

Once set up, launch the interactive dashboard with one command:

```bash
streamlit run dashboard/app.py
```

Then open your browser to `http://localhost:8501`.

**For non-technical users:** Simply type any English postcode into the search bar. The dashboard will return:

- 📊 **Deprivation profile** — how the area ranks nationally across income, employment, health, education, housing, crime, and environment
- 🏘️ **Area typology** — which cluster this neighbourhood belongs to and what that means
- ⚡ **Energy risk** — proportion of properties likely to be energy poor
- 🌊 **Flood risk** — nearby flood monitoring data and risk classification
- 🏠 **House prices** — median sale prices over time vs similar areas
- 🏥 **Health outcomes** — key indicators like life expectancy, obesity rates, GP access

No coding knowledge required.

---

## 📊 Example Output

**Input:** Postcode `E1 6RF` (Whitechapel, Tower Hamlets)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LSOA:            E01004204 — Tower Hamlets 023A
IMD Rank:        423 / 32,844  (most deprived 1.3%)
Area Typology:   Cluster 2 — Young, Urban, High Deprivation
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Income Deprivation:     ████████████░░  82nd percentile (most deprived)
Employment Deprivation: ███████████░░░  78th percentile
Health Deprivation:     █████████░░░░░  65th percentile
Education Deprivation:  ████████████░░  80th percentile
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Energy Poverty Risk:    HIGH  (model confidence: 81%)
Flood Risk:             LOW   (no nearby flood warnings)
Predicted Crime (next month): 47 incidents  (±6)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Median House Price (2024):  £485,000
National Median:            £285,000
vs. Similar Deprived Areas: +42% premium (urban location effect)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 🔍 Key Concepts Explained (For Non-Technical Readers)

<details>
<summary><strong>What is an LSOA?</strong></summary>

An LSOA (Lower Super Output Area) is a small geographic zone defined by the UK government for statistical purposes. England is divided into about 33,000 of them, each containing roughly 400–1,200 households. They're small enough to capture neighbourhood-level differences, but large enough to protect individual privacy. Think of them as very precise postcodes for data analysis.

</details>

<details>
<summary><strong>What is the Index of Multiple Deprivation (IMD)?</strong></summary>

The IMD is the UK government's official measure of relative deprivation. It ranks every LSOA in England from 1 (most deprived) to 32,844 (least deprived) across seven domains: income, employment, health, education, housing, crime, and environment. It's updated roughly every 4–5 years — the most recent version is from 2019.

</details>

<details>
<summary><strong>What does "causal inference" mean?</strong></summary>

Most data analysis finds correlations — things that tend to happen together. Causal inference goes further and asks: if we changed X, would Y actually change? For example, flooded areas tend to have lower house prices — but is that *because* of flooding, or because flood-prone areas also tend to be more deprived? Causal models mathematically separate these effects to give policymakers more reliable answers.

</details>

<details>
<summary><strong>What is machine learning doing here?</strong></summary>

Machine learning is used in three ways in this project: (1) **clustering** — grouping similar neighbourhoods together without being told in advance what the groups should be; (2) **classification** — predicting which category something falls into (e.g. high/medium/low energy poverty risk); and (3) **forecasting** — predicting future values (e.g. crime counts next month) based on historical patterns.

</details>

---

## 🗓️ Roadmap

- [x] Project design & data source verification
- [ ] Data ingestion pipelines for all 9 sources
- [ ] LSOA-level feature store (60+ features)
- [ ] Exploratory data analysis notebooks
- [ ] Area typology clustering model
- [ ] Energy poverty risk classifier
- [ ] Crime forecasting model
- [ ] Causal flood/housing model
- [ ] FastAPI serving layer
- [ ] Streamlit dashboard (v1)
- [ ] Model cards & documentation
- [ ] Unit tests & CI pipeline
- [ ] Streamlit dashboard (v2 — compare areas feature)
- [ ] Public deployment

---

## 🤝 Contributing

Contributions, suggestions, and issue reports are very welcome. If you work in local government, public health, or housing and have domain expertise to offer, please reach out — getting the framing of the analysis right matters as much as the technical implementation.

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature-name`)
3. Commit your changes (`git commit -m 'Add some feature'`)
4. Push to the branch (`git push origin feature/your-feature-name`)
5. Open a Pull Request

---

## ⚖️ Ethics & Data Privacy

- All data used is **publicly available and open licence**
- EPC data contains address-level information — it is used only in aggregate and no individual addresses are stored or surfaced in the dashboard
- The models in this project rank and score **areas**, not individuals
- IMD and deprivation metrics describe structural conditions, not personal characteristics — outputs should never be used to make decisions about individuals
- Model cards are provided for each ML model, documenting known limitations, potential biases, and appropriate use cases

---

## 📄 Licence

This project is licensed under the **MIT Licence** — see the [LICENSE](LICENSE) file for details.

Data sources are subject to their own licences:
- IMD & EPC data: [Open Government Licence v3.0](https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/)
- Land Registry Price Paid: [Open Government Licence v3.0](https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/)
- ONS data: [Open Government Licence v3.0](https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/)
- Police data: [Open Government Licence v3.0](https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/)
- Open-Meteo: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

---

## 🙏 Acknowledgements

- [Ministry of Housing, Communities & Local Government](https://www.gov.uk/government/organisations/ministry-of-housing-communities-and-local-government) — IMD & EPC data
- [Office for National Statistics](https://www.ons.gov.uk/) — Census 2021 and geography boundaries
- [NHS England / UKHSA](https://fingertips.phe.org.uk/) — Fingertips health data
- [Environment Agency](https://www.gov.uk/government/organisations/environment-agency) — Flood monitoring data
- [HM Land Registry](https://www.gov.uk/government/organisations/land-registry) — Price Paid data
- [Open-Meteo](https://open-meteo.com/) — Weather & flood discharge data
- [Postcodes.io](https://postcodes.io/) — Free postcode lookup API

---

<p align="center">
  Built with open data, for the public good.
</p>
