# Swiss Vol Risk Framework — Volatility & VaR Analytics

**Volatility forecasting and VaR measurement framework — IB / Risk Management Desk**

**UBS SIX-listed equities:** UBSG, NESN, NOVN, ROP (Roche) vs. SMI

---

## 📋 Project Overview

This project builds a volatility forecasting and risk measurement framework
across four Swiss equities listed on the SIX Swiss Exchange, delivered as a
3-milestone progression with a documented decision trail — including two
fitting bugs found and fixed, and two competing hypotheses tested and
rejected before landing on the finding that survived scrutiny.

The framework covers:

* Exploratory Data Analysis (EDA) and corporate-action audit (spin-offs, ticker conversions)
* Volatility regime identification and regime-conditioned correlation analysis
* FX-beta decomposition
* Stationarity testing
* GJR-GARCH(1,1) volatility modeling, separate-vs-pooled specification via likelihood-ratio test
* 1-day VaR backtesting (Kupiec, Christoffersen)
* 10-day stress VaR: Monte Carlo simulation vs. Filtered Historical Simulation
* Skewed Student-t refinement, verified via paired day-by-day comparison
* Walk-forward out-of-sample validation
* Machine Learning challenger evaluation (LightGBM) against a pre-registered criterion

**Data vintage:** yfinance pull — 2026-07-24
**Author:** Isai Macías Tolosa
**Status:** ✅ 3 Milestones completed

---

## 🗂️ Repository Structure

> ⚠️ **Confirm before publishing:** the layout below assumes files are
> organized into subfolders. If your repo currently has all files at the
> root level (no `notebooks/`, `data/`, `reports/` subfolders), either
> reorganize the files to match this structure, or update the paths below
> to reflect the flat layout — the "Quick Start" commands must match
> whichever structure actually exists in the repo.

