#  Delivery ETA Prediction using Graph Analytics & Machine Learning

> Predicting delivery ETAs by modeling the logistics network itself — not just distance and route type.

[![Python](https://img.shields.io/badge/Python-3.x-blue.svg)](https://www.python.org/)
[![XGBoost](https://img.shields.io/badge/Model-XGBoost-orange.svg)](https://xgboost.readthedocs.io/)
[![NetworkX](https://img.shields.io/badge/Graph-NetworkX-green.svg)](https://networkx.org/)
[![License](https://img.shields.io/badge/License-MIT-lightgrey.svg)](#license)


##  Overview

Most ETA prediction systems treat delivery delays as noise driven purely by **distance** and **route type** — completely ignoring **where congestion actually happens in the network**. This project builds an ETA prediction pipeline that fuses **graph analytics** (hub centrality, corridor risk, bottleneck detection) with **machine learning (XGBoost)** to produce materially more accurate — and more explainable — delivery time estimates.

Instead of just answering *"how long will this delivery take?"*, the model also answers *"why is it going to take that long, and where in the network is the risk coming from?"*

##  Problem Statement

Poor ETA accuracy directly hurts logistics operations by:

- Lowering customer satisfaction and trust
- Increasing SLA breaches
- Making route planning reactive instead of proactive
- Hiding *where* in the network delays are actually originating

**Goal:** Model delivery delays using both route characteristics *and* graph-derived network intelligence, so that ETA errors — and the bottlenecks causing them — can both be reduced and explained.


##  Dataset

| Metric | Value |
|---|---|
| Raw shipment records | 144,867 |
| Unique trips | 14,817 |
| Source hubs | 1,508 |
| Destination hubs | 1,481 |

**Key attributes:** actual delivery time, OSRM-estimated travel time, distance metrics, route type, source/destination hubs, timestamps.

## Methodology

### 1. Data Processing
Data audit, missing-value analysis, OD (origin-destination) leg aggregation, and trip-level consolidation.

### 2. Network Construction
Built a **directed logistics graph**:
- **Nodes** = logistics hubs
- **Edges** = shipment corridors between hubs

| Network Stat | Value |
|---|---|
| Nodes | 1,590 |
| Edges | 2,508 |

### 3. Bottleneck Detection
Used **NetworkX** to compute:
- Degree Centrality
- Betweenness Centrality
- In-Degree / Out-Degree

Critical bottleneck hubs were flagged based on centrality scores and their contribution to SLA breaches.

### 4. Feature Engineering
- **Graph-derived:** source/destination hub centrality, corridor delay ratio, corridor traffic volume, network risk score
- **Temporal:** hour of day, day of week, month
- **Operational:** OSRM time, OSRM distance, actual distance, route type

### 5. Modeling
Trained an **XGBoost Regressor** in two configurations:

| Model | Features |
|---|---|
| **Baseline** | OSRM time, OSRM distance, distance to destination, route type |
| **Graph-Enhanced** | Baseline features + graph centrality + corridor risk + bottleneck indicators |


## 📈 Results

| Metric | Baseline | Graph-Enhanced | Improvement |
|---|---|---|---|
| MAE | 47.12 | **31.49** | ▼ 33.17% |
| RMSE | 108.40 | **90.92** | ▼ 16.13% |
| R² | 0.9297 | **0.9506** | ▲ |
| Accuracy @15% | — | **54.46%** | — |

**Takeaway:** Injecting network structure into the model cut prediction error by nearly a third — proof that *where* a hub sits in the network matters as much as raw distance.


## 💡 Key Insights

1. **Corridor delay ratio** emerged as one of the single most predictive features.
2. Graph-based bottleneck indicators meaningfully improved ETA accuracy over distance-only features.
3. A small set of **high-centrality hubs** disproportionately drive network-wide delays — the classic 80/20 bottleneck pattern.
4. Combining graph analytics with ML doesn't just improve accuracy — it makes delay *explainable*, not just predictable.

Each notebook builds on the previous — from raw data auditing, through graph construction and bottleneck analysis, to feature engineering, model training, comparison, and dashboard-ready output generation.


## 🛠️ Tech Stack

- **Language:** Python
- **Data:** Pandas, NumPy
- **Graph Analytics:** NetworkX
- **Modeling:** XGBoost, Scikit-Learn
- **Visualization:** Plotly, Streamlit

## How to Run

```bash
# Clone the repo
git clone https://github.com/<your-username>/delivery-eta-graph-ml.git
cd delivery-eta-graph-ml

# Install dependencies
pip install pandas numpy networkx xgboost scikit-learn plotly streamlit jupyter

# Run notebooks in order (01 → 09)
jupyter notebook notebooks/
```

---

## Strategy Memo

See [`Strategy_Memo.pdf`](./Strategy_Memo.pdf) for the business-facing writeup of findings, bottleneck recommendations, and next steps.

---

## Author

**Ayush Mishra**

---

