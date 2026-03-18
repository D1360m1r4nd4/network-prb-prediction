# 📡 Mobile Network PRB Utilization Prediction

> End-to-end data science project on a synthetic 5G/LTE Radio Access Network dataset —  
> covering data quality validation, KPI engineering, exploratory analysis, and machine learning.

---

## 🎯 Objective

Predict **Downlink PRB (Physical Resource Block) Utilization** — a core capacity KPI in mobile networks that determines whether a cell is congested or has available headroom for new traffic.

---

## 📊 Dataset

| Property | Value |
|---|---|
| Records | 28,440 hourly cell-level observations |
| Sites | 200 (Macro & Indoor) |
| Technologies | LTE & 5G NR |
| Features | 51 counters/KPIs |
| Target | `dl_prb_utilization` (continuous, 0–100%) |

The dataset is **synthetic** but modeled on real-world RAN KPI distributions. It includes realistic patterns such as peak-hour demand cycles, interference events, massive events (e.g. concerts/stadiums), and cell outages.

---

## 🗂️ Project Structure

```
network-prb-prediction/
├── README.md
├── requirements.txt
├── data/
│   └── network_performance_data.csv
├── notebooks/
│   └── 04_ml_prb_prediction.ipynb
├── images/
│   ├── 3_1_Hourly_Plots.png
│   ├── 3_2_1_1_Throughput_LTE_vs_NR.png
│   ├── 3_2_1_2_Throughput_LTE_vs_NR_-_DL_UL_breakdown.png
│   ├── 3_2_2_ERAB_SSR_LTE_vs_NR.png
│   ├── 3_3_RSRP_Distribution_-_Macro_vs_Indoor.png
│   ├── 3_4_1_Correlation_Heatmap.png
│   ├── 3_4_2_Correlation_Heatmap_-_KPI_subset.png
│   └── 3_4_3_Correlation_Cluster_Heatmap_-_KPI_subset.png
└── reports/
    └── Final_Report.pdf
```

---

## 🔬 Methodology

### Exercise 1 — Data Loading & Quality Validation
- Loaded 28,440 records with 51 columns
- Programmatic checks for nulls, negative values, and out-of-range rates/percentages
- Result: **clean dataset**, no imputation required

### Exercise 2 — KPI Engineering & Aggregation
Derived the three core RAN quality pillars:
- **Accessibility**: E-RAB Setup Success Rate
- **Retainability**: E-RAB Drop Rate
- **Mobility**: Combined Handover Success Rate (intra + inter frequency + inter-RAT)
- **Efficiency**: `traffic_per_user` = traffic volume normalized by active users

Aggregated views at per-site and per-hour granularity for trend analysis.

### Exercise 3 — Exploratory Data Analysis
Key findings from visualizations:
- **Peak hours** (09:00–20:00) show ~50% higher PRB utilization than off-peak
- **5G NR** delivers ~3× the median throughput of LTE at comparable PRB loads
- **Indoor cells** have significantly better RSRP (median ~-85 dBm vs ~-95 dBm for Macro)
- **Interference** is the primary driver of ERAB drop rate (correlation = 0.72)
- `avg_active_users` and `dl_prb_utilization` are strongly correlated (r = 0.80)

### Exercise 4 — Machine Learning: PRB Prediction

Three model iterations, each building on the previous:

| Model | Key Change | R² |
|---|---|---|
| Linear Regression | Baseline | ~0.46 |
| Random Forest | `hour` as categorical + traffic volume feature | ~0.70 |
| **HistGradientBoosting** | Outage filtering + engineered features | **~0.75** |

**Final model features:** `hour` (categorical), `site_type`, `technology`, `band`, `avg_active_users`, `avg_rsrp_dbm`, `avg_sinr_db`, `dl_traffic_volume_gb`, `massive_event`, `load_balancing_active`, `traffic_per_user`, `traffic_sinr_ratio`

**Encoding approach:** Compared `pd.get_dummies()` vs scikit-learn `OneHotEncoder` within a `Pipeline` — highlighting the importance of fit/transform separation to prevent data leakage.

---

## 📈 Key Results

- ✅ **R² = 0.75** on held-out test set (20% split, random_state=10)
- ✅ Outage rows filtered before training to eliminate distorted baselines
- ✅ Engineered features (`traffic_per_user`, `traffic_sinr_ratio`) improved R² by ~5 points over raw features
- ✅ `massive_event` and `load_balancing_active` flags proved critical — their inclusion prevents systematic prediction errors during anomalous network states

---

## 🛠️ Tech Stack

- **Python 3.x**
- `pandas`, `numpy` — data manipulation
- `matplotlib`, `seaborn` — visualization
- `scikit-learn` — ML pipeline, encoding, modeling, evaluation
- `HistGradientBoostingRegressor` — final model (handles categorical natively, robust to outliers)

---

## ⚙️ Setup & Usage

```bash
# Clone the repo
git clone https://github.com/your-username/network-prb-prediction.git
cd network-prb-prediction

# Create virtual environment
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Launch notebook
jupyter notebook notebooks/04_ml_prb_prediction.ipynb
```

---

## 🔭 Potential Extensions

- **Higher temporal resolution** (15-min bins) to reduce unexplained variance (~25%)
- **Anomaly detection** to flag cells behaving outside their predicted envelope
- **Streamlit dashboard** for interactive KPI exploration by site or technology
- **Model serving** via `joblib.dump()` + REST API for real-time inference on live network counters

---

## 📄 License

This project is for portfolio and educational purposes. The dataset is fully synthetic.
