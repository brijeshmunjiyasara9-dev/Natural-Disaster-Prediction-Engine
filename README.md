# 🌍 Natural Disaster Prediction Engine

<div align="center">

![Version](https://img.shields.io/badge/version-2.0.0-blue?style=for-the-badge)
![License](https://img.shields.io/badge/license-MIT-green?style=for-the-badge)
![Node](https://img.shields.io/badge/Node.js-18+-339933?style=for-the-badge&logo=node.js&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.11--3.13-3776AB?style=for-the-badge&logo=python&logoColor=white)
![React](https://img.shields.io/badge/React-18-61DAFB?style=for-the-badge&logo=react&logoColor=black)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15+PostGIS-336791?style=for-the-badge&logo=postgresql&logoColor=white)

**An end-to-end autonomous, physics-grounded geospatial AI platform for flood & landslide susceptibility mapping, real-time dynamic risk prediction, and satellite-based crop intelligence — orchestrated by a 5-agent AI pipeline running 24/7.**

[📖 Documentation](#-documentation) · [🚀 Quick Start](#-quick-start) · [🏗️ Architecture](#️-system-architecture) · [📊 Modules](#-modules) · [🤖 Agentic AI](#-agentic-ai-pipeline)

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [System Architecture](#️-system-architecture)
- [Project Structure](#-project-structure)
- [Modules](#-modules)
  - [Module A — Susceptibility Mapping](#module-a--ahp-susceptibility-mapping)
  - [Module B — ERA5 Rainfall Engine](#module-b--era5-rainfall--weather-engine)
  - [Module C — Dynamic Risk Prediction](#module-c--dynamic-physics-based-risk-prediction)
  - [Module D — AgriIntel Crop Intelligence](#module-d--agriintel-v2--satellite-crop-intelligence)
- [Agentic AI Pipeline](#-agentic-ai-pipeline)
- [Tech Stack](#-tech-stack)
- [Database Schema](#️-database-schema)
- [Data Sources](#-data-sources)
- [Quick Start](#-quick-start)
- [Environment Variables](#-environment-variables)
- [API Reference](#-api-reference)
- [Deployment](#-deployment)
- [Troubleshooting](#-troubleshooting)

---

## 🎯 Overview

The **Natural Disaster Prediction Engine** is a full-stack geospatial AI system designed to provide early warning and risk intelligence for natural disasters — primarily floods and landslides — across India. It fuses multi-source satellite and weather data with physics-based hydrological models and autonomous AI agents to generate district-level risk maps and alerts without human intervention.

### ✨ Key Innovations

| Feature | Description |
|---------|-------------|
| **Terrain-Adaptive AHP** | 12 separate AHP weight matrices — one per terrain class — ensuring coastal plains, floodplains, mountains, and arid zones are treated physically accurately |
| **Physics Fusion Model** | TOPMODEL (Beven & Kirkby 1979) + Infinite-Slope FoS (Iverson 2000) + SCS-CN (TR-55 1986) combined into one dynamic risk engine |
| **5-Agent Autonomous Pipeline** | Orchestrator → Data Ingestion → GIS → ML → Alert agents running every 6 hours via n8n, zero human touch required |
| **Stage-wise Crop Risk** | Per-crop-stage weather risk with Key_Parameter 2.5× boost + consistency guards — unique in open-source agri tools |
| **Antecedent Precipitation** | 10-day API decay model (K=0.85, Kohler & Linsley 1951) for accurate soil moisture state before each prediction |
| **National India Coverage** | Full 4-level administrative hierarchy (State → District → Taluka → Village) loaded into PostGIS spatial tables |

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          CLIENT LAYER                                   │
│   ┌──────────────────────────────────────────────────────────────────┐  │
│   │  React 18 + Vite  │  Leaflet/MapLibre  │  Three.js 3D  │  GSAP  │  │
│   └──────────────────────────────────────────────────────────────────┘  │
└────────────────────────────┬────────────────────────────────────────────┘
                             │  REST API  +  WebSocket (ws://host/ws)
┌────────────────────────────▼────────────────────────────────────────────┐
│                        API GATEWAY LAYER                                │
│   ┌─────────────────────────────────────────────────────────────────┐   │
│   │  Node.js 18  +  Express 4.18  │  JWT Auth  │  WebSocket Server  │   │
│   │  12 Route Modules  │  Multer Upload  │  Job Queue Manager       │   │
│   └─────────────────────────────────────────────────────────────────┘   │
└──────────────────────────┬──────────────────────────────────────────────┘
                           │  HTTP POST  +  Agent Callbacks
┌──────────────────────────▼──────────────────────────────────────────────┐
│                        COMPUTE LAYER                                    │
│  ┌─────────────────────┐  ┌──────────────────┐  ┌───────────────────┐  │
│  │  Python FastAPI      │  │  Streamlit        │  │  n8n Orchestrator │  │
│  │  GIS Microservice   │  │  AgriIntel v2     │  │  Agent Pipeline   │  │
│  │  :8000              │  │  :8501            │  │  :5678            │  │
│  │  GDAL · WBT · CDSAPI│  │  XGBoost · NASA   │  │  CRON · Webhooks  │  │
│  └─────────────────────┘  └──────────────────┘  └───────────────────┘  │
└──────────────────────────┬──────────────────────────────────────────────┘
                           │  SQL + Spatial Writes
┌──────────────────────────▼──────────────────────────────────────────────┐
│                        STORAGE LAYER                                    │
│  ┌───────────────────────────┐   ┌────────────────────────────────────┐ │
│  │  PostgreSQL 15 + PostGIS  │   │  File System (DATA_ROOT)           │ │
│  │  Docker Container         │   │  GeoTIFF Rasters │ Shapefiles      │ │
│  │  12+ Spatial Tables       │   │  Output Maps    │ DEM Tiles        │ │
│  └───────────────────────────┘   └────────────────────────────────────┘ │
└──────────────────────────┬──────────────────────────────────────────────┘
                           │  External APIs
┌──────────────────────────▼──────────────────────────────────────────────┐
│                       EXTERNAL DATA LAYER                               │
│  Copernicus ERA5 (CDSAPI)  │  NASA POWER API  │  Google Earth Engine   │
│  SMTP / Slack Alerts       │  Sentinel-2 ESA  │  SRTM / CartoDEM DEMs  │
└─────────────────────────────────────────────────────────────────────────┘
```

### Data Flow — Flood Susceptibility Map (Example)

```
User clicks "Generate Map" in Frontend
        │
        ▼
Backend creates job (status: pending) → PostgreSQL
        │
        ▼
Backend triggers GIS Microservice via HTTP POST
        │
        ▼
GIS Microservice reads DEM, LULC, River, Soil from FILE SYSTEM
        │
        ▼
flood_susceptibility.py → terrain-specific AHP weighted combination
        │
        ▼
Output GeoTIFF + GeoJSON saved to database/output/
        │
        ▼
GIS updates job status → PostgreSQL (status: done)
        │
        ▼
Backend detects change → broadcasts via WebSocket
        │
        ▼
Frontend renders interactive risk map on Leaflet
```

---

## 📁 Project Structure

```
Natural-Disaster-Prediction-Engine/
│
├── 📁 frontend/                        # React 18 + Vite SPA
│   ├── src/
│   │   ├── pages/
│   │   │   ├── AuthPage.jsx            # Login / authentication
│   │   │   ├── ProjectHub.jsx          # 3D module cards landing page
│   │   │   ├── SusceptibilityModule.jsx # AHP map viewer
│   │   │   ├── RainfallModule.jsx      # ERA5 rainfall explorer
│   │   │   ├── DynamicModule.jsx       # Dynamic risk prediction
│   │   │   └── AdminDashboard.jsx      # 8-panel admin control center
│   │   ├── components/
│   │   │   ├── admin/                  # All admin panel components
│   │   │   │   ├── AgentPipeline.jsx   # Live agent status view
│   │   │   │   ├── DemUpload.jsx       # DEM upload + terrain pipeline
│   │   │   │   ├── SusceptibilityMapping.jsx
│   │   │   │   ├── DynamicMapping.jsx
│   │   │   │   ├── WeatherDownload.jsx
│   │   │   │   ├── ManualDataUpload.jsx
│   │   │   │   ├── DisasterManager.jsx
│   │   │   │   └── BoundaryImporter.jsx
│   │   │   └── three/
│   │   │       ├── ModuleCards3D.jsx   # Three.js 3D terrain/rain cards
│   │   │       └── TopographyBackground.jsx
│   │   ├── lib/api.js                  # Axios API client
│   │   ├── store/useStore.js           # Zustand global state
│   │   ├── App.jsx                     # Router + lazy loading
│   │   └── index.css                  # Global styles
│   ├── package.json
│   └── vite.config.js
│
├── 📁 backend/                         # Node.js + Express API server
│   ├── server.js                       # Main server + WebSocket setup
│   ├── config/db.js                    # PostgreSQL connection pool
│   ├── middleware/auth.js              # JWT protect middleware
│   ├── routes/
│   │   ├── auth.js                     # Login + JWT
│   │   ├── regions.js                  # Region CRUD + spatial
│   │   ├── upload.js                   # Multer file handler
│   │   ├── jobs.js                     # Job status + WS push
│   │   ├── susceptibility.js           # Trigger + fetch results
│   │   ├── rainfall.js                 # ERA5 timeseries
│   │   ├── boundaries.js               # India admin boundaries
│   │   ├── manual.js                   # LULC/River/Soil/Fault
│   │   ├── disasters.js                # Disaster types CRUD
│   │   ├── weather.js                  # Weather download
│   │   ├── dynamic.js                  # Dynamic risk trigger
│   │   └── agents.js                   # Agent run + log ingestion
│   └── db/
│       ├── migrate.js                  # Migration runner
│       └── migrations/
│           ├── 001_initial.sql         # Users, regions, jobs
│           ├── 002–009_*.sql           # Progressive schema additions
│           ├── 010_susceptibility_scripts.sql
│           └── 011_agent_tables.sql    # agent_runs + agent_logs
│
├── 📁 gis-service/                     # Python FastAPI GIS Microservice
│   ├── main.py                         # FastAPI app + router registration
│   ├── config.py                       # Pydantic settings
│   ├── requirements.txt
│   ├── routers/
│   │   ├── dem.py                      # DEM upload + terrain pipeline
│   │   ├── terrain.py                  # Terrain classification API
│   │   ├── susceptibility.py           # AHP map generation endpoint
│   │   ├── era5.py                     # ERA5 data import
│   │   ├── weather.py                  # Weather download endpoint
│   │   ├── india_layers.py             # India spatial layers
│   │   └── dynamic.py                  # Dynamic risk prediction API
│   ├── susceptibility_scripts/
│   │   ├── flood_susceptibility.py     # 8-layer AHP flood model
│   │   └── landslide_susceptibility.py # FoS + AHP landslide model
│   ├── scripts/
│   │   ├── dynamic_core.py             # Shared physics engine (TOPMODEL + SCS-CN)
│   │   ├── flood_dynamic_db.py         # Flood dynamic prediction runner
│   │   ├── landslide_dynamic_db.py     # Landslide dynamic prediction runner
│   │   ├── terrain_classifier.py       # 12-class terrain classification
│   │   ├── import_boundaries.py        # Shapefile → PostGIS importer
│   │   └── weather_downloader.py       # GEE weather task submitter
│   ├── agents/
│   │   ├── data_ingestion_agent.py     # ERA5 + MODIS auto-fetcher
│   │   └── gis_agent.py               # Terrain + susceptibility agent
│   └── utils/
│       ├── db_spatial.py               # PostGIS helper functions
│       └── topo_processor.py           # Topography processing utilities
│
├── 📁 Crop Prediction/                 # Streamlit AgriIntel v2
│   ├── main_app.py                     # 4-tab Streamlit app
│   ├── nn_classifier.py                # Nearest-Neighbour crop classifier
│   ├── xgb_crop_classifier.py          # XGBoost crop classifier
│   ├── xgb_engine.py                   # XGBoost + Monte Carlo yield engine
│   ├── xgb_crop_model.pkl              # Pre-trained XGBoost model
│   ├── pipeline/
│   │   ├── nasa_power.py               # NASA POWER API + stage aggregation
│   │   ├── sowing_harvest.py           # Stage duration + date estimation
│   │   ├── weather_risk.py             # Stage-wise risk scoring
│   │   └── yield_pipeline.py           # Yield forecast pipeline
│   ├── data_bank_ref.xlsx              # Stage-wise ideal weather + Key_Parameter
│   ├── training_features_pan_india.csv # Pan-India training features
│   └── requirements.txt
│
├── 📁 database/                        # Shared data root (DATA_ROOT)
│   └── output/                         # Generated GeoTIFF + GeoJSON maps
│
├── 📁 n8n/
│   └── workflow.json                   # n8n importable agent workflow
│
├── 📁 scripts/
│   ├── setup_n8n.bat                   # Windows n8n auto-setup
│   └── setup_n8n.sh                    # Linux/macOS n8n auto-setup
│
├── 📁 docs/
│   └── presentation/
│       └── index.html                  # Thesis presentation (light theme)
│
├── docker-compose.yml                  # PostgreSQL + PostGIS + n8n
├── .env.example                        # Root environment template
├── architecture.md                     # System architecture documentation
├── agentic_ai_architecture.md          # Agentic AI design documentation
├── setup.md                            # Detailed setup guide
└── README.md                           # This file
```

---

## 📊 Modules

### Module A — AHP Susceptibility Mapping

> **Route:** `/susceptibility` | **Tech:** Python FastAPI + GDAL + WhiteboxTools

Generates flood and landslide susceptibility GeoTIFF maps for any Indian district using Analytical Hierarchy Process (AHP) multi-criteria spatial analysis with terrain-adaptive weights.

#### How It Works

```
                    DEM (GeoTIFF)
                         │
                         ▼
              ┌─────────────────────┐
              │  WhiteboxTools      │
              │  Slope · Aspect     │
              │  TWI · Curvature    │
              │  Flow Accumulation  │
              └──────────┬──────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │  Terrain Classifier │
              │  12-Class Mapping   │
              │  (Mountain→Coastal) │
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │   AHP Layer Stack   │
              │  + LULC (Bhuvan)    │
              │  + River (HydroSHEDS│
              │  + Soil (NBSS&LUP)  │
              │  + Fault (GSI)      │
              └──────────┬──────────┘
                         │
                         ▼
              Terrain-Specific Weights
              (12 unique weight sets)
                         │
                         ▼
              ┌─────────────────────┐
              │  Output GeoTIFF     │
              │  5-Class Risk Map   │
              │  Very Low → Very    │
              │         High        │
              └─────────────────────┘
```

#### Terrain Classes & Flood Algorithm Assignment

| Class | Name | Flood Algorithm | Min Slope Cap |
|-------|------|----------------|---------------|
| 1 | Coastal Lowland | `coastal_inundation` | 3° |
| 2 | Floodplain | `inundation` | 5° |
| 3 | Alluvial Plain | `inundation` | 8° |
| 4 | Valley/River Basin | `channel_flood` | 15° |
| 5 | Piedmont/Foothill | `flash_flood` | 20° |
| 6 | Low Hill | `flash_flood` | 25° |
| 7 | High Hill | `flash_flood` | 35° |
| 8 | Mountain | `mountain_flood` | 60° |
| 9 | Plateau/Mesa | `plateau_pond` | 5° |
| 10 | Escarpment/Cliff | `flash_flood` | 70° |
| 11 | Arid Plain | `sheet_flood` | 10° |
| 12 | Coastal Dune | `coastal_inundation` | 10° |

#### Flood Susceptibility Layer Weights (Floodplain Class 2)

```
Flow Accumulation  ████████████████████████████████████ 35%
TWI (Wetness)      ██████████████████████████████ 30%
Elevation          ████████████████████ 20%
Soil Properties    ████████ 8%
River Distance     █████ 5%
LULC Roughness     █████ 5%
Drainage Density   ██ 2%
```

#### Landslide — Factor of Safety (Infinite Slope)

```
FoS = [C' + (γz·cos²α - u)·tan φ'] / (γz·sin α·cos α)

Where:
  C'  = Effective cohesion (kPa) — soil + root cohesion
  γz  = Unit weight × depth
  α   = Slope angle (from DEM)
  u   = Pore water pressure (from TWI)
  φ'  = Effective friction angle
```

---

### Module B — ERA5 Rainfall & Weather Engine

> **Route:** `/rainfall` | **Tech:** Python + CDSAPI + Google Earth Engine + PostGIS

Automated ingestion, processing, and visualisation of Copernicus ERA5 reanalysis weather data at district level with 24-hour temporal scrubbing.

#### ERA5 Parameters Fetched

| Parameter | ERA5 Variable | Unit | Usage |
|-----------|--------------|------|-------|
| Precipitation | `total_precipitation` | mm | Trigger scoring |
| Soil Moisture | `volumetric_soil_water_l1` | m³/m³ | AMC condition |
| Surface Runoff | `surface_runoff` | mm | SCS-CN input |
| Temperature | `2m_temperature` | K | Crop risk |
| Wind Speed | `10m_u/v_component_of_wind` | m/s | Evapotranspiration |
| Evaporation | `potential_evaporation` | m | Soil moisture budget |

#### Antecedent Precipitation Index (API)

```
APIₙ = P₀ + K·P₁ + K²·P₂ + ... + Kⁿ·Pₙ
     where K = 0.85  (Kohler & Linsley, 1951)

This 10-day weighted sum accurately represents
pre-event soil saturation state for dynamic modelling.
```

---

### Module C — Dynamic Physics-Based Risk Prediction

> **Route:** `/dynamic` | **Tech:** Python + Rasterio + NumPy + PostGIS

The most computationally intensive module — fusing susceptibility maps, real weather data, and LULC with three physical models to produce date-specific risk maps.

#### Dynamic Risk Formula

```
Dynamic Risk = w₁·Susceptibility + w₂·TriggerScore + w₃·LULCModifier

For Flood:     w₁=0.40, w₂=0.45, w₃=0.15
For Landslide: w₁=0.45, w₂=0.40, w₃=0.15
```

#### Trigger Score Computation

```python
# SCS-CN Curve Number Runoff
S = (25400 / CN) - 254           # Maximum retention (mm)
Q = (rain - 0.2·S)² / (rain + 0.8·S)  # Runoff depth (mm)

# TOPMODEL Wetness
TWI = ln(flow_accumulation / tan(slope))

# API Decay
API = Σ(Pₙ × 0.85ⁿ) for n = 0..10 days

# Combined Trigger
trigger_score = normalize(Q + TWI_weight·TWI + API_weight·API)
```

#### AMC Condition Classes

| Condition | API Threshold | CN Adjustment | Soil State |
|-----------|--------------|---------------|------------|
| AMC-I (Dry) | API < 35mm | CN reduced | Low saturation |
| AMC-II (Normal) | 35 ≤ API < 53mm | Standard CN | Average |
| AMC-III (Wet) | API ≥ 53mm | CN increased | High saturation |

---

### Module D — AgriIntel v2 — Satellite Crop Intelligence

> **Service:** Streamlit App `:8501` | **Tech:** XGBoost + Rasterio + NASA POWER API

A standalone satellite crop intelligence system that classifies crops from Sentinel-2 GeoTIFF uploads, estimates growth stages, and generates yield forecasts with stage-wise weather risk analysis.

#### Full Pipeline

```
                   Sentinel-2 GeoTIFF Upload
                            │
                            ▼
                    ┌───────────────┐
                    │  Band Check   │
                    │  ≥10 bands?   │
                    └───────┬───────┘
               No ◄─────────┤────────► Yes
           (Agri Map only)   │         (Full pipeline)
                            ▼
              ┌─────────────────────────┐
              │  18 Spectral Features   │
              │  NDVI, NDRE, EVI, SAVI  │
              │  CIre, RVI, NDWI, LSWI  │
              │  NDMI, BSI, NDBI, SWIR  │
              └────────────┬────────────┘
                           │
                    ┌──────▼──────┐
                    │  Classifier  │
                    │  NN or XGB  │
                    └──────┬──────┘
                           │  Per-pixel crop + growth stage
                           ▼
              ┌─────────────────────────┐
              │  Sowing/Harvest Est.    │
              │  data_bank_ref.xlsx     │
              │  Stage durations lookup │
              └────────────┬────────────┘
                           │
                    ┌──────▼──────────┐
                    │  NASA POWER API  │
                    │  7 daily params  │
                    │  sowing→harvest  │
                    └──────┬──────────┘
                           │
              ┌─────────────────────────┐
              │  Stage-wise Risk Score   │
              │  Key_Parameter × 2.5    │
              │  Consistency Guards     │
              └────────────┬────────────┘
                           │
              ┌─────────────────────────┐
              │  XGBoost Yield Forecast  │
              │  37 engineered features  │
              │  + Monte Carlo (1000x)  │
              └─────────────────────────┘
```

#### Stage-wise Risk Logic

```python
# Key_Parameter gets 2.5x weight boost
for stage in crop_stages:
    base_weight = 1.0 / num_params
    key_param_weight = base_weight * 2.5   # Boost
    other_weights = normalize_remaining(base_weight, key_param_weight)

# Consistency guards
if any_stage_extreme(stage_scores):
    final_score = max(final_score, 38)  # Floor to Moderate

if any_stage_category == "Extreme":
    final_risk = max(final_risk, "High")  # Floor to High
```

---

## 🤖 Agentic AI Pipeline

The v2.0 system introduces a **fully autonomous multi-agent architecture** — after one-time setup, it operates 24/7 without any human involvement.

### Agent Architecture

```
                      ┌─────────────────────┐
                      │  ⏰ CRON Trigger     │
                      │   Every 6 hours     │
                      └──────────┬──────────┘
                                 │
                      ┌──────────▼──────────┐
                      │  🧠 ORCHESTRATOR    │
                      │  Node.js State      │
                      │  Machine            │
                      │  + Retry Logic      │
                      └──┬──────────────────┘
                         │
          ┌──────────────▼────────────────┐
          │                               │
   ┌──────▼──────┐                ┌───────▼──────┐
   │ 📡 DATA     │                │ 🛰️ CROP      │
   │ INGESTION   │                │ AGENT        │
   │ ERA5+MODIS  │                │ (parallel)   │
   └──────┬──────┘                └───────┬──────┘
          │  DATA_READY                   │ CROP_READY
          ▼                               │
   ┌──────────────┐                       │
   │ 🗺️ GIS AGENT │◄──────────────────────┘
   │ Terrain +    │
   │ Suscept.    │
   └──────┬───────┘
          │  GIS_READY
          ▼
   ┌──────────────┐
   │ 🤖 ML AGENT  │
   │ Risk scoring │
   │ + forecasts  │
   └──────┬───────┘
          │  PREDICTIONS_READY
          ▼
   ┌──────────────┐
   │ 🚨 ALERT     │
   │ AGENT        │
   │ PDF+Email    │
   └──────────────┘
```

### Fault Tolerance Matrix

| Failure Scenario | Autonomous Response |
|-----------------|---------------------|
| Data fetch failure | Retry 3× with exponential backoff, skip region on final failure |
| GIS agent crash | Agent restarts, logs error, continues remaining regions |
| ML model divergence | Falls back to previous day's cached predictions |
| HIGH/CRITICAL risk detected | Auto-generates PDF + dispatches email/Slack alert |
| New region added to DB | Automatically included in next 6-hour pipeline run |
| Server restart | Orchestrator resumes from last `agent_logs` checkpoint |
| n8n workflow failure | Error Trigger node catches, notifies admin, queues retry |

### Standard Agent API Contract

Every agent exposes a uniform REST interface:

```http
POST /agents/{name}/run
Content-Type: application/json

{
  "run_id": "uuid-1234",
  "region": "Gujarat",
  "disaster_type": "flood",
  "data_date": "2026-05-07",
  "callback_url": "http://localhost:4000/api/agents/callback",
  "config": {}
}
```

```http
POST /agents/callback  (Agent → Orchestrator when complete)

{
  "run_id": "uuid-1234",
  "agent": "gis",
  "status": "success",
  "outputs": {
    "tif_path": "/database/output/flood_Gujarat_2026-05-07.tif",
    "job_id": 42
  },
  "duration_seconds": 95
}
```

---

## 🛠️ Tech Stack

### Frontend

| Package | Version | Purpose |
|---------|---------|---------|
| React | 18.3.1 | UI framework |
| Vite | 5.2.10 | Build tool + HMR |
| Three.js | 0.163.0 | 3D module cards |
| @react-three/fiber | 8.16.1 | React Three.js renderer |
| React-Leaflet | 4.2.1 | GIS maps |
| GSAP | 3.12.5 | Scroll + entrance animations |
| Framer Motion | 11.1.7 | Component transitions |
| Zustand | 4.5.2 | Global state management |
| React-Router-DOM | 6.23.1 | SPA routing |
| Axios | 1.6.8 | HTTP client |

### Backend (Node.js)

| Package | Version | Purpose |
|---------|---------|---------|
| Express | 4.18.3 | HTTP server + routing |
| ws | 8.16.0 | WebSocket server |
| jsonwebtoken | 9.0.2 | JWT auth |
| bcryptjs | 2.4.3 | Password hashing |
| pg | 8.11.3 | PostgreSQL client |
| multer | 1.4.5-lts.1 | File upload |
| nodemailer | 8.0.7 | Email alerts |
| node-cron | 4.2.1 | Scheduled tasks |
| axios | 1.15.2 | Inter-service HTTP |

### GIS Microservice (Python)

| Package | Version | Purpose |
|---------|---------|---------|
| FastAPI | 0.115.12 | Async API server |
| Uvicorn | 0.34.2 | ASGI server |
| Rasterio | 1.4.3 | GeoTIFF read/write |
| GDAL | latest | Raster/vector I/O |
| WhiteboxTools | 2.3.6 | Terrain analysis |
| GeoPandas | 1.0.1 | Vector spatial ops |
| NumPy | 2.2.5 | Raster computation |
| SciPy | 1.15.3 | Spatial interpolation |
| psycopg2-binary | 2.9.10 | PostgreSQL adapter |
| CDSAPI | 0.7.6 | ERA5 data fetcher |
| NetCDF4 | 1.7.2 | NetCDF file handling |

### AgriIntel (Streamlit)

| Package | Purpose |
|---------|---------|
| Streamlit ≥ 1.35 | Web UI framework |
| XGBoost ≥ 2.0 | Crop classifier + yield model |
| scikit-learn ≥ 1.3 | ML utilities |
| Rasterio ≥ 1.3 | Satellite TIFF processing |
| openpyxl ≥ 3.1 | data_bank_ref.xlsx reader |
| Pillow ≥ 10.0 | Image rendering |

### Infrastructure

| Component | Image / Version | Purpose |
|-----------|----------------|---------|
| PostgreSQL | postgis/postgis:15-3.3 | Relational + spatial DB |
| PostGIS | 3.3 | Spatial extension |
| n8n | n8nio/n8n (latest) | Workflow orchestration |
| Docker Compose | v2 | Container management |

---

## 🗄️ Database Schema

### Core Tables

```sql
-- Users & authentication
users (id UUID PK, email UNIQUE, password bcrypt, role admin|user)

-- 4-level India administrative hierarchy
regions (id UUID, country, state, district, centroid GEOMETRY, bbox GEOMETRY)

-- DEM + GIS processing jobs
jobs (id UUID, region_id FK, module, disaster_type, status, progress 0-100, log)

-- India spatial boundaries
india_states    (id, name, geom GEOMETRY(MultiPolygon, 4326))
india_districts (id, state_id FK, name, geom)
india_talukas   (id, district_id FK, name, geom)
india_villages  (id, taluka_id FK, name, geom)

-- Static GIS layers
india_rivers    (id, name, stream_order, geom GEOMETRY(MultiLineString))
india_soil      (id, soil_class INT, description, geom GEOMETRY(MultiPolygon))
india_faults    (id, name, geom GEOMETRY(MultiLineString))

-- Weather time-series (ERA5)
weather_data (grid_id, state, lat, lon, date, rain_mm, soil_moisture,
              surface_runoff_mm, temperature_2m_k, geom GEOMETRY(Point))

-- Susceptibility results
susceptibility_results (id UUID, region_id FK, disaster_type,
                        final_geojson JSONB, generated_at)

-- Dynamic risk results
dynamic_risk_results (id UUID, region_id FK, disaster_type, data_date,
                      risk_geojson JSONB, risk_stats JSONB)

-- Autonomous agent tracking
agent_runs (run_id UUID, triggered_at, completed_at, status, regions TEXT[])
agent_logs (id, run_id FK, agent_name, status, attempt_num, output_payload JSONB)
agent_alerts (id, run_id FK, region, disaster_type, risk_level, channels_notified)
```

---

## 📡 Data Sources

| Source | Type | Resolution | Access |
|--------|------|-----------|--------|
| **SRTM / ALOS / CartoDEM** | DEM Raster | 30m | Free download |
| **Copernicus ERA5** | Weather Reanalysis | 0.25° (~25km) | CDSAPI (free account) |
| **NASA POWER** | Daily Weather | 0.5° (~50km) | REST API (free) |
| **Sentinel-2 (ESA)** | Satellite Imagery | 10m | Google Earth Engine |
| **Bhuvan / NRSC** | LULC | 56m–30m | Free (India) |
| **NBSS&LUP / HWSD** | Soil | Variable | Free download |
| **GSI Fault Lines** | Geology Vector | 1:1M scale | Free (India) |
| **HydroSHEDS** | River Network | 90m | Free download |
| **Census 2011 Boundaries** | Admin Vectors | District/Taluka | Free (India) |

---

## 🚀 Quick Start

### Prerequisites

```bash
# Required
Docker Desktop (latest)    # PostgreSQL + n8n
Node.js v18 or v20 LTS     # Backend + Frontend
Python 3.11–3.13           # GIS Service + AgriIntel

# Accounts needed
Copernicus CDS Account     # ERA5 data (free at cds.climate.copernicus.eu)
```

### Step 1 — Clone & Configure

```bash
git clone https://github.com/brijeshmunjiyasara9-dev/Natural-Disaster-Prediction-Engine.git
cd Natural-Disaster-Prediction-Engine

# Copy all .env files
cp .env.example .env
cp backend/.env.example backend/.env
cp gis-service/.env.example gis-service/.env
```

### Step 2 — Start Infrastructure

```bash
# Start PostgreSQL + PostGIS + n8n
docker compose up -d

# Verify DB is ready
docker compose ps   # Should show 'healthy'
```

### Step 3 — Backend API

```bash
cd backend
npm install

# Run database migrations (first time only)
node db/migrate.js

# Start API server
npm run dev
# ✅ Running at http://localhost:4000
```

### Step 4 — GIS Microservice

```bash
cd gis-service
python -m venv venv

# Windows
.\venv\Scripts\activate

# macOS/Linux
source venv/bin/activate

pip install -r requirements.txt
uvicorn main:app --reload --port 8000
# ✅ Running at http://localhost:8000
```

### Step 5 — Frontend

```bash
cd frontend
npm install
npm run dev
# ✅ Running at http://localhost:3000 or :3001
```

### Step 6 — Activate n8n Agent Pipeline

```bash
# Windows
.\scripts\setup_n8n.bat

# macOS/Linux
bash scripts/setup_n8n.sh

# Open n8n dashboard
# http://localhost:5678
# → Find "Risk Prediction Engine Architecture" workflow
# → Toggle ACTIVE in top-right corner
```

### Step 7 — AgriIntel (Optional)

```bash
cd "Crop Prediction"
pip install -r requirements.txt
streamlit run main_app.py
# ✅ Running at http://localhost:8501
```

---

## 🔑 Environment Variables

### Root `.env`

```env
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=prediction_engine
POSTGRES_PORT=5432

# CRITICAL — absolute path to shared data directory
DATA_ROOT=./database
```

### `backend/.env`

```env
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/prediction_engine
JWT_SECRET=<generate: node -e "console.log(require('crypto').randomBytes(32).toString('hex'))">
PORT=4000
FRONTEND_URL=http://localhost:3000
DATA_ROOT=../database

# Email alerts (optional)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your@email.com
SMTP_PASS=your_app_password
```

### `gis-service/.env`

```env
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/prediction_engine
DATA_ROOT=../database
PORT=8000

# ERA5 data fetch
CDS_KEY=<your Copernicus CDS API key>
```

---

## 📡 API Reference

### Authentication

```http
POST /api/auth/login
{"email": "admin@example.com", "password": "password"}
→ {"token": "jwt...", "user": {"id", "email", "role"}}
```

### Regions

```http
GET  /api/regions              → List all regions
POST /api/regions              → Create region
GET  /api/regions/:id/boundary → GeoJSON boundary
```

### Susceptibility

```http
POST /api/susceptibility/generate
{
  "region_id": "uuid",
  "disaster_type": "flood",        // flood | landslide
  "weights": { "slope": 0.15, ... } // optional custom AHP weights
}
→ {"job_id": "uuid", "status": "pending"}

GET /api/susceptibility/:region_id/:disaster_type
→ {"geojson": {...}, "generated_at": "2026-05-07"}
```

### Dynamic Risk

```http
POST /api/dynamic/generate
{
  "region_id": "uuid",
  "disaster_type": "flood",
  "target_date": "2026-05-07",
  "antecedent_days": 10
}
→ {"job_id": "uuid"}

GET /api/dynamic/:region_id/:disaster_type/:date
→ {"risk_geojson": {...}, "risk_stats": {"high_pct": 12.4}}
```

### Weather

```http
GET /api/rainfall/:region_id?date=2026-05-07&hour=12
→ {"geojson": {...}, "max_rainfall_mm": 45.2}
```

### Agent Pipeline

```http
POST /api/agents/run
{"regions": ["Gujarat"], "data_date": "2026-05-07"}
→ {"run_id": "uuid", "message": "Agent pipeline initialized"}

GET /api/agents/status/:run_id
→ {"status": "running", "agents": [{...}]}

POST /api/agents/logs  (called by agents)
{"run_id", "agent_name", "status", "progress_pct", "current_step"}
```

### WebSocket

```javascript
// Subscribe to job updates
const ws = new WebSocket('ws://localhost:4000/ws?job=<job_id>');
ws.onmessage = (e) => {
  const { progress, step, status } = JSON.parse(e.data);
  // Update UI in real-time
};
```

---

## 🚢 Deployment

### Development (Local)

All services run locally as described in [Quick Start](#-quick-start).

### Production Considerations

```yaml
# docker-compose.prod.yml additions:

backend:
  environment:
    NODE_ENV: production
    JWT_SECRET: ${JWT_SECRET}   # Use secrets manager

gis-service:
  deploy:
    resources:
      limits:
        memory: 4G              # GIS processing is memory-intensive

nginx:
  image: nginx:alpine
  # Reverse proxy all services through port 80/443
  # SSL termination with Let's Encrypt
```

### Portability

Moving to a new machine:
1. Zip entire project folder (including `database/`)
2. Extract on new machine
3. Update `DATA_ROOT` in root `.env` to new absolute path
4. Run `docker compose up -d` and follow Steps 3–7

---

## 🛠️ Troubleshooting

| Issue | Solution |
|-------|----------|
| **"Pipeline Running" stuck** | Click **"Repair & Sync"** in Admin Dashboard |
| **CORS / Access Denied** | Check `backend/server.js` CORS origin list matches your frontend port |
| **n8n Status Offline** | Ensure Docker is running, re-run `setup_n8n.bat/.sh` |
| **No Active Regions Found** | Generate a Susceptibility Map for a region before running dynamic agent |
| **Python: Module Not Found** | Ensure you activated venv and ran `pip install -r requirements.txt` |
| **Port already in use** | Check for conflicting services on 5432, 4000, 8000, 3000, 5678 |
| **GIS job stuck at 0%** | Check `gis-service` terminal for Python errors; verify DATA_ROOT path |
| **ERA5 fetch fails** | Verify `CDS_KEY` in `gis-service/.env` and check Copernicus quota |

---

## 📚 References

- Beven, K.J. & Kirkby, M.J. (1979). *A physically based variable contributing area model of basin hydrology.* — TOPMODEL foundation
- Iverson, R.M. (2000). *Landslide triggering by rain infiltration.* Water Resources Research — Infinite-Slope FoS
- USDA-NRCS (1986). *Urban Hydrology for Small Watersheds. TR-55.* — SCS Curve Number method
- Skempton, A.W. & DeLory, F.A. (1957). *Stability of natural slopes in London Clay.* — Soil mechanics
- Kohler, M.A. & Linsley, R.K. (1951). *Predicting the runoff from storm rainfall.* — API decay constant K=0.85
- Saaty, T.L. (1980). *The Analytic Hierarchy Process.* McGraw-Hill — AHP multi-criteria method

---

## 📄 License

This project is licensed under the MIT License.

---

## 🙏 Acknowledgements

- **Copernicus Climate Change Service** — ERA5 reanalysis data
- **NASA POWER Project** — Daily agricultural meteorology
- **BISAG-N** — Geospatial technology guidance
- **NRSC Bhuvan** — India LULC data
- **NBSS&LUP** — National Bureau of Soil Survey and Land Use Planning
- **Geological Survey of India** — Fault line data

---

<div align="center">

**Built with ❤️ for disaster risk reduction across India**

*"The only required human action: one-time .env config. After that: 100% autonomous, 24/7."*

</div>
