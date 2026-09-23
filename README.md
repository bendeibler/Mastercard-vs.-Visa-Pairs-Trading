# Pairs-Trading-Ma-V
A market-neutral pairs trading strategy built from scratch in Python — from statistical pair selection through backtesting, out-of-sample validation, and an interactive Tableau dashboard.

Show Image

View the interactive Tableau dashboard → (replace with your Tableau Public link)

Overview

Pairs trading is a market-neutral, mean-reversion strategy: when the price spread between two historically linked stocks diverges from its normal range, you short the relatively overvalued stock and buy the relatively undervalued one, betting the spread reverts. This project builds and rigorously tests such a strategy end-to-end, using Mastercard (MA) and Visa (V) as the traded pair.

Rather than stopping at a backtest, the project treats the negative result as the finding: it diagnoses why the strategy underperformed, checks whether that holds up out-of-sample, and stress-tests it against transaction costs and a full parameter grid — the kind of rigor that separates a credible backtest from a curve-fit one.

Methodology
Candidate screening — Tested correlation and cointegration (Engle-Granger) across a candidate universe (KO/PEP, MA/V, HD/LOW, JPM/BAC) rather than picking a pair by eye. KO/PEP looked promising on correlation (0.68) but failed cointegration (p = 1.00) and was rejected. MA/V was the only pair (of 15 tested combinations) to pass cointegration at all, at p = 0.0476.
Spread construction — Estimated a rolling 60-day OLS hedge ratio between log(MA) and log(V), shifted one day to avoid look-ahead bias, and built the spread as log(MA) − hedge_ratio × log(V).
Signal generation — Standardized the spread into a rolling 30-day z-score. Entered positions at ±2 standard deviations, exited near a z-score of 0.
Backtesting — Applied signals with a one-day lag (no look-ahead), compounded daily returns, and computed standard performance metrics.
Diagnostics & robustness checks:
Trade-by-trade P&L breakdown (28 discrete trades)
Transaction cost sensitivity (10 bps per trade)
70/30 walk-forward validation (threshold selected in-sample, tested out-of-sample, untouched)
Benchmark comparison vs. buy-and-hold MA, buy-and-hold V, and SPY
Full parameter sensitivity grid (5 z-score windows × 5 entry thresholds = 25 combinations)
Dashboard — Backtest outputs (daily series, trade-level table, sensitivity grid) exported to CSV and visualized in Tableau for interactive exploration.
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

What I'd Try Next
Widen the candidate universe beyond payments/consumer-staples/banks
Test a shorter or volatility-adaptive hedge-ratio window to respond faster to regime changes
Size positions by z-score magnitude or spread volatility instead of a fixed ±1 unit position
Repository Structure
├── notebooks/
│   └── pairs_trading_MA_V.ipynb      # Full analysis: selection, backtest, diagnostics
├── data/
│   ├── raw/                          # Raw price data (if saved)
│   └── processed/
│       ├── ma_v_daily_tableau.csv    # Daily price/spread/z-score/signal/returns
│       ├── trades_tableau.csv        # Trade-level P&L and holding periods
│       └── sensitivity_tableau.csv   # Z-score window x entry threshold Sharpe grid
├── images/
│   └── dashboard_preview.png         # Dashboard screenshot
├── tableau/
│   └── pairs_trading_dashboard.twbx  # Packaged Tableau workbook
└── README.md
Tools & Techniques

Python: pandas, NumPy, statsmodels (ADF test, Engle-Granger cointegration, rolling OLS), yfinance, matplotlib, seaborn Statistics: stationarity testing, cointegration testing, z-score standardization, walk-forward (out-of-sample) validation Visualization: Tableau (interactive dashboard with train/test comparison, trade return chart, Sharpe sensitivity heatmap, holding period scatter)

How to Run
bash
pip install yfinance pandas numpy statsmodels matplotlib seaborn
jupyter notebook notebooks/pairs_trading_MA_V.ipynb

Data is pulled live via yfinance, so re-running the notebook will refresh results with the latest available prices.
