# oil-futures-model
# WTI Crude Oil Futures Curve Model

## What This Is
A quantitative research project that models the WTI crude oil futures curve, detects market regimes using a Hidden Markov Model, and backtests a calendar spread trading strategy from 2010 to present.

## Motivation
Oil futures markets alternate between two structural states — contango (future prices higher than spot) and backwardation (future prices lower than spot). These regimes have different implications for traders and reflect underlying supply/demand dynamics. This project builds a systematic framework to detect those regimes and tests whether trading on them is profitable.

## Methodology

### Phase 1 — Futures Curve Data
- Pulled daily WTI front-month (RCLC1) and 4-month (RCLC4) futures prices from the EIA API from 2010 to present
- Calculated log spread as the core signal: log(far price) - log(near price)
- Positive spread = contango, negative spread = backwardation

### Phase 2 — Schwartz 2-Factor Decomposition
- Decomposed the log spread into two factors:
  - Chi (χ): long-run equilibrium mean = 0.0095
  - Xi (ξ): short-term deviation from equilibrium
- Estimated mean reversion speed (kappa = 0.018, half-life = 38 days)
- Annualized spread volatility = 14.6%

### Phase 3 — HMM Regime Detection
- Fitted a 2-state Gaussian Hidden Markov Model on the xi factor
- Regime 0 (Contango): mean xi = +0.038
- Regime 1 (Backwardation): mean xi = -0.006
- Regimes correspond to known market events:
  - 2015-2016: OPEC oil glut → contango
  - 2018-2019: supply tightness → backwardation
  - 2020: COVID demand collapse → extreme contango spike

### Phase 4 — Backtest
- Strategy: short the spread in contango regimes, long the spread in backwardation regimes
- Signal is lagged by one day to avoid lookahead bias
- Results (2010-present):
  - Total log P&L: +0.22
  - Sharpe Ratio: 0.10
  - Max Drawdown: -0.55
- Strategy performs consistently in normal market conditions
- The 2020 COVID event caused a significant drawdown as the regime signal lagged the extreme move — a known limitation of HMM-based detection during structural breaks

## Tech Stack
- Python, Pandas, NumPy, Matplotlib
- hmmlearn (HMM fitting)
- EIA API (data source)

## Files
- `oil_futures_model.ipynb` — full notebook with all analysis and charts

## Resume Line
Built a WTI crude oil futures curve model using Schwartz 2-factor decomposition and Hidden Markov Model regime classification to detect contango/backwardation regimes, backtested a calendar spread strategy from 2010–present achieving positive P&L over 14 years of data.

## Limitations and Next Steps
- Chi is modeled as a static constant rather than a stochastic process (full Schwartz implementation)
- Strategy has no transaction costs — real implementation would reduce P&L
- Regime detection lags during extreme tail events
- Next steps: volatility-scaled position sizing, transaction cost modeling, expanding to term structure beyond 2 contract months
