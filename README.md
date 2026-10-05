# MCP Tool Platform

Two Model Context Protocol servers and an orchestration layer that expose market analytics, database operations, and client communications as callable tools for LLM agents and automated workflows. Agents do not hold raw credentials. Every data or action path goes through a named tool.

## What it can do

- **Real-time and historical market data.** Live ticks over WebSocket from Angel One SmartAPI, plus quotes, historical bars, and symbol lookup
- **Database management.** List tables, view tables, create tables, and insert data into the platform's databases
- **Account and execution support.** Holdings, positions, margin, and transaction-cost calculation. Order placement and modification tools exist and are used only when execution is enabled.
- **Options analytics.** Implied volatility and Greeks, probability of profit for option strategies, chain analytics, and implied-versus-historical volatility comparisons
- **Fundamentals.** Company financial analysis from internal databases and external screener sources
- **Macro and flow intelligence.** Institutional flows, bulk and block deals, and economic and earnings calendars
- **Risk.** Position-level and book-level risk aggregation
- **AI-assisted analytics.** Interpretation-heavy questions, such as volatility-surface shape or open-interest anomalies, are sent to a second model from a different provider as a cross-check
- **Backtesting.** Historical tests of options and equity strategies, on a separate server
- **Real-time client communication.** Notifications and report dispatch through Slack and email
- **Operations.** Health checks and generated tool documentation

## Why it's needed

Agents need controlled, auditable access to data, databases, and analytics. A single tool surface means multiple agents and clients share one implementation of broker and data access, instead of each building its own. Tool-level scoping limits what each agent can do. Splitting backtesting onto its own server means a large batch job cannot slow down live requests, and the same separation keeps live client communication responsive.

## What's inside

- **Live analytics server.** Long-running service for latency-sensitive tools, using Server-Sent Events transport
- **Backtesting server.** Separate service for heavy, long-running historical runs
- **WebSocket market-data client.** Maintains the live tick connection to Angel One SmartAPI
- **Shared analytics library.** Pricing, Greeks, technical indicators, and risk calculations used by both servers
- **Data access modules.** Market data, fundamentals, and flow and macro data
- **Database module.** Table management and reads and writes for portfolio and analysis data
- **Orchestration layer.** Routes calls to the right server, lets agents combine tools in one workflow, and applies per-tool scoping and input validation
- **Communication module.** Slack and email notifications
- **Tool registry.** Generates tool documentation from the registered tools

## Tech stack

- **Language:** Python
- **Protocol and frameworks:** Model Context Protocol, FastMCP, the official MCP Python SDK, FastAPI, Uvicorn
- **Market data:** Angel One SmartAPI, including its WebSocket feed
- **Numerics:** NumPy, pandas, SciPy
- **Methods:** Black-Scholes pricing and Greeks, root-finding for implied volatility, lognormal probability-of-profit calculations, Wilder-smoothed ADX, Bollinger Bands, VWAP, Ichimoku, Yang-Zhang volatility
- **AI:** Anthropic SDK (Claude), Google Gen AI SDK (Gemini)
- **Data:** PostgreSQL on AWS RDS, psycopg2
- **Parsing:** BeautifulSoup for public web pages, PDF text extraction
- **Auth:** Firebase Admin SDK
- **Communication:** Slack SDK and Bolt, email
- **Infrastructure:** AWS EC2, managed services with automatic restart, deployed through CI/CD

## Design decisions

- **Two servers, split by workload.** Live and batch work have different latency and load profiles.
- **Tool-level scoping.** Agents get access to named tools, not to databases or brokers directly.
- **Credentials stay outside agent context.** Agents call tools. They never receive the credentials those tools use.
- **Second-model cross-checks.** Ambiguous analytics are reviewed by an independent model.

## Not included

Tool-by-tool specifications, schemas, endpoints, credentials, hostnames, and client-specific configuration.
