# NVIDIA Log-Returns: OLS Regression Analysis

**Authors:** Marek Siuda
**Institution:** Cracow University of Economics (UEK), Econometrics Course  
**Period:** January 2019 – May 2025 · N = 77 monthly observations

---

## Overview

This project builds an econometric model explaining NVIDIA Corporation (NVDA) monthly log-returns using five macro-financial variables. The analysis was originally conducted in Gretl and Excel for an academic Econometrics course, and is reproduced here in Python with full transparency and reproducibility.

The project demonstrates: OLS estimation, functional form testing (Ramsey RESET), model selection via sequential backward elimination, and a standard suite of regression diagnostics.

---

## Research Question

> *To what extent can NVIDIA's monthly stock performance be explained by macro-financial indicators — specifically retail investor leverage, market volatility, interest rates, tech-sector momentum, and currency dynamics?*

---

## Variables

| Symbol | Variable | Transformation | Source |
|--------|----------|---------------|--------|
| **Y** | NVIDIA stock price | Log-return: Δln(P) | Yahoo Finance |
| **X1** | FINRA margin debt | Log-change: Δln | FINRA Monthly Statistics |
| **X2** | CBOE VIX index | Level | CBOE via Yahoo Finance |
| **X3** | Fed Funds Rate (FRED) | Log-change: Δln | Federal Reserve (FRED) |
| **X4** | Invesco QQQ ETF | Log-return: Δln | Yahoo Finance |
| **X5** | USD/TWD exchange rate | Log-change: Δln | Yahoo Finance |

---

## Methodology

### Step 1 — Baseline OLS (5 regressors)
$$Y_t = \beta_0 + \beta_1 X1_t + \beta_2 X2_t + \beta_3 X3_t + \beta_4 X4_t + \beta_5 X5_t + \varepsilon_t$$

The baseline model achieves **R² = 0.62** with a highly significant F-statistic, suggesting that macro-financial factors collectively explain a meaningful share of NVDA's monthly return variation.

### Step 2 — Ramsey RESET Test
The RESET test (Ramsey 1969) evaluates whether powers of the fitted values (ŷ², ŷ³) carry additional explanatory power — a sign of omitted non-linearities or interaction effects. The test rejects the purely linear specification at α = 0.10, motivating an enriched model.

### Step 3 — Extended Model
We add squared terms for each predictor and all 10 pairwise interaction terms, yielding an augmented specification with 20 regressors. Sequential backward elimination at α = 0.10 prunes all statistically redundant terms.

### Step 4 — Final Model
The elimination procedure converges on a parsimonious two-regressor model:

$$Y_t = \beta_0 + \beta_1 \cdot \Delta\ln(\text{QQQ})_t + \beta_2 \cdot \Delta\ln(\text{QQQ})_t \times \Delta\ln(\text{Rate})_t + \varepsilon_t$$

**Final R² = 0.64**, F-statistic significant at p < 10⁻¹⁶.

---

## Key Findings

**QQQ log-return (X4)** is the single strongest predictor of NVDA monthly performance. As a high-beta Nasdaq-100 constituent, NVIDIA amplifies broad tech-sector momentum captured by QQQ.

**The QQQ × ΔlnRate interaction** is economically intuitive: the sensitivity of NVDA returns to tech-sector momentum is itself a function of the interest rate environment. When the Fed tightens (positive ΔlnRate), growth-stock discount rates rise and the QQQ-to-NVDA pass-through weakens. The opposite holds during easing cycles — a pattern consistent with duration risk in high-multiple stocks.

The variables X1 (margin debt), X2 (VIX), and X5 (USD/TWD) are individually insignificant after controlling for QQQ and the rate interaction, suggesting their effects are either mediated by the tech index or too noisy at monthly frequency to isolate cleanly.

---

## Diagnostics

| Test | Null Hypothesis | Result |
|------|----------------|--------|
| Breusch-Pagan | Homoskedasticity | See notebook |
| Jarque-Bera | Normality of residuals | See notebook |
| Durbin-Watson | No autocorrelation | See notebook |
| Ljung-Box (lag 12) | No autocorrelation | See notebook |
| VIF | No severe multicollinearity | VIF < 5 for all regressors |

---

## Repo Structure

```
NVDAkodowanie/
├── notebooks/
│   └── 01_nvda_regression.ipynb   # Main analysis notebook
├── data/
│   └── raw/
│       └── nvda_macro_monthly.csv  # Clean 77-observation dataset
├── src/                            # (reserved for helper modules)
├── results/
│   └── figures/                    # Auto-generated plots
├── README.md
├── requirements.txt
└── .gitignore
```

---

## How to Run

```bash
# 1. Clone the repo
git clone <repo-url>
cd NVDAkodowanie

# 2. Create and activate a virtual environment
python3 -m venv .venv
source .venv/bin/activate        # macOS/Linux
# .venv\Scripts\activate         # Windows

# 3. Install dependencies
pip install -r requirements.txt

# 4. Launch JupyterLab
jupyter lab notebooks/01_nvda_regression.ipynb
```

---

## Requirements

- Python ≥ 3.10
- See `requirements.txt` for the full package list (pandas, numpy, statsmodels, matplotlib, seaborn, yfinance, jupyter)

---

## Data Notes

All price-based variables are transformed to log-returns (Δln) to achieve stationarity and percentage-change interpretability. The dataset spans January 2019 – May 2025, covering the pre-COVID boom, the 2020 crash and recovery, the 2021–2022 AI hype cycle, the 2022 rate-hike bear market, and the 2023–2025 AI renaissance.

FINRA margin debt is sourced from FINRA's monthly statistics and represents aggregate debit balances in margin accounts — a proxy for retail leveraged speculation.

---

## License

MIT — free to use, cite, and adapt.
