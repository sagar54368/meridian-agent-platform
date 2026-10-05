# Reporting and Client Delivery

How analysis becomes a client-ready report. The same rendering approach covers scheduled reports and on-demand analysis.

## Scheduled reports

Each weekday, the daily market-intelligence pipeline produces structured output. A fixed HTML template renders that output into a report, and the report is delivered by email to the distribution list.

## On-demand portfolio analysis

A client or internal user can request an analysis of a portfolio. The system:

1. Pulls current portfolio and market data through the MCP tool layer.
2. Produces a structured analysis with a language model.
3. Validates the structure.
4. Renders the result in the same report format as the scheduled reports.

## Rendering principle

Models produce content. Templates control layout. Formatting is tested separately from analysis content, so a change to the analysis cannot break the report's structure, and a template change cannot alter the analysis.

## Delivery and alerting

- Distribution lists are managed outside the codebase.
- Delivery failures trigger an alert to the team.
- A fallback path reports failures in rendering or delivery, so a report never goes out silently broken.

## Not included

Templates, recipient lists, client names, portfolio data, and sample reports.
