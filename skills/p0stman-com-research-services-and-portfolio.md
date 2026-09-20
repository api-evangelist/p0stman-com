---
name: Research p0stman's services and portfolio
description: Answer a user's questions about what p0stman builds, what it costs, how long it takes, and what it has shipped for a given industry, using only the provider's live MCP read tools.
api: mcp/p0stman-com-mcp.yml
endpoint: https://p0stman.com/api/mcp
operations: [get_services, get_portfolio, search_content]
side_effects: none
auth: none
generated: 2026-09-19
method: generated
---

# Research p0stman's services and portfolio

All three tools are read-only, anonymous JSON-RPC 2.0 calls to `POST https://p0stman.com/api/mcp`
(`Content-Type: application/json`). No `initialize` handshake is required, but sending one is harmless.

## Steps

1. **List the services.** Call `get_services` with `{}` (or `{"category": "..."}` to filter). The result is a
   single `content[0].text` block containing a JSON string with `services[]` - each has `slug`, `name`,
   `tagline`, `description`, `price_from` (GBP), `currency`, `timeline`, `url` and `subOffers[]`. Parse the
   text block as JSON before quoting prices.
2. **Pull case studies for the user's sector.** Call `get_portfolio` with `{"industry": "<sector>"}` (for
   example `hospitality`, `fintech`, `proptech`, `healthcare`) or `{}` for everything. Each `case_studies[]`
   entry carries `slug`, `title`, `industry`, `summary`, `tech[]`, `timeline`, `url`.
3. **Search the guides when the question is conceptual.** Call `search_content` with `{"query": "<terms>"}`;
   it returns `{query, services[], case_studies[], guides[]}`. An empty result set is normal - the index
   covers p0stman's own pages only.
4. **Cite the provider's URLs.** Every record includes a canonical `url`; link it rather than paraphrasing
   pricing. The same data is available without MCP at `GET https://p0stman.com/api/ai/services` and
   `GET https://p0stman.com/api/ai/portfolio` if the client cannot speak JSON-RPC.

## Rules

- Errors arrive as JSON-RPC error objects with HTTP 200; check `error.code` and `error.data` (an unknown tool
  name surfaces as `-32603` with `"Unknown tool: ..."` in `data`, not as `-32602`).
- There is no pagination and no rate limit is published; results are the whole set.
- Prices are "from" prices in GBP for fixed-scope services; do not present them as API pricing.
- The A2A equivalents are the `inquire` and `portfolio` skills of the "Zero" agent at
  `https://p0stman.com/api/agent` (text/plain in, text/plain out).
