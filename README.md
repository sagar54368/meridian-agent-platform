# AI Portfolio Management

A multi-agent system that starts with a universe of stocks, analyzes each stock on its own across technical, fundamental, news, and risk dimensions, and combines those views into one composite score. It then maintains a live portfolio that is re-evaluated every cycle, not only at entry.

## What it can do

- Builds a stock universe from an index, a custom watchlist, or the current holdings of a portfolio under review
- Analyzes each stock across four dimensions: technical, fundamental, news and sentiment, and risk
- Checks technical agreement across daily, four-hour, and one-hour views, and lowers the score when timeframes conflict
- Reads recent news with a language model and returns a directional view with stated confidence and reasoning
- Reads the market regime for the whole portfolio, not only for single stocks
- Measures value at risk at two confidence levels, and each position's contribution to portfolio risk
- Produces one composite score per stock, mapped to a tier, with every point traceable to an agent output
- Gives each stock a recommendation relative to the existing book
- Sizes positions, checks for unintended concentration, and tests the proposed portfolio against a portfolio-level risk gate
- Reconciles each holding as new, hold, reduce, or exit against a persisted position state
- Produces a structured cycle report with the full portfolio, score breakdowns, and an audit trail
- Runs execution optionally, separate from analysis

## Why it's needed

A single-number stock screen misses timing and portfolio context. Running separate analyses in parallel, then combining them with risk inside the score, gives a more complete view. Maintaining the portfolio from persisted state, instead of rebuilding it each cycle, avoids churn and duplicate entries. Keeping analysis separate from execution means every recommendation is auditable whether or not anything acts on it.

## What's inside

1. **Universe loader.** Builds the starting list for each cycle.
2. **Ingestion and cache.** Pulls market, fundamental, and news data from the data platform.
3. **Technical agent.** Indicators on three timeframes, with a confluence module.
4. **Fundamental agent.** Quality, growth, and valuation, weighted toward trend direction.
5. **News and sentiment agent.** Language-model read per stock, plus a portfolio-level regime read.
6. **Risk agent.** Value at risk, marginal contribution to portfolio risk, and correlation with the existing book.
7. **Scoring engine.** Combines component scores into one number and maps it to a tier.
8. **Decision layer.** Converts tiers into recommendations relative to current holdings.
9. **Portfolio constructor.** Sizing, diversification check, and risk gate.
10. **Position state store and reconciler.** Persisted holdings, reconciled every cycle.
11. **Reporting.** Structured cycle reports and audit output.

## Tech stack

- **Language:** Python
- **Numerics and statistics:** pandas, NumPy, SciPy
- **Technical analysis:** Indicators computed in-house, including moving averages, ADX, Bollinger Bands, VWAP, Ichimoku, and Yang-Zhang volatility
- **Risk:** Value at risk, correlation analysis, marginal risk contribution
- **AI:** Anthropic Claude for news and regime interpretation
- **Data access:** The platform's MCP tool layer and internal stores
- **Database:** PostgreSQL for position state

## Design principles

- **No hardcoded thresholds in scoring.** Components are measured relative to peers, to history, and to trend, so the system does not need retuning each time the regime changes.
- **Risk is part of the score.** A position cannot score well on upside alone. Its contribution to portfolio risk counts in the same score.
- **Multi-timeframe confirmation.** A stock can look strong on a daily chart and weak on an hourly one. The score rewards agreement.
- **State is reconciled, never assumed.** Each cycle rebuilds current state from a persisted record.
- **Analysis and execution are separate.**

## Validation

Validated by checking agreement between independent agents on the same instruments, and by reviewing the quality of each agent's output. Results are not published.

## Not included

Weights, tier boundaries, indicator parameters, prompts, data sources, portfolio contents, and performance results.
