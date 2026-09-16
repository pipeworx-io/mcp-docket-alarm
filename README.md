# mcp-docket-alarm

Docket Alarm MCP — state + county + federal court dockets (docketalarm.com)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `docketalarm_search` | Search court dockets (state + federal) for `<query>` — full-text search across 675M+ US state, county, and federal court dockets (deeper state coverage than CourtListener). Returns matching cases with court, docket number, title, filing date, and link. Example: docketalarm_search({ q: "patent infringement Apple", limit: 20, _apiKey: "user@example.com:yourpassword" }) |
| `docketalarm_get_docket` | Get full docket detail for a case — returns case title, parties, filing date, and docket entries for a specific court + docket number. Cached documents are free. Example: docketalarm_get_docket({ court: "U.S. District Court, N.D. California", docket: "5:11-cv-01846", _apiKey: "user@example.com:yourpassword" }) |
| `docketalarm_search_pacer` | Search federal PACER dockets by party — live party-name search across federal PACER (Appellate, Bankruptcy, Civil, Criminal, MDL). Note: this passes live PACER search fees at cost. Returns matching federal cases with court, docket, title, and filing date. Example: docketalarm_search_pacer({ party_name: "Microsoft Corporation", _apiKey: "user@example.com:yourpassword" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "docket-alarm": {
      "url": "https://gateway.pipeworx.io/docket-alarm/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/docket-alarm/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1576+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "docket-alarm": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-docket-alarm"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-docket-alarm
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Docket Alarm data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
