# Vett Compliance MCP Server

Read-only [Model Context Protocol](https://modelcontextprotocol.io) tools for the Vett
compliance API — sanctions/PEP/watchlist screening, product recalls, US business lookup, and
federal contract search. Free tier: **20 calls/hour per IP**, no API key required.

Hosted at `https://wimberly.solutions/mcp` (streamable HTTP transport). No installation —
point any MCP client at the URL below.

> This repo is the public discovery/documentation surface for the hosted server. The
> screening logic itself lives in a private repo alongside the rest of the Vett API — this
> is intentionally a docs-only repo, not a self-hostable server.

## Tools

| Tool | What it does |
|---|---|
| `screen_name` | Screen a person/company against 12 sanctions, PEP, watchlist, and recall databases (OFAC SDN, UK OFSI, UN SC, US CSL, EU FSF, Canada SEMA, Australia DFAT, US PEP, FBI Most Wanted, World Bank debarred, US watchlists, product recalls) |
| `screen_wallet` | Check a crypto wallet address (BTC/ETH/XMR/etc.) against OFAC's sanctioned-address list |
| `check_recall` | Check a product/manufacturer against CPSC/FDA/NHTSA recalls |
| `lookup_business` | Look up a US business in the SAM.gov entity registry (UEI, CAGE code, NAICS) |
| `search_gov_contracts` | Search active US federal contract solicitations by keyword/NAICS |
| `list_status` | Freshness + entry counts for every source `screen_name` fans out to |

All tools are read-only — no mutations, no PII beyond what's already public record.

## Claude Desktop

Add to your MCP config (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "vett-compliance": {
      "url": "https://wimberly.solutions/mcp"
    }
  }
}
```

## Claude Code

```bash
claude mcp add --transport http vett-compliance https://wimberly.solutions/mcp
```

## Generic MCP client (streamable HTTP)

```python
from mcp import ClientSession
from mcp.client.streamable_http import streamablehttp_client

async with streamablehttp_client("https://wimberly.solutions/mcp") as (read, write, _):
    async with ClientSession(read, write) as session:
        await session.initialize()
        result = await session.call_tool("screen_name", {"query": "some name"})
```

## Rate limits

20 calls/hour per IP address, unauthenticated. Exceeding it returns a 429 with an upgrade
hint pointing at the full [RapidAPI listing](https://rapidapi.com/Gorp405/api/complete-compliance-suite)
(higher limits, more sources, API key auth).

## About

Built and operated by [Wimberly Solutions](https://wimberly.solutions). Questions or issues:
open a GitHub issue on this repo.
