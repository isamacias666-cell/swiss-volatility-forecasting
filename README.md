# Swiss Vol Risk Framework — Volatility & VaR Analytics

**Production-grade framework for volatility forecasting and VaR measurement — IB / Risk Management Desk**

**UBS SIX-listed equities:** UBSG, NESN, NOVN, ROP (Roche) vs. SMI

---

## 📋 Project Overview

This project delivers a **production-grade framework for volatility forecasting and risk measurement** across four Swiss equities listed on the SIX Swiss Exchange.

The framework covers the complete analytical pipeline:

* Exploratory Data Analysis (EDA)
* Volatility regime identification
* Regime-conditioned correlation analysis
* FX-beta decomposition
* Stationarity testing
* GJR-GARCH volatility modeling
* 1-day VaR backtesting
* 10-day stress VaR
* Monte Carlo simulation
* Filtered Historical Simulation (FHS)
* Skewed Student-t refinement
* Walk-forward validation
* Machine Learning challenger evaluation using LightGBM

**Data vintage:** yfinance pull — 2026-07-24
**Author:** Isai Macías
**Status:** ✅ 3 Milestones completed

---

## 🗂️ Repository Structure

```text
swiss-vol-risk-framework/
├── data/                                      # Datasets (Excel files)
├── notebooks/                                 # Interactive Jupyter Notebooks
│   ├── 01_eda_regime_correlation.ipynb       # Milestone 1: EDA & Regime Audit
│   ├── 02_garch_var_backtest.ipynb            # Milestone 2: GJR-GARCH & VaR Backtest
│   └── 03_skewt_ml_challenger.ipynb           # Milestone 3: Skew-t & ML Challenger
├── reports/                                   # Static reports (HTML + PDF memos)
│   ├── 01_eda_regime_correlation.html
│   ├── 02_garch_var_backtest.html
│   ├── 03_skewt_ml_challenger.html
│   ├── milestone1_memo.pdf
│   ├── milestone2_memo.pdf
│   ├── milestone3_memo.pdf
│   └── standing_open_threads.pdf
├── README.md
└── requirements.txt
```

---

## 🚀 Quick Start

### 1. Clone the repository

```bash
git clone https://github.com/isamacias666-cell/swiss-vol-risk-framework.git
cd swiss-vol-risk-framework
```

### 2. Create a virtual environment

```bash
python -m venv venv
```

Activate it:

**Windows**

```bash
venv\Scripts\activate
```

**macOS / Linux**

