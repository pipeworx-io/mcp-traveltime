# mcp-traveltime

TravelTime MCP — multi-modal isochrones & reachability incl. PUBLIC TRANSPORT

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `traveltime_isochrone` | What area is reachable within N minutes by public transport (or driving/walking/cycling) from a point (isochrone / travel-time polygon). Unlike road-only routing, this supports public_transport. Returns the reachable-area polygon(s). Example: traveltime_isochrone({ lat: 51.5072, lon: -0.1276, travel_time: 30, minutes: true, transport: "public_transport", departure_time: "2026-07-06T08:30:00Z", _apiKey: "appId:apiKey" }) |
| `traveltime_time_filter` | Which of these locations are reachable within N minutes from an origin by public transport (or driving/walking/cycling), and how long each takes. Filters candidate destinations by travel time. Example: traveltime_time_filter({ origin_lat: 51.5072, origin_lon: -0.1276, destinations: [{ id: "a", lat: 51.52, lon: -0.09 }, { id: "b", lat: 51.60, lon: -0.20 }], travel_time: 45, minutes: true, transport: "public_transport", departure_time: "2026-07-06T08:30:00Z", _apiKey: "appId:apiKey" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "traveltime": {
      "url": "https://gateway.pipeworx.io/traveltime/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/traveltime/mcp` returns the tools in the table
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

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/traveltime_isochrone`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "traveltime": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-traveltime"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-traveltime
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Traveltime data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
