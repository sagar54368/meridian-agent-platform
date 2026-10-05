# AI Portfolio Management

A multi-agent system that starts with a universe of stocks, analyzes each stock on its own across technical, fundamental, news, and risk dimensions, and combines those views into one composite score. It then maintains a live portfolio that is re-evaluated every cycle, not only at entry.

## Flow of one cycle

1. **Universe.** The starting list is an index, a custom watchlist, or the current holdings of a portfolio under review. Nothing outside the universe is analyzed.
2. **Ingestion.** Market, fundamental, and news data are pulled, cleaned, and stored, so agents read from one consistent dataset.
3. **Parallel analysis.** Four agents run on every stock at the same time:
   - *Technical.* Indicators across daily, four-hour, and one-hour views, plus a check for agreement across timeframes. Conflicting timeframes lower the score.
   - *Fundamental.* Quality, growth, and valuation, weighted toward trend direction rather than single snapshots.
   - *News and sentiment.* A language model reads recent news and returns a directional view with a stated confidence and reasoning. A separate read covers the market regime for the whole portfolio.
   - *Risk.* Value at risk, each position's contribution to total portfolio risk, and correlation with the rest of the book.
4. **Composite scoring.** Each stock gets one score built from weighted components and mapped to a tier. Every point traces back to a specific agent output.
5. **Decisions.** Each stock gets a recommendation relative to the book, not in isolation.
6. **Portfolio construction.** Positions are sized, checked for unintended concentration, and tested against a portfolio-level risk gate before anything is finalized.
7. **Position management.** Each holding is reconciled against the new analysis and marked as new, hold, reduce, or exit. The reconciliation runs against a persisted position state, so a deteriorating position cannot be ignored.
8. **Output.** A cycle report with the full portfolio, each position's score breakdown, and portfolio statistics. Execution is optional and separate from analysis, so the analysis can run without placing any orders.

## Design principles

- **No hardcoded thresholds in scoring.** Components are measured relative to peers, to history, and to trend, so the system does not need retuning each time the market regime changes.
- **Risk is part of the score.** A position cannot score well on upside alone. Its contribution to portfolio risk counts in the same score.
- **Multi-timeframe confirmation.** A stock can look strong on a daily chart and weak on an hourly one. The score rewards agreement across timeframes.
- **State is reconciled, never assumed.** Each cycle rebuilds current state from a persisted record.
- **Analysis and execution are separate.** An auditable recommendation exists whether or not anything acts on it.

## Validation

Validated through cross-checks between independent agents on the same instruments, out-of-sample testing, and review of each agent's output quality. Results are not published.

## Not included

Weights, tier boundaries, indicator parameters, prompts, data sources, portfolio contents, and performance results.