```bash
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Run the notebooks in order

```bash
jupyter notebook notebooks/01_eda_regime_correlation.ipynb
```

```bash
jupyter notebook notebooks/02_garch_var_backtest.ipynb
```

```bash
jupyter notebook notebooks/03_skewt_ml_challenger.ipynb
```

The notebooks should be executed sequentially because each milestone builds on the analytical decisions and validation framework established previously.

---

# 📊 Key Results — 3 Milestones

## Milestone 1 — EDA & Volatility Regime Audit

| Deliverable                        | Finding                                                                                                                                                                                 |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Regime-Conditioned Correlation** | Correlation structure shifts under stress but **not uniformly**. The 2022 rate-hiking cycle showed lower correlations than calm periods, indicating decoupling rather than convergence. |
| **FX-Beta Decomposition**          | FX explains only **1.4%–2.9% of daily variance**. UBSG beta approximately halved after the Credit Suisse merger.                                                                        |
| **Stationarity Tests**             | All return series were stationary under ADF/KPSS testing, providing valid inputs for GARCH modeling.                                                                                    |
| **NESN Event Day**                 | Approximately **7.2σ outlier**. Decision: keep and document rather than winsorize or suppress.                                                                                          |
| **NOVN / Sandoz Spin-off**         | October 2023: no step change detected in adjusted prices. No correction required.                                                                                                       |
| **ROP (Roche)**                    | Genussschein → Participation Certificate conversion in March 2026 produced a continuous series with no material discontinuity.                                                          |

---

## Milestone 2 — GJR-GARCH Volatility Forecasting & VaR Backtesting

| Deliverable                       | Finding                                                                                                                                                     |
| --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Separate vs. Pooled — LR Test** | Full sample: p ≈ 0 → UBSG requires separate treatment. Pre-merger: p = 0.270 → pooling acceptable. Post-merger: p = 9.7×10⁻⁸ → separate treatment required. |
| **GJR-GARCH(1,1) + Student-t**    | UBSG modeled separately; NESN/NOVN/ROP modeled using the pooled specification.                                                                              |
| **1-Day VaR Backtest**            | UBSG 99% VaR showed borderline undercoverage: **24 violations, p = 0.0144**. Tail/skew mismatch identified as the likely root cause.                        |
| **10-Day Stress VaR**             | FHS was consistently more conservative than Monte Carlo, with a **5.9%–11.7% gap**.                                                                         |
| **Research Narrative**            | Dead ends, bugs, and the refit-8 / Credit Suisse crisis-clustering hypothesis were documented and rejected when the evidence did not support them.          |

---

## Milestone 3 — Skew-t Refinement & ML Challenger Evaluation

| Deliverable                          | Finding                                                                                                                                    |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| **Skew-t vs. Symmetric-t — LR Test** | LR p = **0.0034**. AIC also supported the asymmetric specification. Skew-t was adopted.                                                    |
| **Walk-forward Backtest**            | **21 violations, p = 0.0785**, improving on the 24 violations from the symmetric-t model and moving the result out of the borderline zone. |
| **Paired Comparison**                | 0 unfavorable flips and 3 favorable flips. Improvements were not clustered in a single period.                                             |
| **ML Challenger — LightGBM**         | Did **not** beat the pre-registered success criterion. Negative-but-informative result.                                                    |
| **Setup Failure**                    | Brace-expansion bug recurred after environment reset and was fixed through `setup_scaffold.sh`.                                            |

---

# 📝 Work Log — Progress Journal

## Day 1–2: Data Ingestion & Audit

* Pulled price data from 2005–present for UBSG, NESN, NOVN, ROP, SMI, USDCHF and EURCHF using yfinance.
* Audited relevant corporate actions, including the NOVN/Sandoz spin-off and ROP share-class conversion.
* Identified the approximately 7.2σ NESN event.
* Decision: **keep-not-winsorize**, with the event explicitly documented.

## Day 3–4: EDA & Regime Analysis

* Built rolling volatility measures using 21-day and 63-day windows.
* Identified four major volatility regimes:

  * Global Financial Crisis — 2008
  * CHF unpeg — 2015
  * COVID-19 — 2020
  * Rate-hiking cycle — 2022
* Computed regime-conditioned correlation matrices.
* Documented the 2022 decoupling behavior.
* FX-beta decomposition showed FX explaining only **1.4%–2.9% of daily variance**.
* Identified the UBSG beta shift following the Credit Suisse merger.
* All ADF/KPSS stationarity tests passed.

## Day 5–6: GJR-GARCH Modeling

* Fitted GJR-GARCH(1,1) with Student-t innovations.
* UBSG was modeled separately.
* NESN/NOVN/ROP were modeled using the pooled specification.
* LR testing confirmed the separate-vs-pooled decision:

  * Post-merger p = **9.7×10⁻⁸**
* Performed walk-forward 1-day VaR backtesting.
* Applied Kupiec and Christoffersen tests.
* Identified borderline UBSG 99% VaR undercoverage.
* Developed the skew-mismatch hypothesis.

## Day 7–8: 10-Day Stress VaR

* Compared Monte Carlo simulation with Filtered Historical Simulation.
* FHS was consistently more conservative.
* Observed a **5.9%–11.7% gap** versus Monte Carlo.
* Investigated backtest failures based on evidence rather than forcing a preferred narrative.

## Day 9: Skew-t Refinement

* Tested Hansen's skewed Student-t specification against the symmetric Student-t model.
* LR test: **p = 0.0034**
* Skew-t was adopted.
* Walk-forward backtest:

  * **21 violations**
  * p = **0.0785**
* Paired comparison:

  * 3 favorable flips
  * 0 unfavorable flips
  * No evidence of temporal clustering

## Day 10: ML Challenger Evaluation

* Built a LightGBM challenger using a pre-registered two-part evaluation criterion.
* Compared out-of-sample performance against GJR-GARCH.
* LightGBM did **not** beat GJR-GARCH on the pre-registered QLIKE + calibration criterion.
* Result classified as **negative-but-informative** and retained as part of the research record rather than hidden.

---

# 💡 Business Insights

### 1. UBSG requires separate treatment after the Credit Suisse merger

Pooling post-merger UBSG observations silently combines two different risk profiles. The LR evidence strongly supports separate treatment.

### 2. The 2022 rate-hiking cycle challenges the usual stress-correlation assumption

The 2022 regime showed **decoupling rather than a universal increase in correlations**.

Therefore, risk and hedging strategies that automatically assume correlations will spike together during stress may be unreliable.

### 3. FX is a relatively minor driver of daily equity variance

USDCHF and EURCHF explain only approximately **1.4%–2.9% of daily variance**.

Equity-specific factors dominate the observed daily return variation.

### 4. Skew-t materially improves UBSG 99% VaR coverage

The UBSG backtest moved from:

**Symmetric-t:** p = 0.014 → borderline undercoverage

to:

**Skew-t:** p = 0.079 → substantially more comfortable coverage

This provides empirical support for modeling return asymmetry in the UBSG risk distribution.

### 5. GARCH still beats the LightGBM challenger

The structured GARCH model outperformed the ML challenger under the pre-registered evaluation criterion.

This suggests that, for this sample and problem, financial-model structure — including volatility persistence and leverage asymmetry — provides useful information that generic ML does not automatically recover.

### 6. FHS is more conservative than Monte Carlo for stress VaR

Filtered Historical Simulation consistently produced more conservative 10-day stress estimates.

This makes FHS a useful robustness cross-check when there is concern about distributional misspecification.

---

# 📚 Lessons Learned

1. **Pre-register success criteria.**
   The ML challenger evaluation remained credible because the success criteria were established before model fitting.

2. **"It works" and "why it works" are different questions.**
   The paired comparison showed that the Skew-t improvement was systematic rather than simply random noise.

3. **Walk-forward or nothing.**
   In-sample fit is necessary but insufficient. Out-of-sample performance is the key validation criterion.

4. **Document dead ends.**
   The refit-8 Credit Suisse clustering hypothesis was tested and rejected by its own evidence. That negative result is part of the research record.

5. **Fix processes, not just symptoms.**
   The brace-expansion bug recurred across three milestones because the original fix was a one-off command rather than a reusable setup script.

6. **Keep outliers and annotate them.**
   The NESN 7.2σ event was retained and documented rather than artificially removed through winsorization.

7. **AIC and BIC do not always agree.**
   When information criteria diverged slightly, the disagreement was disclosed rather than hidden or smoothed.

---

# 🔧 Requirements

See [`requirements.txt`](requirements.txt) for the complete dependency list.

The project uses Python and Jupyter notebooks for the analytical workflow.

---

# 📄 License

This repository is provided for research, educational, and analytical purposes.

Unless otherwise specified, no additional license is granted beyond the rights provided by the repository owner.