```text
swiss-vol-risk-framework/
├── data/                                      # Raw datasets (yfinance CSV, zipped)
├── notebooks/                                 # Interactive Jupyter Notebooks
│   ├── 01_eda_regime_correlation.ipynb        # Milestone 1: EDA & Regime Audit
│   ├── 02_garch_var_backtest.ipynb            # Milestone 2: GJR-GARCH & VaR Backtest
│   └── 03_skewt_ml_challenger.ipynb           # Milestone 3: Skew-t & ML Challenger
├── reports/                                   # Static reports (HTML + PDF memos)
│   ├── 01_eda_regime_correlation.html
│   ├── 02_garch_var_backtest.html
│   ├── 03_skewt_ml_challenger.html
│   ├── milestone1_memo.pdf
│   ├── milestone2_memo.pdf
│   ├── milestone3_memo.pdf
│   └── standing_open_threads.pdf              # See "Open Threads" below — read before assuming anything is fully closed
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

### 4. View the notebooks

Every notebook already contains its full computed output — tables, charts,
narrative — saved inside the file. Opening the `.ipynb` (on GitHub, or in
Jupyter) or the `.html` version shows the complete analysis with zero setup:

```bash
jupyter notebook notebooks/01_eda_regime_correlation.ipynb
```

### ⚠️ Reproducing from scratch — known limitation

Re-executing notebooks 02 and 03 end-to-end (e.g. "Restart & Run All") does
**not** currently work out of the box. They load several intermediate result
files (backtest summaries, per-refit diagnostics) generated during the
original interactive analysis sessions, which are not yet included in this
repo. This does not affect the validity of any finding shown in the
notebooks — every table and chart is already saved and correct — it only
affects literal from-scratch re-computation. Regenerating these files is
scoped, known follow-up work, not an open mystery.

---

# 📊 Key Results — 3 Milestones

## Milestone 1 — EDA & Volatility Regime Audit

| Deliverable | Finding |
|---|---|
| **Regime-Conditioned Correlation** | Correlation structure shifts under stress but **not uniformly**. The 2022 rate-hiking cycle showed *lower* correlations than the calm baseline — decoupling, not the convergence pattern seen in 2008/2020. |
| **FX-Beta Decomposition** | FX explains only **1.4%–2.9% of daily variance**. UBSG's USDCHF beta roughly halved after the Credit Suisse merger (directionally consistent, but resting on a ~2-year post-merger sample — not independently large-sample-confirmed). |
| **Stationarity Tests** | All return series stationary under both ADF and KPSS — valid inputs for GARCH modeling. |
| **NESN Event Day** | ~7.2σ single-day move, confirmed genuine (Q2 2026 earnings) via external cross-check. Decision: keep and document, not winsorize. |
| **NOVN / Sandoz Spin-off** | Oct 2023: confirmed via adjusted/unadjusted price-ratio test that the spin-off value transfer is not encoded in the price series. Annotated, not corrected. |
| **ROP (Roche)** | Genussschein → Participation Certificate conversion (Mar 2026) verified as a continuous series against Roche's own published year-end prices — no discontinuity. |

## Milestone 2 — GJR-GARCH Volatility Forecasting & VaR Backtesting

| Deliverable | Finding |
|---|---|
| **Separate vs. Pooled — LR Test** | Full sample: p ≈ 0. Pre-merger-only: p = 0.270 (pooling not rejected). Post-merger-only: p = 9.7×10⁻⁸ (strongly rejects pooling despite only 780 obs). Separate UBSG treatment adopted, with the short post-merger sample disclosed as a caveat, not hidden. |
| **Two fitting bugs found and fixed** | (1) A negative likelihood-ratio statistic — mathematically impossible for a nested model comparison, traced to an optimizer local-optimum failure and fixed with multi-start + warm-starting. (2) An omega-boundary degeneracy in 11 of 23 walk-forward refits, fixed via variance-targeting — re-verified to confirm the fix wasn't just cosmetically changing the number. |
| **1-Day VaR Backtest** | UBSG 99% VaR: borderline undercoverage, 24 violations, Kupiec p = 0.0144 (does not survive multiple-testing correction across the 8 tests run). |
| **10-Day Stress VaR** | FHS consistently more conservative than Monte Carlo, 5.9%–11.7% gap — traced to the fitted Student-t distribution being measurably thinner-tailed than the empirical residuals, not treated as unexplained noise. |
| **Hypotheses tested and rejected** | A near-unit-persistence explanation was ruled out (persistence was uniformly high across all 23 refits — no variation to explain *which* quarters had violations). An event-clustering narrative around the March 2023 SVB/Credit Suisse window was built, then killed by a Monte Carlo null test (P(max violations across 23 quarters ≥ 3) = 43% — not statistically extreme). |

## Milestone 3 — Skew-t Refinement & ML Challenger Evaluation

| Deliverable | Finding |
|---|---|
| **Skew-t vs. Symmetric-t — LR Test** | p = 0.0034, AIC agrees (BIC essentially neutral — disclosed, not smoothed over). Skew-t adopted for UBSG. |
| **Walk-forward Backtest** | 21 violations, Kupiec p = 0.0785 — moved out of the borderline zone (from 24 violations / p = 0.0144 under symmetric-t). |
| **Paired Comparison** | 3 favorable reclassifications, 0 unfavorable — verified day-by-day, not just by comparing two p-values. No temporal clustering (spread across ~2.5 years). |
| **Mechanism, decomposed rather than assumed** | 2 of the 3 improved days are cleanly attributable to skewness alone (the ν parameter barely moved). The third shows ν dropping by over 2 points alongside the skew parameter — a **joint effect, not purely skewness**. Reported as "mostly, not purely" rather than rounded to a cleaner claim. |
| **ML Challenger — LightGBM** | Evaluated against a two-part criterion fixed *before* fitting: beat GJR-GARCH on QLIKE **and** not fail calibration where GARCH passes. Result: LightGBM **wins on QLIKE** (average forecast loss) and at 95% VaR calibration, but **fails Kupiec at 99%** — exactly where GJR-GARCH passes. Per the pre-registered rule, GJR-GARCH retains champion status; LightGBM is retained as a monitored alternative, not discarded. |
| **Process note** | The same environment-setup bug (a shell brace-expansion failure) recurred after a mid-project environment reset, despite being caught once already — fixed with a standing `setup_scaffold.sh` script rather than relying on remembering the fix. |

---

# 📝 Open Threads — read before assuming anything is fully closed

`standing_open_threads.pdf` documents six items deliberately **not**
presented as resolved: UBSG's post-merger sample size, the deferred
event-clustering methodology, the undecomposed refit-10 joint effect, the ML
challenger's monitored 99% calibration gap, the skew-t BIC near-tie, and a
process reminder that a fix isn't real until it survives an environment
reset. This file is placed here deliberately — a documented list of what
isn't fully closed is a sign of rigor, not incompleteness.

---

# 💡 Business Insights

### 1. UBSG requires separate treatment after the Credit Suisse merger
Supported by a standalone post-merger LR test (p = 9.7×10⁻⁸) despite a short
window, and an empirically verified conservatism check — the separate fit
implies *higher* volatility and VaR for UBSG than pooled parameters would.

### 2. The 2022 rate-hiking cycle challenges the usual stress-correlation assumption
2022 showed decoupling, not the convergence-under-stress pattern seen in
2008/2020 — risk/hedging assumptions that treat "correlations spike together
in a crisis" as universal would misrepresent this regime.

### 3. FX is a relatively minor driver of daily equity variance
USDCHF/EURCHF explain ~1.4%–2.9% of daily variance — equity-specific risk
dominates at a daily horizon.

### 4. Skew-t materially, but not purely, improves UBSG's 99% VaR coverage
Kupiec p moves from 0.014 to 0.079 — a real, paired-comparison-verified
improvement, with the mechanism honestly decomposed (see Milestone 3 table)
rather than attributed entirely to skewness.

### 5. GJR-GARCH retains champion status — but the challenger result is mixed, not one-sided
LightGBM beat the champion on QLIKE and at 95% calibration, and lost
specifically at 99% — where it matters most for tail risk. The pre-registered
rule made this an unambiguous decision despite the challenger's real
strengths, which is the point of pre-registering it.

### 6. FHS is a useful robustness cross-check against distributional misspecification
FHS's consistently more conservative 10-day estimates trace to a measurable
thin-tail gap in the fitted parametric distribution, not to arbitrary model
disagreement.

---

# 📚 Lessons Learned

1. **Pre-register success criteria** — the ML evaluation stayed credible because the win condition was fixed before any model was fit.
2. **A cleaner-sounding claim is not automatically the correct one** — the skew-t improvement was mostly, not purely, attributable to skewness, and saying so required checking each flipped day individually rather than trusting the aggregate p-value shift.
3. **Walk-forward or nothing** — in-sample fit is necessary but insufficient; every headline number in this project is out-of-sample.
4. **Document dead ends** — the refit-8 crisis-clustering hypothesis was tested and rejected by its own evidence; that negative result stayed in the record instead of being quietly dropped.
5. **Fix processes, not just symptoms** — a shell scripting bug recurred once after being "fixed" the first time, because the fix wasn't saved anywhere durable; `setup_scaffold.sh` exists specifically so that can't happen a third time.
6. **Keep outliers and annotate them** — the NESN 7.2σ event was verified as genuine and kept, not removed, because a fat-tailed model architecture needs real tail events to be meaningfully tested against.
7. **When two verification methods disagree, explain why — don't pick the convenient one** — a passing Christoffersen test and an apparent violation cluster were reconciled by checking what the formal test actually measures, rather than treating one as overriding the other.

---

# 🔧 Requirements

See [`requirements.txt`](requirements.txt) for the complete dependency list.

---

# 📄 License

This repository is provided for research, educational, and portfolio purposes.
Unless otherwise specified, all rights are retained by the author.
