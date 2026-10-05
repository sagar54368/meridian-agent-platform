# Daily Market Intelligence and Options Automation

A scheduled, multi-agent pipeline that runs every weekday morning on production servers. It researches current market conditions, produces derivatives-focused research, and delivers a formal report to clients. On a normal day, no one touches it.

## Problem it solves

Manual morning research takes time and varies from person to person. Clients need a report that arrives at the same time every day, covers the same areas in the same order, and is built on checked data. If the data is incomplete, the report should say so instead of guessing.

## How a run works

1. **Market intelligence.** An agent gathers context through the platform's tool layer and web search. The context covers market structure, volatility, institutional flows, open-interest changes, the event calendar, and macro or company news. The agent writes one structured market-state file. It does not write the report.
2. **Index derivatives.** A second agent reads the market-state file and the live position ledger. For the major index options, it produces either a defined-risk structure or an explicit no-trade result. A no-trade result is either a hold or a data failure, and both are valid outputs.
3. **Single-stock derivatives.** A third agent screens a candidate universe, ranks names by volatility relationships, applies event and liquidity filters, and proposes a small set of structures.
4. **Rendering and delivery.** A step with no model call maps the structured output onto a fixed template and sends it. Because the layout is fixed, formatting does not drift from day to day.

## Design decisions

- **One job per agent.** Each stage has a defined input and output contract. Stages cannot overwrite each other's outputs.
- **State comes from a ledger, not memory.** Before each decision step, current positions are rebuilt from an append-only record. A decision never depends on what a model remembers from earlier in the conversation.
- **Pinned models for decisions.** Research steps can use a general model. Steps that affect recommendations run on fixed model versions.
- **Separate rendering.** Models supply content. Templates supply layout.
- **Failures alert through another path.** If the model provider or the data feed fails, an alert goes out through a channel that does not depend on that provider.

## Operations

- Per-run token and cost tracking, so unusual cost or behavior shows up quickly
- Data-feed gap detection, with runs stopped or flagged when inputs are missing
- Holiday and weekend guards
- Known failure modes recorded and read at the start of each run

## Not included

Prompts, filters, thresholds, scoring rules, strategy logic, data sources, recipient lists, and output from real runs.
