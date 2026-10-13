---
name: jaspers-screener
description: Screen US public companies and read their SEC filings against criteria with the Jaspers Screener MCP server at https://s.jsprai.com/mcp. Use when asked to find US companies by fundamentals, valuation, index membership, institutional (13F) or insider ownership, to look up one company's figures and holders, or to test yes/no criteria against 10-K and 10-Q filings with verified, cited quotes.
license: MIT
metadata:
  author: Jaspers
  version: 1.0.0
  homepage: https://jsprai.com/agents.md
---

# Jaspers Screener

A hosted MCP server over about 7,100 US SEC registrants (companies that file 10-K and 10-Q): latest-fiscal-year fundamentals and valuation, technicals, index membership, 13F institutional holders, insider trades from Forms 3, 4 and 5, and each company's newest 10-K and 10-Q. It screens by numbers, then reads the survivors' filings against criteria written in words and answers with quotes verified against the filing text.

Not for: trade execution, companies outside the US SEC universe, tick-level data, or investment advice.

## Connect

- Endpoint: `https://s.jsprai.com/mcp`, MCP Streamable HTTP.
- Auth: OAuth 2.0 bearer token with scope `mcp`. An unauthenticated request answers `401` with `WWW-Authenticate: Bearer ... resource_metadata="https://s.jsprai.com/.well-known/oauth-protected-resource/mcp"`.
- The protected resource metadata names the authorization server `https://account.jsprai.com`. Its metadata is at `https://account.jsprai.com/.well-known/oauth-authorization-server`: authorization code grant with PKCE `S256`, refresh tokens, public clients, dynamic client registration at `/oauth/register`, and client ID metadata documents (`client_id_metadata_document_supported: true`).
- Send `Authorization: Bearer <access_token>` on every request. A Jaspers account is created on first sign-in. Use is metered; without an entitlement every tool is listed but answers `NEEDS_SUBSCRIPTION`. Point the user to https://account.jsprai.com/ in that case.

## Tools

`get_guide`, `get_company`, `screen_companies`, `start_qualitative_screen`, `get_qualitative_screen`, `read_evidence`, `start_options_screen`, `get_options_screen`, `follow_run`, `cancel_run`. The live `tools/list` is authoritative; read each tool's description before calling it.

## Workflow

1. Call `get_guide("overview")` once before answering about coverage, freshness, or caveats, and `get_guide("screening")` before the first screen. `get_guide("fields")` explains any field.
2. Screen with `screen_companies`. Filters apply to any field. Narrow over several calls in one session by passing back its `session_id`; `filters` is always the complete set, not a delta. Stop when the result says `qualitative_ready`: the list is small enough to read.
3. For criteria in words, agree them with the user first, then `start_qualitative_screen`. Each criterion is a yes/no question about the company, such as "Does the company depend on a sole-source supplier?" The server reads every company's newest 10-K and 10-Q and answers pass, fail, or unclear, with verified quotes.
4. Follow the run with `get_qualitative_screen` and a long `wait_seconds`, telling the user what comes in as it comes in. If the client shows the run in its own view, leave the view alone. `follow_run` and `cancel_run` manage a run in progress.
5. `read_evidence` returns the quotes behind a verdict, each citing the filing on sec.gov. `get_company` gives one company's figures, holders, and insider activity.
6. `start_options_screen` and `get_options_screen` screen listed options; that data is from Databento.

## Conventions

- Tickers as the SEC writes them: `BRK-B`, not `BRK.B`.
- USD in raw units. Margins, growth, and ownership are fractions: `0.25` means 25%.
- Dates are ISO. `null` means not reported, never zero.
- Say which period a figure is for: `fiscal_period_end` for fundamentals, `price_date` for prices, `inst_period` for holders, `trade_date` for options.
- Sources: SEC filings for figures, Yahoo Finance for prices, Databento for options. Report only what a cited source returned; do not fill gaps from memory or elsewhere.

## More

- Agent instructions for all of Jaspers: https://jsprai.com/agents.md
- Server card: https://jsprai.com/.well-known/mcp/server-card.json
- The same server inside Jaspers Terminal: https://github.com/JaspersAI/plugin-screener-mcp
