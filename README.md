Swiss Vol Risk Framework — Volatility & VaR Analytics

Production-grade framework for volatility forecasting and VaR measurement — IB / Risk Management desk

UBS SIX-Listed Equities: UBSG, NESN, NOVN, ROP (Roche) vs. SMI

📋 Project Overview
Este proyecto entrega un framework production-grade para forecasting de volatilidad y medición de riesgo en cuatro equities suizos cotizados en la bolsa SIX. Cubre el pipeline completo desde EDA e identificación de regímenes de volatilidad hasta modelado GJR-GARCH, backtesting de VaR, refinamiento con Skew-t, y evaluación de un challenger ML (LightGBM).
Data vintage: yfinance pull, 2026-07-24
Author: Isai Macías
Status: ✅ 3 Milestones completados
🗂️ Repository Structure
swiss-vol-risk-framework/
├── data/                           # Datasets (Excel files)
├── notebooks/                      # Jupyter Notebooks interactivos
│   ├── 01_eda_regime_correlation.ipynb    # Milestone 1: EDA & Regime Audit
│   ├── 02_garch_var_backtest.ipynb       # Milestone 2: GJR-GARCH & VaR Backtest
│   └── 03_skewt_ml_challenger.ipynb      # Milestone 3: Skew-t & ML Challenger
├── reports/                        # Reportes estáticos (HTML + PDF memos)
│   ├── 01_eda_regime_correlation.html
│   ├── 02_garch_var_backtest.html
│   ├── 03_skewt_ml_challenger.html
│   ├── milestone1_memo.pdf
│   ├── milestone2_memo.pdf
│   ├── milestone3_memo.pdf
│   └── standing_open_threads.pdf
├── README.md
└── requirements.txt
🚀 Quick Start
# Clone the repository
git clone https://github.com/isamacias666-cell/swiss-vol-risk-framework.git
cd swiss-vol-risk-framework
# Create virtual environment
python -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate
# Install dependencies
pip install -r requirements.txt
# Run notebooks in order
jupyter notebook notebooks/01_eda_regime_correlation.ipynb
jupyter notebook notebooks/02_garch_var_backtest.ipynb
jupyter notebook notebooks/03_skewt_ml_challenger.ipynb
📊 Key Results — 3 Milestones
Milestone 1 — EDA & Volatility Regime Audit
Deliverable	Finding
Regime-Conditioned Correlation	Correlation structure shifts under stress but NOT uniformly — 2022 rate-hiking cycle showed lowercorrelations than calm periods (decoupling, not convergence)
FX-Beta Decomposition	FX explains only 1.4%–2.9% of daily variance; UBSG beta halvedpost-CS merger
Stationarity Tests	All return series stationary (ADF/KPSS) — valid input for GARCH
NESN Event Day	~7.2σ outlier — keep-not-winsorize(documented, not suppressed)
NOVN / Sandoz Spin-off	Oct 2023: no step change in adjusted prices — no correction needed
ROP (Roche)	Genussschein→Participation cert (Mar 2026): continuous, no discontinuity
Milestone 2 — GJR-GARCH Volatility Forecasting & VaR Backtesting
Deliverable	Finding
Separate vs. Pooled (LR test)	Full sample p≈0 → UBSG separate; Pre-merger p=0.270 → pooled OK; Post-merger p=9.7×10⁻⁸ → must be separate
UBSG GJR-GARCH(1,1) + Student-t	Separate treatment required; pooled for NESN/NOVN/ROP
1-Day VaR Backtest	UBSG 99% VaR borderline undercoverage (24 violations, p=0.0144) — skew/tail-mismatch identified as root cause
10-Day Stress VaR	FHS consistently more conservative than MC (5.9%–11.7% gap)
Honest Narrative	Includes dead ends, bugs caught, and the refit-8/CS crisis clustering story built and killed by its own evidence
Milestone 3 — Skew-t Refinement & ML Challenger Evaluation
Deliverable	Finding
Skew-t vs. Symmetric-t (LR test)	LR p=0.0034, AIC agrees — Skew-t adopted
Walk-forward Backtest	21 violations (p=0.0785) vs. 24 of symmetric-t — exits the borderline zone ✅
Paired Comparison	0 days flipped unfavorably; 3 flipped favorably (borderline→safe), not clustered (Mar 2023–Sep 2025)
ML Challenger (LightGBM)	Does NOT beat pre-registered criterion — negative-but-informative result❌
Setup Failure Documented	Brace-expansion bug recurred after env reset → fixed via setup_scaffold.sh
📝 Work Log (Progress Journal)
Day 1–2: Data Ingestion & Audit
	Pulled price data (2005–present) for UBSG, NESN, NOVN, ROP, SMI, USDCHF, EURCHF via yfinance
	Audited corporate actions: NOVN/Sandoz spin-off, ROP share class conversion
	Flagged NESN 7.2σ event; decision: keep-not-winsorize
