# Trader Performance vs Market Sentiment — Primetrade.ai Assignment

## Objective
Analyze how Bitcoin market sentiment (Fear/Greed Index) relates to trader behavior and performance on Hyperliquid. Uncover patterns that could inform smarter trading strategies.

## Setup & How to Run

### Prerequisites
- Python 3.10+

### Installation
```bash
python -m venv venv

# Windows
.\venv\Scripts\activate

# Linux/Mac
source venv/bin/activate

pip install -r requirements.txt
```

### Download Data
The datasets must be placed in the `data/` folder:
- `data/sentiment_data.csv` — Bitcoin Fear & Greed Index
- `data/trader_data.csv` — Hyperliquid Trader Data

If not present, the trader data can be downloaded via:
```bash
python -c "import gdown; gdown.download('https://drive.google.com/uc?id=1IAfLZwu6rJzyWKgBToqwSmmVYU6VbjVs', 'data/trader_data.csv', quiet=False)"
```

### Run Analysis
```bash
python analysis.py
```

All output charts and CSV tables will be saved to the `output/` folder.

### Jupyter Notebook
Open and run all cells in `analysis_notebook.ipynb` for an interactive walkthrough:
```bash
jupyter notebook analysis_notebook.ipynb
```

### Streamlit Dashboard (Bonus)
Launch the interactive dashboard:
```bash
streamlit run app.py
```
The dashboard provides 4 tabs: Performance by Sentiment, Behavioral Patterns, Trader Explorer, and Clustering — with sidebar filters for sentiment regime, date range, and trader selection.

---

## Datasets

| Dataset | Rows | Columns | Date Range |
|---------|------|---------|------------|
| Sentiment (Fear/Greed) | 2,644 | 4 | 2018-02-01 to 2025-05-02 |
| Trader Data (Hyperliquid) | 211,224 | 16 | 2023-05-01 to 2025-05-01 |

- **Overlapping dates**: 479 days
- **Unique traders**: 32 accounts
- **Zero missing values** in both datasets, zero duplicates

---

## Methodology

### Part A — Data Preparation
1. Loaded and inspected both datasets (shape, dtypes, nulls, duplicates)
2. Parsed timestamps (`Timestamp IST` → datetime), extracted date for merge
3. Merged on `date` (inner join) → 211,218 matched rows across 479 days
4. Created sentiment binary grouping: Fear (incl. Extreme Fear), Neutral, Greed (incl. Extreme Greed)
5. Computed key metrics:
   - **Daily PnL** per trader and aggregate
   - **Win rate** (% of closing trades with positive PnL)
   - **Long/Short ratio** (directional bias)
   - **Trade frequency**, average trade size, total volume

### Part B — Analysis
- Mann-Whitney U tests for Fear vs Greed comparisons (non-parametric)
- Segmented traders into 3 dimensions:
  - **Frequency**: Frequent vs Infrequent (median split)
  - **Performance**: Consistent Winner / Inconsistent Winner / Net Loser
  - **Trade Size**: High Size vs Low Size (median split)
- Cross-tabulated segment performance by sentiment regime

### Part C — Actionable Strategies
- Derived 2 strategy rules from quantitative evidence

### Bonus
- **Predictive Model**: Random Forest + Gradient Boosting for next-day profitability bucket
- **Clustering**: K-Means (k=3) to identify trader behavioral archetypes

---

## Key Insights

### Insight 1: Fear Days Are Surprisingly More Profitable (But Riskier)
- Average daily PnL on Fear days ($39,012) is **2.5x higher** than Greed days ($15,848)
- However, worst single-day drawdown occurs during Greed (-$419,020 vs -$122,672 on Fear)
- Interpretation: Fear creates buying opportunities for skilled traders; Greed leads to complacency

### Insight 2: Traders Show Strong Contrarian Behavior During Fear
- Trade frequency is **~2.7x higher** on Fear days (793 vs 294 trades/day, p=0.0042)
- Long/Short ratio spikes to **3.67** during Fear (vs 1.98 Greed, p<0.0001)
- Volume is 4.2x higher on Fear days — traders aggressively buy the dip
- This is statistically significant contrarian positioning

### Insight 3: Net Losers Get Crushed During Greed — Winners Stay Consistent
- Consistent winners earn positive PnL across **all** sentiment regimes
- Net Losers average **-$8,654/day on Greed** (their worst regime) — they chase rallies poorly
- Frequent traders outperform on Fear; Infrequent traders do better on Greed
- High-size traders benefit most from Fear ($7,620 avg vs $3,355 for low-size)

---

## Strategy Recommendations

### Strategy 1: Sentiment-Adaptive Risk Management for Underperformers
> "During Greed days, traders with win rates <75% should reduce position size by 50% or pause. Net Losers lose -$8,654/day during Greed — they chase momentum poorly."

### Strategy 2: Contrarian Long Bias During Fear for Active Traders
> "During Fear days (index <25), allocate 20-30% more capital to long positions. Fear-day PnL is 2.5x Greed-day PnL. The best traders already do this — L/S ratio reaches 3.67 during Fear."

---

## Bonus Results

### Predictive Model
- **Random Forest**: 87.8% accuracy on next-day profitability (Profit/Loss)
- **Gradient Boosting**: 84.2% accuracy
- Top features: avg trade size, lagged sentiment, rolling PnL, lagged PnL

### PnL Volatility Prediction
- **Random Forest Regressor** predicts next-day PnL volatility from sentiment features
- Evaluates correlation between rolling market fear and trader risk

### Trader Clustering
- K-Means (k=3) identified behavioral archetypes across the 32 traders
- Silhouette analysis for optimal cluster count
- PCA and radar charts for cluster visualization
- Clusters differentiated by activity level, profitability, and directional bias

### Interactive Dashboard
- Streamlit app (`app.py`) with 4 analysis tabs and sidebar filters

---

## Output Files
| File | Description |
|------|-------------|
| `output/chart1_pnl_by_sentiment.png` | PnL distributions by sentiment |
| `output/chart2_behavioral_patterns.png` | Trade frequency, size, L/S ratio by sentiment |
| `output/chart3_trader_segmentation.png` | Segment-level performance comparison |
| `output/chart4_timeseries_heatmap.png` | Cumulative PnL, correlations, top traders |
| `output/chart5_feature_importance.png` | ML feature importances |
| `output/chart6_trader_clustering.png` | Trader archetype clustering |
| `output/chart7_volatility_prediction.png` | PnL volatility prediction |
| `output/key_metrics_by_sentiment.csv` | Summary metrics table |
| `output/account_stats.csv` | Per-account statistics |
| `output/daily_aggregate.csv` | Daily aggregate metrics |
| `output/daily_trader_level.csv` | Daily per-trader metrics |

---

## Project Structure
```
├── analysis.py               # Main analysis script (Parts A, B, C + Bonus)
├── analysis_notebook.ipynb   # Jupyter notebook (interactive walkthrough)
├── app.py                    # Streamlit dashboard (bonus)
├── requirements.txt          # Python dependencies
├── README.md                 # This file
├── data/
│   ├── sentiment_data.csv
│   └── trader_data.csv
└── output/
    ├── chart1_pnl_by_sentiment.png
    ├── chart2_behavioral_patterns.png
    ├── chart3_trader_segmentation.png
    ├── chart4_timeseries_heatmap.png
    ├── chart5_feature_importance.png
    ├── chart6_trader_clustering.png
    ├── chart7_volatility_prediction.png
    ├── key_metrics_by_sentiment.csv
    ├── account_stats.csv
    ├── daily_aggregate.csv
    ├── daily_trader_level.csv
    └── analysis_log.txt
```
