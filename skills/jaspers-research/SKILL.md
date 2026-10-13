---
name: jaspers-research
description: Search and read one US-listed company's SEC filings, query a filing database (sections, Forms 3/4/5, 13D/G), and verify every quote against sec.gov with the Jaspers Research MCP server at https://analyst-api.jsprai.com/mcp/open. Use when asked what a company's 10-K, 10-Q, 8-K, proxy or insider filings say, to find language across a company's filings, to tabulate insider transactions, or to answer a research question with cited quotes.
license: MIT
metadata:
  author: Jaspers
  version: 1.0.0
  homepage: https://jsprai.com/agents.md
---

# Jaspers Research MCP

A hosted MCP server (name `jaspers-sec`) over SEC data: semantic and exact-term search across one US-listed company's filings, the structured content of each filing, a filing database split into sections with Forms 3, 4, 5 and 13D/G as structured fields, a screener over about 7,100 SEC 10-K filers, and a `show_citations` tool that finds each quote in its source and returns the sec.gov URL. Everything it serves is public SEC data; the value is reading it fast and citing it exactly.

Not for: 13F institutional holdings (those are on the Jaspers Screener MCP), prices, companies outside SEC EDGAR, or investment advice.

## Connect

- Endpoint: `https://analyst-api.jsprai.com/mcp/open`, MCP Streamable HTTP.
- Auth: a Jaspers Account access token as `Authorization: Bearer <token>`, scope `mcp`, issued by `https://account.jsprai.com` for the resource `https://analyst-api.jsprai.com/mcp/open`. Protected resource metadata: `https://analyst-api.jsprai.com/.well-known/oauth-protected-resource/mcp/open`. Authorization server metadata: `https://account.jsprai.com/.well-known/oauth-authorization-server` (authorization code with PKCE `S256`, refresh tokens, public clients, dynamic client registration, client ID metadata documents).
- A request without a valid token answers `401` with a `WWW-Authenticate: Bearer` challenge naming that metadata, so a client that follows RFC 9728 discovers the sign-in on its own. Inside Jaspers Terminal, the Research plugin connects on the user's sign-in with nothing to paste.
- Research is a subscription on the account, with a preview period on new accounts. Without it every tool is listed and every call answers that Research MCP needs a subscription; point the user to https://account.jsprai.com/auth/account.

## Tools

- Per company, each with a `ticker` and an optional `as_of_date` (YYYY-MM-DD): `search_filings` (semantic), `keyword_search_filings` (exact terms), `fetch_filings` (structured content per filing), `fetch_chunk`, and the filing database `query_filings`, `aggregate_filings`, `get_distinct_values`, `count_filings`. `get_company_status` follows a company's first load.
- Across the universe: `screener_field_stats`, `screen_companies`, `search_filing_text`, `list_filing_sections`, `fetch_filing_sections`, `list_insider_activity`, `screen_qualitative` (starts a job) and `screen_qualitative_status` (poll until done).
- `get_guide(topic)` and `show_citations`. The live `tools/list` is authoritative.

## Workflow

1. Call `get_guide(topic)` first: `"filing_database"` before the filing database tools, `"screener"` before the universe tools, `"sec_filings"` for fiscal periods.
2. A company loads on first use. Until something is loaded a tool answers with an error and an `ingest` status; while it loads, results lead with an `ingest` key saying which years are in. Follow with `get_company_status(ticker, wait_seconds=45)` until `phase` is `"complete"`. `"not_sec_filer"` means SEC EDGAR has no such ticker.
3. Search before reading: `search_filings` for a concept ("supply concentration", "going concern"), `keyword_search_filings` for exact terms or figures, then `fetch_chunk` or `fetch_filings` for the surrounding text.
4. For insider and ownership forms, use the filing database: Forms 3, 4, 5 and 144 are not in the search tools. `fetch_filings(form_types=["4"])` or `query_filings(form="4")`; `aggregate_filings` to total them. Form 4 `shares` are unsigned, so split buys from sales by `code` (`P` is an open-market buy, `S` a sale).
5. For questions across many companies, use the universe tools; screener values are raw units (fractions for margins and growth, absolute USD for dollar fields).
6. Before answering, call `show_citations` with the ids from each result (`document_id` plus `chunk_id`, or filing database `_id` plus `section_key`) and a 5 to 40 word quote copied exactly. It returns the sec.gov URL, the source's own words, and whether the quote verified. Cite universe results by the sec.gov filing link each returns.

## Reporting

- Every number and quote traces to a tool result; nothing from memory.
- Quote the filing's own words and name the form and date; say which fiscal period a figure is for.
- When a quote did not verify, say so or drop it, and never present an unverified paraphrase as a quotation.

## More

- Agent instructions for all of Jaspers: https://jsprai.com/agents.md
- Server card: https://jsprai.com/.well-known/mcp/research-server-card.json
- The same server inside Jaspers Terminal, as research rooms: https://github.com/JaspersAI/plugin-research
