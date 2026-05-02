# Equity-Portfolio-Analytics-Engine
Project: Equity Portfolio Analytics Engine
Stack: Python, Jupyter, openpyxl, yfinance, scipy, scikit-learn, sqlite3

A full personal investment analytics system built for ethics-screened portfolios. Covers data engineering, factor modeling, risk analytics, backtesting, and automated compliance, all in one notebook that outputs a 17-sheet Excel dashboard.

The problem it solves:
Most portfolio trackers tell you what you own and what it is worth. This one tells you why you should own it, whether it passes your compliance screen, how much risk it adds, and what the systematic evidence says about holding it.

Data layer:
- Dual-source price fetch: Yahoo Finance primary, Alpha Vantage cross-validation
- Price staleness detection at 15 minutes with after-hours market flagging
- Cross-source discrepancy alerts at 0.5% tolerance
- SQLite caching with 1-hour TTL to avoid redundant API calls
- Exponential backoff retries on failed requests
- Parallel fundamentals enrichment via ThreadPoolExecutor across all tickers simultaneously

Four-factor conviction model:
Grounded in Fama-French and Asness QMJ academic research. Every stock in the portfolio and all four watchlists gets scored across:
- Value: forward P/E, EV/EBITDA, FCF yield
- Growth: TTM revenue growth (5 quarters), EPS growth, forward EPS estimates
- Momentum: 12-1 month return, 50/200-day MA signal, RSI
- Quality: ROE, Debt/EBITDA, interest coverage, Altman Z-score proxy

  
All four factors are converted to percentile ranks within the universe. High-beta names (beta above 1.5) receive a risk-adjusted multiplier that pulls their score down. Final grades run A through D.

Ethics screening:
- Pulls live Debt/Assets directly from balance sheet data on every run
- Below 25%: Compliant. 25 to 33%: Monitor. Above 33%: flagged
- SIC code and industry keyword scan flags financial institutions, alcohol, tobacco, weapons, gambling, and other excluded sectors automatically
- Zero manual flags. The compliance audit sheet updates itself.

Risk analytics:
- Historical VaR at 95% and 99% confidence intervals
- CVaR (conditional value at risk / expected shortfall)
- Full correlation matrix across all holdings with high-correlation pair detection above 0.70
- Effective N-bets metric to measure true diversification
- Five stress scenarios: Fed +200bps rate shock, USD/CAD at 1.25, USD/CAD at 1.55, Tech sector -20%, Full recession -30%, Gold +25%
- Half-Kelly position sizing with a hard 15% cap per name
- ATR(14) dynamic stop-loss levels per holding, surfaced as both a price and a distance percentage

Backtest engine:
- 3-year walk-forward simulation on monthly momentum top-10 rebalancing
- 0.1% transaction cost friction applied on both sides of every trade
- Benchmarked against SPY with annualized return, Sharpe ratio, alpha, and maximum drawdown
- Equity curve chart included in the Excel output

Peer and sector analysis:
- Relative strength vs sector ETF over 63 trading days
- Peer multiples table: forward P/E, EV/EBITDA, P/S, P/B versus sector median
- Beta and alpha decomposition for each holding
- Portfolio factor exposure summary showing weighted averages across all four factors

Excel output (17 sheets):
- Data freshness and source validation
- Live holdings with ATR stops, RSI, and compliance status
- Four watchlists: US stocks, Canadian stocks, Canadian sub-$10, dividend names
- Conviction screener with full factor scores
- Top 5 picks card view
- Momentum alerts filtered to Strong Buy and Buy Zone
- Growth scatter chart
- Risk metrics and stress test results
- Peer multiples comparison
- Backtest results with equity curve
- Factor exposure per position
- Compliance audit log with re-verify flags
- Redeployment priority map grouped by signal
- Summary dashboard with 12-metric block and top picks

Design: Notion x Oxford palette. Oxford navy headers, Notion warm off-white alternating rows, pill-style grade badges. Font size 12 for data, 13 for headers. Built to be readable at a glance without any chart dependency.

The system runs end to end from a single cell. Every assumption is explicit, every output is traceable back to a source.

Personal finance tooling built for people who actually think about what they own.


## Preview
<img width="1366" height="387" alt="image" src="https://github.com/user-attachments/assets/4c77ade8-d982-42e4-9ab9-c3b3af27fefa" />
