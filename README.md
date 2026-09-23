# Pairs-Trading-Ma-V
A market-neutral pairs trading strategy built from scratch in Python — from statistical pair selection through backtesting, out-of-sample validation, and a Tableau dashboard.

<img width="1668" height="790" alt="image" src="https://github.com/user-attachments/assets/ee11ade6-68ff-4f44-aef4-974950314884" />


## Overview

Pairs trading is a market-neutral, mean-reversion strategy: when the price spread between two historically linked stocks diverges from its normal range, you short the relatively overvalued stock and buy the relatively undervalued one, betting the spread reverts. This project builds and rigorously tests such a strategy end-to-end, using Mastercard (MA) and Visa (V) as the traded pair.

Rather than stopping at a backtest, the project treats the negative result as the finding: it diagnoses why the strategy underperformed, checks whether that holds up out-of-sample, and stress-tests it against transaction costs and a full parameter grid — the kind of rigor that separates a credible backtest from a curve-fit one.

## Methodology
### Candidate screening — Tested correlation and cointegration (Engle-Granger) across a candidate universe (KO/PEP, MA/V, HD/LOW, JPM/BAC) rather than picking a pair by eye. KO/PEP looked promising on correlation (0.68) but failed cointegration (p = 1.00) and was rejected. MA/V was the only pair (of 15 tested combinations) to pass cointegration at all, at p = 0.0476.
### Spread construction — Estimated a rolling 60-day OLS hedge ratio between log(MA) and log(V), shifted one day to avoid look-ahead bias, and built the spread as log(MA) − hedge_ratio × log(V).
### Signal generation — Standardized the spread into a rolling 30-day z-score. Entered positions at ±2 standard deviations, exited near a z-score of 0.
### Backtesting — Applied signals with a one-day lag (no look-ahead), compounded daily returns, and computed standard performance metrics.
### Diagnostics & robustness checks:
Trade-by-trade P&L breakdown (28 discrete trades)
### Transaction cost sensitivity (10 bps per trade)
### 70/30 walk-forward validation (threshold selected in-sample, tested out-of-sample, untouched)
### Benchmark comparison vs. buy-and-hold MA, buy-and-hold V, and SPY
### Full parameter sensitivity grid (5 z-score windows × 5 entry thresholds = 25 combinations)

## Dashboard — Backtest outputs (daily series, trade-level table, sensitivity grid) exported to CSV and visualized in Tableau for interactive exploration.
Key Results
Metric	Gross	Net of Costs (10bps)
Sharpe Ratio	-0.05	-0.14
Total Return	-6.56%	-11.55%
Max Drawdown	-25.12%	—
Win Rate	49.78%	—
Trade Stats	
Number of Trades	28
Average Trade P&L	-0.49%
Best / Worst Trade	+5.14% / -8.67%
Out-of-Sample Sharpe	-0.83
Out-of-Sample Total Return	-17.19%

Bottom line: the MA/V pairs strategy did not generate a profitable edge over this sample. The cointegration signal was statistically real but weak (p = 0.0476, barely under the standard threshold), and a full 25-combination parameter sweep confirmed the underperformance wasn't the result of one unlucky threshold choice — only 2 of 25 combinations turned positive, and both barely above zero (Sharpe 0.06–0.09). The out-of-sample Sharpe (-0.83) was notably worse than the full-sample gross Sharpe (-0.05), reinforcing that the in-sample result would not have held up live.

Strategy volatility (11.41% annualized) was substantially lower than either underlying stock (MA: 24.12%, V: 22.75%) — evidence the long/short structure did strip out market direction, even though it didn't produce positive risk-adjusted returns here.

Python: pandas, NumPy, statsmodels (ADF test, Engle-Granger cointegration, rolling OLS), yfinance, matplotlib, seaborn Statistics: stationarity testing, cointegration testing, z-score standardization, walk-forward (out-of-sample) validation Visualization: Tableau (interactive dashboard with train/test comparison, trade return chart, Sharpe sensitivity heatmap, holding period scatter)





















## Methodology

The project follows an end-to-end quantitative research workflow:

Candidate Screening → Statistical Testing → Spread Construction → Signal Generation → Backtesting → Robustness Testing → Out-of-Sample Validation → Risk Analysis → Tableau Visualization

### 1. Candidate Screening

Tested correlation and Engle-Granger cointegration across a candidate universe rather than selecting a pair based solely on visual similarity.