Day 3–4: EDA & Regime Analysis
	Built rolling volatility (21d, 63d) and flagged 4 regimes: GFC 2008, CHF unpeg 2015, COVID 2020, rate-hiking 2022
	Computed regime-conditioned correlation matrices — 2022 decoupling documented
	FX-beta decomposition: FX explains only 1.4%–2.9% of variance; UBSG shifted post-CS merger
	All stationarity tests passed (ADF/KPSS)
Day 5–6: GJR-GARCH Modeling
	Fitted GJR-GARCH(1,1) Student-t for UBSG (separate) and NESN/NOVN/ROP (pooled)
	LR test confirmed separate-vs-pooled decision: post-merger p=9.7×10⁻⁸
	Walk-forward 1-day VaR backtest: Kupiec + Christoffersen tests
	UBSG 99% VaR borderline undercoverage identified — skew-mismatch hypothesis
Day 7–8: 10-Day Stress VaR
	Monte Carlo vs. Filtered Historical Simulation comparison
	FHS consistently more conservative (5.9%–11.7% gap)
	Investigated backtest failures on their merits
Day 9: Skew-t Refinement
	Tested Hansen's skewed Student-t vs. symmetric-t
	LR p=0.0034 → Skew-t adopted
	Walk-forward backtest: 21 violations (p=0.0785) — exits borderline zone
	Paired comparison: 3 favorable flips, 0 unfavorable, not clustered
Day 10: ML Challenger Evaluation
	Built LightGBM challenger with pre-registered two-part criterion
	Result: does NOT beat GJR-GARCH on out-of-sample QLIKE + calibration
	Negative-but-informative: documented as process record, not hidden
💡 Business Insights
1.	UBSG requires separate treatment post-CS merger — pooling after June 2023 silently mixes two different risk profiles
2.	2022 rate-hiking cycle decoupled correlations — the usual "correlations spike in stress" assumption fails here; hedging strategies based on that assumption are unreliable
3.	FX is a minor driver — 1.4%–2.9% of daily variance; equity-specific factors dominate
4.	Skew-t materially improves 99% VaR coverage for UBSG — moving from borderline (p=0.014) to comfortable (p=0.079)
5.	GARCH still beats LightGBM for this problem — the structured model's variance-targeting and leverage asymmetry encode financial theory that generic ML cannot replicate with this sample size
6.	FHS > MC for stress VaR — more conservative, more robust to distributional misspecification
📚 Lessons Learned
1.	Pre-register your success criteria — the ML challenger evaluation was credible because the bar was set before fitting anything
2.	"It works" and "why it works" are different questions — the paired comparison proved skew-t's improvement was systematic, not noise
3.	Walk-forward or nothing — in-sample fit is necessary but not sufficient; the only thing that matters is out-of-sample performance
4.	Document dead ends — the refit-8 CS clustering story was killed by its own evidence; that record is more valuable than a tidy narrative
5.	Fix process, not just symptoms — the brace-expansion bug recurred across 3 milestones because the fix was a one-off command, not a saved script
6.	Keep outliers, annotate them — the NESN 7.2σ event was kept with documentation, not winsorized away
7.	BIC and AIC don't always agree — disclosed rather than smoothed when they diverge slightly
🔧 Requirements
See requirements.txt for the full dependency list.
📄 License
This project is licensed under the MIT License — see the LICENSE file for details.
