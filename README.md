# MCP Tool Platform

Two Model Context Protocol servers and an orchestration layer that expose market analytics as callable tools for LLM agents and automated workflows. Agents never hold raw credentials. Every data or action path goes through a named, scoped tool.

## Why two servers

Live analytics requests are latency-sensitive. Backtesting is heavy, long-running, and unpredictable in load. Running them as separate servers means a large backtest cannot slow down a live request.

## Live analytics server

Tools are grouped by domain:

- **Market data.** Quotes, historical bars, and symbol lookup.
- **Account and execution support.** Holdings, positions, margin, and transaction-cost calculation. Order operations are gated behind explicit controls.
- **Options analytics.** Implied volatility and Greeks, strategy probability of profit, chain analytics, and implied-versus-historical volatility comparisons.
- **Fundamentals.** Company financial analysis from internal databases and external screener sources.
- **Macro and flow intelligence.** Institutional flows, block deals, and economic and earnings calendars.
- **Risk.** Position-level and book-level risk aggregation.
- **AI-assisted analytics.** Interpretation-heavy questions, such as volatility-surface shape or open-interest anomalies, are passed to a second model from a different provider. Its output is a cross-check, not the final word.
- **Communication.** Notifications and report dispatch.
- **Utilities.** Health checks and generated tool documentation.

## Backtesting server

Runs historical tests of options and equity strategies on real historical data. It is isolated from the live server, so batch jobs cannot affect live responsiveness.

## Orchestration layer

- Routes tool calls to the right server
- Lets an agent combine tools from both servers in one workflow
- Applies per-tool scoping and input validation before a call reaches a server
- Schedules recurring pipeline runs that depend on tool output

## Access and safety

- Tool-level scoping: an agent can call only the tools its role needs.
- No raw credentials in agent context. Credentials stay on the server side.
- Input validation at the tool boundary.
- Cross-checks by a second model on ambiguous analytics.

## Deployment

Both servers run as long-lived services with automatic restart. Changes go through local tests, then CI/CD, then automated deployment to AWS.

## Not included

Tool-by-tool specifications, schemas, endpoints, credentials, hostnames, and any client-specific configuration.