Candidate pairs included:

KO / PEP
MA / V
HD / LOW
JPM / BAC

Across 15 tested combinations, MA/V was the only pair to pass the cointegration test, with a p-value of 0.0476.

As an example of why correlation alone was insufficient, KO/PEP showed a relatively strong correlation of 0.68 but failed the cointegration test.

### 2. Spread Construction

Estimated a 60-day rolling OLS hedge ratio between the log prices of MA and V.

The hedge ratio was shifted by one trading day before being used in the strategy to reduce look-ahead bias.

The spread was constructed as:

log(MA) − hedge_ratio × log(V)

### 3. Signal Generation

The spread was standardized using a 30-day rolling z-score.

Enter long spread when the z-score reaches -2
Enter short spread when the z-score reaches +2
Exit positions as the spread reverts toward zero

Trading signals were lagged by one day to ensure that information from the current trading period was not used to generate that same period's return.

### 4. Backtesting

The strategy compounds daily returns and evaluates both portfolio-level and trade-level performance.

The backtest includes:

28 discrete trades
Daily strategy returns
Cumulative returns
Trade-level P&L
Win rate
Sharpe ratio
Maximum drawdown
Holding periods
Annualized volatility

### 5. Robustness & Validation

The strategy was subjected to several tests designed to determine whether the historical results were robust:

### Transaction Costs

10 basis points applied when positions change

### Walk-Forward Validation

70% in-sample
30% out-of-sample
Entry threshold selected using only the in-sample period

### Parameter Sensitivity

5 z-score windows
5 entry thresholds
25 total parameter combinations

### Benchmark Comparison

MA buy-and-hold
V buy-and-hold




## Key Results
Metric	Gross	Net of Costs
Sharpe Ratio	-0.05	-0.14
Total Return	-6.56%	-11.55%
Maximum Drawdown	-25.12%	—
Win Rate	49.78%	—

### Trade-Level Results
Metric	Result
Number of Trades	28
Average Trade P&L	-0.49%
Best Trade	+5.14%
Worst Trade	-8.67%
Out-of-Sample Sharpe	-0.83
Out-of-Sample Return	-17.19%

### Parameter Sensitivity

The full 25-combination parameter grid provided additional evidence that the underperformance was not isolated to a single parameter choice.

Only 2 of 25 parameter combinations produced positive Sharpe ratios, and both were marginal at 0.06–0.09.

The out-of-sample Sharpe of -0.83 was substantially worse than the full-sample gross Sharpe of -0.05, indicating that the historical in-sample performance did not translate effectively to unseen data.

### Risk Characteristics

The strategy produced 11.41% annualized volatility, substantially below the individual stocks:

MA: 24.12%
V: 22.75%

This demonstrates that the long/short structure reduced directional exposure, even though the strategy did not generate positive risk-adjusted returns over the tested period.

## Key Takeaway

The MA/V pairs strategy did not produce a profitable trading edge over the tested sample.

Importantly, the analysis demonstrates why simply finding a statistically related pair is not enough to establish a viable trading strategy. Although MA/V passed the cointegration test at p = 0.0476, the relationship produced weak trading performance, deteriorated out-of-sample, and remained unprofitable across the majority of tested parameter combinations.

The project therefore emphasizes research discipline over backtest optimization: testing the hypothesis, controlling for look-ahead bias, incorporating realistic trading costs, validating on unseen data, stress-testing parameters, and documenting the negative result.




## Tableau Dashboard

Backtest outputs were exported to CSV and visualized in Tableau to create a dashboard covering:

Cumulative strategy performance
MA and V buy-and-hold performance
Individual trade returns
Train vs. test performance
Sharpe-ratio parameter sensitivity
Trade holding periods
Win/loss distribution
Strategy risk metrics

### Technologies
Python
pandas
NumPy
statsmodels
yfinance
matplotlib
seaborn

### Statistical & Quantitative Methods
OLS regression
Rolling hedge ratios
Augmented Dickey-Fuller testing
Engle-Granger cointegration
Z-score standardization
Mean-reversion signals
Walk-forward / out-of-sample validation
Transaction-cost modeling
Sharpe ratio
Maximum drawdown
Parameter sensitivity analysis

### Visualization
Tableau
Interactive dashboards
Time-series analysis
Parameter heatmaps
Trade-level visualization
