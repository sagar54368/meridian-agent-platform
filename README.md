# LLMOps and MLOps

The operations practices that keep LLM agents, analytics, and data pipelines correct, observable, and cost-controlled in production. The focus is on what goes wrong with language models in production and how the system is built to catch it.

## What it can do

- Version-controls prompts alongside the pipelines that use them
- Pins model versions for decision-relevant steps, and keeps research steps on a general model
- Tracks token usage and cost for every scheduled run
- Validates structured output from each stage before the next stage uses it
- Gates scheduled runs on data completeness, so runs do not start on missing inputs
- Runs health checks on services
- Sends failure alerts through a channel that does not depend on the model provider
- Records known failure modes and reads them at the start of each run
- Builds and deploys automatically: changes are developed and tested locally, pushed to GitHub, and deployed through CI/CD to AWS EC2
- Keeps long-running services under process supervision with automatic restart
- Runs scheduled production jobs on cron and monitors them through the alerting above

## Why it's needed

Language models are nondeterministic, and model aliases can change under a running system. A production system needs reproducible decisions, visible costs, outputs that are checked before use, and an alert when something breaks. Deterministic rendering and fixed templates keep formatting from drifting. Reconciling state from records keeps a bad model answer from becoming a bad trade.

## What's inside

- **Prompt repository.** Versioned prompts under source control
- **Model configuration.** Fixed versions for decision steps, with separate roles for research, decisions, and cross-checks
- **Run logger.** Token usage and cost for each scheduled run
- **Output validators.** Structure checks on structured outputs
- **Data gates.** Pre-run checks on input completeness
- **Health checks.** Service checks
- **Alert module.** Independent alerting path
- **CI/CD pipeline.** Validates changes and deploys them to AWS EC2
- **Process supervision and scheduler.** Keeps services running and runs scheduled jobs
- **Failure memory.** Known failure modes, read at the start of each run

## Tech stack

- **Languages:** Python, Bash
- **AI tooling:** Anthropic Claude through Claude Code in headless mode, with JSON output for cost and token accounting
- **Version control:** Git and GitHub
- **CI/CD and deployment:** Pipeline triggered from GitHub, deploying to AWS EC2
- **Cloud:** AWS EC2 for services, AWS RDS for state
- **Scheduling and supervision:** cron, process supervision with automatic restart
- **Alerting:** Slack webhooks and email

## Design decisions

- **Pinned models for decisions.** A floating model alias once resolved to different model versions inside a single production run. Decision steps now use fixed versions, and changes to them are deliberate.
- **Explicit model roles.** A general model handles research, a pinned model handles decisions, and an independent model handles cross-checks.
- **Validation before use.** Structured output from each stage is checked before the next stage uses it.
- **Fail to a safe result.** Authentication or data failures produce a no-trade result or an alert, never a guessed answer.

## Not included

Pipeline definitions, prompts, configuration, credentials, and incident logs.
