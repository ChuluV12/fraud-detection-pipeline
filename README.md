# 🛡️ Real-Time Fraud Detection Pipeline

A production-style, end-to-end fraud detection system that processes a simulated live transaction stream, scores each transaction using machine learning, and visualises results on an interactive dashboard.

---

## 📸 Overview

This project simulates a real-world financial fraud detection pipeline — from raw transaction ingestion through ML scoring to a live monitoring dashboard — built entirely in Python.

```
Transaction Stream ──► PySpark Processing ──► ML Scoring ──► SQLite Storage ──► Streamlit Dashboard
       │                                            │
  (Simulated)                         Isolation Forest + Random Forest
```

---

## ✨ Key Features

| Feature | Detail |
|---|---|
| **Streaming simulation** | Generates 20 transactions/sec with realistic fraud patterns |
| **Velocity detection** | Flags users exceeding N transactions in a rolling time window |
| **Geographic anomaly** | Detects impossible travel between consecutive transactions |
| **ML scoring** | Isolation Forest (unsupervised) + Random Forest (supervised) ensemble |
| **Risk tiering** | Every transaction labelled LOW / MEDIUM / HIGH / CRITICAL |
| **Live dashboard** | Streamlit app with Plotly charts, exposed via ngrok tunnel |
| **Data export** | CSV + summary stats ready for Tableau or further analysis |

---

## 🛠️ Tech Stack

- **Python 3** — core language
- **PySpark** — distributed streaming processing layer
- **scikit-learn** — Isolation Forest & Random Forest models
- **SQLite** — lightweight persistence for flagged transactions
- **Streamlit** — interactive monitoring dashboard
- **Plotly / Matplotlib** — data visualisation
- **ngrok** — public tunnel for dashboard access from Colab
- **pandas / NumPy** — data manipulation

---

## 🚀 Getting Started

### Run in Google Colab (recommended)

1. Open the notebook in Colab:

   [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR-USERNAME/fraud-detection-pipeline/blob/main/Fraud_pipeline.ipynb)

2. Run **Cell 1** to install dependencies.

3. Run **Cells 2–9** in order to:
   - Import libraries and configure settings
   - Generate synthetic user profiles and training data
   - Train ML models
   - Start the transaction stream and processing pipeline

4. For the **live dashboard**:
   - Get a free ngrok token at [dashboard.ngrok.com](https://dashboard.ngrok.com/get-started/your-authtoken)
   - Paste your token into Cell 12 where indicated
   - Run Cells 10–12 to launch the Streamlit dashboard

5. Run **Cells 13–14** to export results and view visualisations.

### Run locally

```bash
git clone https://github.com/YOUR-USERNAME/fraud-detection-pipeline.git
cd fraud-detection-pipeline

pip install -r requirements.txt

jupyter notebook Fraud_pipeline.ipynb
```

> **Note:** PySpark requires Java 8+. Install with `sudo apt install default-jdk` on Linux or via [Adoptium](https://adoptium.net/) on Windows/Mac.

---

## 📁 Project Structure

```
fraud-detection-pipeline/
│
├── Fraud_pipeline.ipynb   # Main notebook — full pipeline end to end
├── requirements.txt        # Python dependencies
├── .gitignore              # Excludes secrets, DB files, build artifacts
└── README.md               # This file
```

---

## 🧠 How It Works

### 1. Transaction Simulation
`TransactionSimulator` generates a stream of synthetic transactions for 1,000 users. Each user has a spending profile (typical amount range, home location, active hours). Fraud is injected at ~10% across three patterns:
- **Velocity fraud** — burst of transactions in a short window
- **Amount anomaly** — transaction far above the user's normal range
- **Geographic anomaly** — transaction in a location inconsistent with recent history

### 2. Feature Engineering
Each transaction is enriched with:
- **Velocity score** — transaction count in the last N minutes for that user
- **Geo consistency score** — distance from the user's typical location
- **Time features** — hour of day, day of week (fraud has temporal patterns)
- **Merchant risk score** — categorical risk weighting by merchant type

### 3. ML Scoring
Two models are combined:
- **Isolation Forest** — unsupervised anomaly detection; no labels needed. Flags statistical outliers.
- **Random Forest** — supervised classifier trained on labelled synthetic data. Produces a calibrated fraud probability.

The final risk score is a weighted ensemble of both, mapped to LOW / MEDIUM / HIGH / CRITICAL tiers.

### 4. Storage & Dashboard
Flagged transactions (MEDIUM and above) are written to SQLite. The Streamlit dashboard reads from this database in near-real-time, displaying:
- Transaction volume over time
- Risk level breakdown
- Geographic heatmap of flagged transactions
- Top flagged users and merchants

---

## 📊 Sample Output

```
Risk Level Distribution:
  CRITICAL:  4.2%
  HIGH:      11.8%
  MEDIUM:    23.5%
  LOW:       60.5%

Total processed:  500 transactions
Total flagged:    197 transactions
Processing rate:  ~8.3 txns/sec
```

---

## ⚙️ Configuration

Key parameters in Cell 3 (`CONFIG` dict):

| Parameter | Default | Description |
|---|---|---|
| `simulation_speed` | `0.1s` | Delay between generated transactions |
| `anomaly_threshold` | `-0.3` | Isolation Forest decision boundary |
| `velocity_window_minutes` | `5` | Rolling window for velocity checks |
| `velocity_threshold` | `3` | Max transactions before velocity flag |
| `amount_threshold` | `$5,000` | Absolute high-amount flag |

---

## 🔒 Security Note

This notebook previously contained a hardcoded ngrok token which has been revoked. **Never commit API keys or tokens to a public repository.** Use environment variables or a `.env` file instead (already excluded in `.gitignore`).

---

## 📈 Potential Extensions

- Replace SQLite with PostgreSQL or Kafka for true production throughput
- Add a model retraining loop on confirmed fraud labels
- Deploy the Streamlit dashboard to Streamlit Cloud or AWS
- Add SHAP explainability so analysts can see why a transaction was flagged
- Integrate with a real payments API (Stripe test mode, etc.)

---

## 👤 Author

Built as a data engineering / ML portfolio project demonstrating:
real-time stream processing · anomaly detection · supervised classification · dashboard development
