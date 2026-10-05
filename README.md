# Reporting and Client Delivery

How analysis becomes a client-ready report, and how clients are kept informed in real time. The same rendering approach covers scheduled reports and on-demand analysis.

## What it can do

- Renders the daily market-intelligence output into a formal HTML report each weekday
- Delivers reports by email to a managed distribution list
- Produces on-demand portfolio analyses: pulls live portfolio and market data through the tool layer, produces a structured analysis with a language model, and renders it in the same format
- Sends real-time notifications to clients and the team through Slack and email
- Keeps formatting separate from analysis content, so a template change cannot alter the analysis and an analysis change cannot break the layout
- Sends failure alerts to the team, and an alert fires if the model provider's authentication fails, so a broken report is not discovered only by a client

## Why it's needed

Clients need reports that look the same every time and arrive on schedule. Clients also ask for analysis of their own portfolios, and that output needs the same care as the scheduled reports. Real-time notifications keep clients informed when something needs attention, rather than after the fact. Separating content from layout keeps both reliable.

## What's inside

- **Scheduled report renderer.** Turns structured daily output into a report
- **On-demand analysis path.** Pulls portfolio and market data, runs the analysis, and renders it
- **Templates.** Fixed HTML layouts for each report type
- **Email sender.** Delivers reports to the distribution list
- **Notification module.** Real-time Slack and email messages to clients and the team
- **Alert path.** Independent channel for failures, including authentication failures in the model provider

## Tech stack

- **Language:** Python
- **Templates:** HTML and CSS
- **AI:** Anthropic Claude for on-demand analysis content
- **Data:** MCP tool layer for portfolio and market data
- **Communication:** Email from Python, Slack SDK and webhooks
- **Infrastructure:** AWS EC2

## Design principles

- **Models produce content, templates control layout.** Formatting is tested separately from analysis.
- **Failures are visible.** Delivery and authentication problems alert the team instead of failing silently.

## Not included

Templates, recipient lists, client names, portfolio data, and sample reports.
