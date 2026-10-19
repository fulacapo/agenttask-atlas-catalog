# AgentTask Atlas catalog (open data)

Open data behind [AgentTask Atlas](https://agenttaskatlas.com): a small, curated directory that answers one question for developers, *"which MCP server or Agent Skill should I install for this dev task?"*

- **Website:** https://agenttaskatlas.com
- **Remote MCP server (free, read-only, no auth):** `https://agenttaskatlas.com/api/mcp` — docs at https://agenttaskatlas.com/mcp
- **Official MCP Registry:** [`com.agenttaskatlas/catalog`](https://registry.modelcontextprotocol.io/v0.1/servers?search=agenttaskatlas)

Every entry (43 at the time of this export: 35 MCP servers, 8 Agent Skills) records the dev tasks it fits, which clients document it, the permissions and credentials it needs, the official install step, the source link, the license, the last verified date and a one-line practical verdict.

> **Honest caveat:** entries summarize official sources (READMEs, docs, release pages). Nothing is hands-on tested and there is **no independent security audit**. Review permissions and code at the source before installing anything.

## Files

| File | What it is |
| --- | --- |
| [`catalog.json`](catalog.json) | All entries, same data the website and the MCP server use. |
| [`categories.json`](categories.json) | Dev-task and entry-type categories with counts and URLs. |
| [`server.json`](server.json) | The `server.json` published to the Official MCP Registry. |
| [`LICENSE`](LICENSE) | CC BY 4.0 for the data in this repository. |

### Entry fields (`catalog.json`)

`slug`, `name`, `vendor`, `type` (`mcp-server` or `agent-skill`), `tasks` (category slugs), `agents` (clients with setup instructions in the official docs), `agentsSource`, `permissions`, `credentials`, `maintenance`, `lastActivity`, `install`, `installUrl`, `sourceUrl`, `license` (SPDX id as reported by GitHub, `NOASSERTION` when unclear), `verifiedOn`, `archived`, `verdict`, and optionally `release` (`tag`, `date`, `url`).

Each entry also has a page at `https://agenttaskatlas.com/entry/<slug>`.

## Use the catalog from your coding agent (remote MCP server)

The server speaks **Streamable HTTP**, is stateless, needs no account or API key, and only reads the public catalog. Tools:

- `search_catalog(query, category?, limit?)` — find entries for a task
- `get_entry(slug)` — full card for one entry
- `list_categories()` — all categories with counts

### Claude Code

From the [Claude Code MCP docs](https://code.claude.com/docs/en/mcp) (remote HTTP server):

```bash
claude mcp add --transport http agenttask-atlas https://agenttaskatlas.com/api/mcp
```

Add `--scope user` to make it available in all your projects. Check it with `claude mcp list` or `/mcp`.

### Claude Desktop / claude.ai

Remote servers are added as **custom connectors** ([Claude Help Center](https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp)): open **Customize → Connectors**, choose **Add → Custom connector** (on some layouts: **Add → Custom → Web**), enter a name and the URL `https://agenttaskatlas.com/api/mcp`, and pick **No sign in** for authentication. Free plans allow one custom connector.

If you prefer the local config file (`claude_desktop_config.json`, stdio only), bridge it with the community [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) package:

```json
{
  "mcpServers": {
    "agenttask-atlas": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://agenttaskatlas.com/api/mcp"]
    }
  }
}
```

### Cursor

From the [Cursor MCP docs](https://cursor.com/docs/context/mcp): add this to `~/.cursor/mcp.json` (global) or `.cursor/mcp.json` (project):

```json
{
  "mcpServers": {
    "agenttask-atlas": {
      "url": "https://agenttaskatlas.com/api/mcp"
    }
  }
}
```

### VS Code (GitHub Copilot)

From the [VS Code MCP docs](https://code.visualstudio.com/docs/copilot/customization/mcp-servers): run **MCP: Add Server** from the Command Palette, choose **HTTP**, paste `https://agenttaskatlas.com/api/mcp` and give it an ID such as `agenttask-atlas`.

Or edit a config file ([format reference](https://code.visualstudio.com/docs/copilot/reference/mcp-configuration)). VS Code format, `.vscode/mcp.json`:

```json
{
  "servers": {
    "agenttask-atlas": {
      "type": "http",
      "url": "https://agenttaskatlas.com/api/mcp"
    }
  }
}
```

Portable Copilot format, workspace-root `.mcp.json` or `~/.copilot/mcp-config.json`:

```json
{
  "mcpServers": {
    "agenttask-atlas": {
      "type": "http",
      "url": "https://agenttaskatlas.com/api/mcp"
    }
  }
}
```

### Try it with curl

```bash
curl -s https://agenttaskatlas.com/api/mcp \
  -H 'content-type: application/json' -H 'accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"search_catalog","arguments":{"query":"postgres"}}}'
```

## Corrections and suggestions

Found an outdated install step, a wrong license, or a server that should be listed? Open an issue in this repository. Listing is free; sponsorship never changes verdicts.

## License

The data in this repository (`catalog.json`, `categories.json`, `server.json`, this README) is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Attribution: "AgentTask Atlas (https://agenttaskatlas.com)". Product names, logos and the software each entry links to belong to their respective owners and keep their own licenses.

This repository contains only exported data, not the website's source code.
