# Daily Market Intelligence and Options Automation

A scheduled, multi-agent pipeline that runs every weekday morning on production servers. It researches current market conditions, produces derivatives-focused research, and delivers a formal report to clients. On a normal day, no one touches it.

## What it can do

- Runs unattended Monday to Friday, skipping weekends and market holidays
- Gathers market context through tools and web search, and writes one structured market-state file
- Uses live market data from the platform's real-time feed through the tool layer
- Produces a defined-risk index options recommendation, or an explicit hold or data-failure result when a trade is not warranted
- Screens single-stock options candidates, ranks them by volatility relationships, and applies liquidity and event filters
- Tracks open positions across runs, so new trades and existing holdings are handled differently
- Renders a formal HTML report and emails it to the client distribution list
- Sends a fallback Slack alert if a run fails on something like an authentication problem

## Why it's needed

Manual morning research takes time and varies by analyst. A scheduled pipeline gives clients a report at the same time every day, covering the same areas in the same order. Built-in checks mean missing data produces a no-trade result or a flag, never an invented recommendation.

## What's inside

1. **Orchestrator.** A shell script that runs the stages in order, applies the holiday and weekend guard, and handles errors.
2. **Market intelligence stage.** An agent that gathers context and writes the market-state file. It does not format anything.
3. **Index options stage.** An agent that reads the market state and current positions, then returns a structure or a no-trade result. Runs on a pinned model.
4. **Single-stock options stage.** An agent that screens and ranks candidates and proposes a small set of structures. Runs on a pinned model.
5. **Position-state builder.** Rebuilds current positions from the trade ledger before each decision stage.
6. **Trade ledger.** An append-only record that is the source of truth for positions.
7. **Render and send stage.** Maps structured output onto a fixed template and sends it. This stage makes no model call.
8. **Failure memory.** Known failure modes and data-feed gaps, read at the start of each run.
9. **Alert path.** Sends failures to the team through a channel that does not depend on the model provider.

## Tech stack

- **Languages:** Python (stage logic, state builder, renderer, sender), Bash (orchestration), SQL (state queries)
- **AI:** Anthropic Claude, run in headless mode through Claude Code with JSON output for per-run token and cost accounting
- **Tools:** Model Context Protocol (MCP) tools from the platform's tool layer, plus web search for breaking news
- **Market data:** Angel One SmartAPI, through the platform's real-time feed
- **Data store:** PostgreSQL on AWS RDS for position state
- **Delivery:** HTML templates, email from Python, Slack webhooks for alerts
- **Infrastructure:** AWS EC2 Linux server, cron scheduling, deployed through CI/CD

## Design decisions

- **One job per agent.** Each stage has a defined input and output contract, and stages cannot overwrite each other's outputs.
- **State comes from a ledger, not memory.** Positions are rebuilt from the append-only record before each decision.
- **Pinned models for decisions.** Research can use a general model. Steps that affect recommendations run on fixed versions.
- **Separate rendering.** Models supply content, templates supply layout.
- **Independent failure path.** Alerts do not depend on the component that failed.

## Not included

Prompts, filters, thresholds, scoring rules, strategy logic, data sources, recipient lists, and output from real runs.
