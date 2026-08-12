# Portfolio Risk & Performance Dashboard

**Aadarsh Sinnathambi** | Financial Mathematics, University College Cork

A Python tool that analyses my own DeGiro investment portfolio — reconstructing my actual trading history from raw transaction data, calculating realised and unrealised profit/loss, and measuring risk (volatility, Value at Risk, Sharpe ratio, and maximum drawdown).

## What it does

- Loads my current holdings and cost basis, converting everything into a single currency (EUR), since the portfolio holds both USD- and EUR-denominated assets
- Pulls live share prices and FX rates via the `yfinance` package
- Visualises portfolio allocation via pie chart and per-position returns via bar chart
- Parses my full DeGiro transaction log (91 trades since December 2024) to reconstruct exact daily share positions using cumulative sums, then rebuilds total portfolio value on every single calendar day by merging those positions with historical price and FX data
- Separates realised P/L (from 19 fully closed positions, computed directly from transaction cash flows) from unrealised P/L (on current holdings)
- Calculates annualised volatility, 1-day 95% Value at Risk, an approximate Sharpe ratio, and maximum drawdown on the reconstructed daily returns

## Key technical decisions

- **EUR as base currency** — matches my account's actual reporting currency, rather than converting to USD (which most holdings are priced in), avoiding an unnecessary second conversion step later.
- **Realised P/L from cash flows, not price data** — for a fully closed position, the net euro amount spent and received is the exact gain or loss, so no market data or estimation is needed for that calculation.
- **Daily position reconstruction** — built using `groupby` (splits data into separate groups so calculations can be done on each one individually) and `cumsum` (keeps a running total as trades happen over time) on the transaction log, then `reindex` (stretches the data out to match every calendar date, leaving gaps where there's no trade data) and `ffill` (fills those gaps with the last known value until a new one comes along) to convert "positions on trade days only" into "positions on every calendar day" — necessary because market prices only exist on trading days, but a portfolio's value technically exists every day, including weekends.

## Findings

- Realised P/L across the 19 fully closed positions is **negative (-€701)**, while unrealised gains on currently open positions (particularly AMD, LITE, and DELL) account for the bulk of overall profit — suggesting my "buy and hold" positions have outperformed the "actively trade in and out" positions.
- The portfolio carries meaningful concentration in **energy/utilities** (CEG, NEE, CWEN, FSLR).
- **AMD alone accounts for 26.7%** of total portfolio value — single-stock concentration, not just sector concentration. A bad company-specific event could meaningfully impact a quarter of the portfolio.
- **Investment approach behind the trading pattern:** my high turnover reflects a deliberate entry-and-conviction strategy rather than indecisive trading. I typically start a new position with a modest initial stake (~€500) after doing my own research, then add further capital over time — including during dips — if my conviction in the stock remains, regardless of short-term price movement. Decisions to add to or exit a position are driven by ongoing research and belief in the individual company, rather than broader market trend or sentiment. I close positions early and accept the loss when new information changes my original thesis, or when my conviction shifts toward a different opportunity. This explains the asymmetry between realised and unrealised P/L: the €701 realised loss represents the accepted cost of exiting weaker theses early, while the ~€10k unrealised gain reflects capital concentrated in higher-conviction, longer-held growth positions.

## Known limitations

- **Cash-flow distortion in risk metrics** — volatility, Sharpe ratio, and VaR are calculated on raw daily returns, which don't fully separate genuine market movement from days when new capital was added. Large capital injections on a given day inflate that day's apparent "return," overstating volatility and Sharpe. A fully accurate version would require time-weighted (cash-flow-adjusted) returns, which this iteration doesn't implement.
- **Approximated historical cash balance** — the current cash balance is held flat across the full period, since deposit/withdrawal history isn't available in the DeGiro transaction export (only trade history).
- **Dividends not captured** — realised P/L and total return figures slightly understate actual performance as a result.
- **One unmapped ticker** — a previously held, fully-closed Amundi ETF position isn't mapped to a Yahoo Finance ticker, so it's excluded from the historical value chart, though it is correctly included in realised P/L.
- **Risk-free rate assumed at 0% in the Sharpe ratio** — the formula divides excess return by volatility, where "excess" means return above what you'd get risk-free. Assuming 0% overstates the Sharpe ratio, since real risk-free rates aren't zero.
- **Small sample size for the risk metrics** — volatility, VaR, and Sharpe are calculated on roughly a year of daily data, from when the portfolio became substantially invested. This short window could shift meaningfully with more history, so these figures are a snapshot of one specific period rather than a stable long-run estimate.

## Tools

Python, pandas, matplotlib, yfinance, Jupyter Notebook

## Author

Aadarsh Sinnathambi — Financial Mathematics student, University College Cork
