# Data Platform

The ingestion, normalization, and storage layer that feeds every agent and tool in the platform. It handles real-time and historical data only. No synthetic data is used anywhere in the pipeline.

## Sources

- **Market data.** Real-time and historical prices and bars from brokerage APIs.
- **Fundamentals.** Company financials and ratios from internal databases, supplemented by external screener sources.
- **Flows and block deals.** Institutional flow data and bulk and block-deal data from external market-data providers.
- **News and events.** News articles and macro or company events from news sources.

## Storage

Data is stored in several purpose-specific databases: price history, fundamentals, risk and portfolio state, and news and events. Downstream agents read from these internal stores. They do not call external sources directly, so every agent in a cycle works from the same cleaned snapshot.

## Ingestion design

- **Rate-limited clients.** API calls respect provider limits through request-window control.
- **Throttled collection.** Public sources are collected at a slow, polite rate.
- **Normalized schemas.** Data from different providers is mapped to one schema before storage.
- **Caching by data type.** Live quotes expire within minutes, historical bars within hours, and fundamentals within a day or more. Slow-changing data is not refetched every cycle.
- **Validation before use.** Data-quality checks run before each cycle. Missing or invalid inputs stop the run or flag it, rather than letting a model fill in the gap.

## Operations

- Scheduled refresh jobs
- Gap detection for missing feeds, with alerts
- Separate handling for live and historical workloads, so large historical loads do not slow live reads

## Not included

Schemas, table definitions, query logic, hostnames, provider credentials, and the contents of any dataset.
