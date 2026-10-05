# LLMOps and MLOps

The operations practices that keep LLM agents and analytics correct and observable in production. The focus is on what goes wrong with language models in production and how the system is built to catch it.

## Versioning

- **Prompts are version-controlled** alongside the pipelines that use them. A prompt change is reviewed and tested like any other code change.
- **Pinned model versions for decisions.** A floating model alias once resolved to different model versions inside a single production run. Decision-relevant steps now run on fixed versions, and changes to them are deliberate.
- **Explicit model roles.** A general model handles research. A pinned model handles decision steps. An independent second model handles cross-checks.

## Observability

- Per-run token and cost tracking, to catch unusual usage early
- Structured logs for every stage
- Health checks on services and data feeds
- Alerts sent through a channel that does not depend on the model provider

## Validation gates

- **Structured output checks.** Every model output is validated against its expected shape before any downstream step uses it.
- **Data-quality gates.** Runs do not start on incomplete inputs.
- **Deterministic rendering.** Formatting is fixed by templates, so output cannot drift from day to day.

## Reliability

- **Stateful reconciliation.** Positions and decisions are rebuilt from an append-only record, not from model memory.
- **Known failure modes.** Failures seen in production are written down and read at the start of each run.
- **Graceful failure.** Authentication or data failures produce a no-trade result or an alert. They do not produce a guessed answer.

## Delivery

- Work is developed and tested locally, pushed to GitHub, verified by CI/CD, and deployed to AWS automatically.
- Long-running services run under process supervision with automatic restarts.
- Scheduled production runs are monitored through the alerting described above.

## Not included

Pipeline definitions, prompts, configuration, credentials, and incident logs.
