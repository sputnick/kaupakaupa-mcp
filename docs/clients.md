# Connecting a client to the KaupaKaupa MCP server

The server address is the same for every client:

```
https://mcp.kaupakaupa.is/mcp
```

No sign-in, API key or OAuth is needed. Client menus change often; each section names the page it was checked against and the date.

## Quick test

MCP Inspector (needs Node.js; see the Inspector docs for the minimum version):

```bash
npx @modelcontextprotocol/inspector --server-url https://mcp.kaupakaupa.is/mcp --transport http
```

Or start it with no arguments (`npx @modelcontextprotocol/inspector`) and choose Streamable HTTP and the URL in the UI. Then list the tools and call `search_products` with `{"query": "kaffivel"}`.

One-line check with curl (the `Accept` header must list both types):

```bash
curl -sS -X POST https://mcp.kaupakaupa.is/mcp -H 'Content-Type: application/json' -H 'Accept: application/json, text/event-stream' -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{},"clientInfo":{"name":"curl","version":"0"}}}'
```

A JSON reply with `serverInfo`, the negotiated `protocolVersion` and a `tools` capability means it works (`tools/list` on the session lists the tools). The reply carries an `Mcp-Session-Id` header (add `-i` to see it).

## Claude.ai and Claude Desktop (custom connectors)

1. Open Settings, then Connectors (in the current Claude.ai UI this is Customize > Connectors).
2. Click Add, then "Add custom connector".
3. Enter a name and the URL `https://mcp.kaupakaupa.is/mcp`, then Continue.
4. Under Authentication choose "No sign in", then Add.
5. In a chat, click the "+" at the lower left, then Connectors, and switch the connector on.

Custom connectors are available on Free (one connector), Pro, Max, Ultra, Team and Enterprise plans. On Team and Enterprise only owners can add a connector; members then connect to it individually. The connection comes from Anthropic's servers, so the server must be reachable on HTTPS port 443 (it is). Claude Desktop uses the same Connectors page; the docs give no separate menu path, and the menu names can differ slightly.

Source: https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp , https://modelcontextprotocol.io/docs/develop/connect-remote-servers (checked 2026-11-23)

## Claude Code

```bash
claude mcp add --transport http kaupakaupa https://mcp.kaupakaupa.is/mcp
```

Add `--scope project` to write it to `.mcp.json` for your team, or `--scope user` for all your projects. The default scope is local (this project, only you). Project file:

```json
{
  "mcpServers": {
    "kaupakaupa": {
      "type": "http",
      "url": "https://mcp.kaupakaupa.is/mcp"
    }
  }
}
```

An entry with `url` but no `type` is skipped. Claude Code asks you to approve project-scoped servers on first use.

Source: https://code.claude.com/docs/en/mcp (checked 2026-11-23)

## ChatGPT

ChatGPT has changed this flow. The current docs describe adding it as a custom MCP server in Plugins, on the web only:

1. Go to Plugins (chatgpt.com/plugins), select the plus button, then "Add custom MCP server".
2. Enter a name and an optional description.
3. Under Connection, enter `https://mcp.kaupakaupa.is/mcp`.
4. Set authentication to "No authentication".
5. Tick "I understand and want to continue", then "Create as a plugin" and review the discovered tools.
6. Install the plugin, start a new chat, type `@` and pick it.

Developer Mode is not required for this flow, per the current help article. Plan requirements: on Business only admins and owners can create custom MCP server plugins; on Enterprise/Edu admins and owners can create them and only they can publish; Pro users can connect custom servers with read/fetch permissions only (this server is read-only). The article does not address Plus. It is web only, not mobile. The help article says there are no geo restrictions; availability in your country and workspace still depends on your plan and workspace settings, so check there if the option is missing. Both SSE and streaming HTTP are supported.

Source: https://help.openai.com/en/articles/12584461-developer-mode-apps-and-full-mcp-connectors-in-chatgpt-beta , https://developers.openai.com/api/docs/guides/developer-mode (checked 2026-11-23)

## OpenAI Codex CLI

Add to `~/.codex/config.toml` (or `.codex/config.toml` in a trusted project):

```toml
[mcp_servers.kaupakaupa]
url = "https://mcp.kaupakaupa.is/mcp"
```

`codex mcp list` shows the result. The docs show `codex mcp add <name> --url <address>` only in an OAuth example, so use the config file for a no-auth server. The docs say Codex connects without authentication when no credential source resolves.

Source: https://learn.chatgpt.com/docs/extend/mcp?surface=cli (checked 2026-11-23)

## Gemini CLI

In `~/.gemini/settings.json` or the project's `.gemini/settings.json` (file locations from memory, not re-checked):

```json
{
  "mcpServers": {
    "kaupakaupa": {
      "httpUrl": "https://mcp.kaupakaupa.is/mcp"
    }
  }
}
```

Use `httpUrl`, not `url`: `url` means the SSE transport. Or run `gemini mcp add --transport http kaupakaupa https://mcp.kaupakaupa.is/mcp`.

Source: https://raw.githubusercontent.com/google-gemini/gemini-cli/main/docs/tools/mcp-server.md (checked 2026-11-23)

## Cursor

`.cursor/mcp.json` in the project, or `~/.cursor/mcp.json` for all projects:

```json
{
  "mcpServers": {
    "kaupakaupa": {
      "url": "https://mcp.kaupakaupa.is/mcp"
    }
  }
}
```

Source: https://cursor.com/docs/context/mcp (checked 2026-11-23)

## VS Code with GitHub Copilot

Run "MCP: Add Server" from the Command Palette, choose HTTP, enter the URL and a server ID. Or create `.vscode/mcp.json`:

```json
{
  "servers": {
    "kaupakaupa": {
      "type": "http",
      "url": "https://mcp.kaupakaupa.is/mcp"
    }
  }
}
```

The docs do not say "agent mode" in the pages checked; they say to pick Copilot as the session target before connecting a server. The same docs recommend a portable `mcpServers` format in a workspace-root `.mcp.json` for new Copilot setups; that format was not checked here.

Source: https://code.visualstudio.com/docs/copilot/customization/mcp-servers , https://code.visualstudio.com/docs/agents/reference/mcp-configuration (checked 2026-11-23)

## Windsurf

Not confirmed. Windsurf's MCP docs now redirect to Devin's docs, and the pages checked did not show a remote-server JSON example for Windsurf. Use the in-app MCP settings (Cascade MCP panel) to add a remote server by URL, or check https://docs.devin.ai/desktop/cascade/mcp . Devin's own config files use `url` with `"transport": "http"`:

```json
{
  "mcpServers": {
    "kaupakaupa": { "url": "https://mcp.kaupakaupa.is/mcp", "transport": "http" }
  }
}
```

That block is from Devin CLI's docs and is not confirmed for Windsurf.

Source: https://docs.devin.ai/desktop/cascade/mcp , https://docs.devin.ai/cli/extensibility/mcp/configuration.md (checked 2026-11-23)

## Zed

Settings, then AI, then MCP Servers, then Add Server, then "Add Remote Server". Or in `settings.json`:

```json
{
  "context_servers": {
    "kaupakaupa": {
      "url": "https://mcp.kaupakaupa.is/mcp"
    }
  }
}
```

Without an `Authorization` header Zed may try an OAuth flow; the server does not ask for one. A green dot next to the name means it is active.

Source: https://zed.dev/docs/ai/mcp (checked 2026-11-23)

## Cline, Roo Code and Continue

All three support remote Streamable HTTP.

Cline: `cline_mcp_settings.json` (IDE: Configure tab, Configure MCP Servers; or the Remote Servers tab, choose Streamable HTTP). Set `type` explicitly; leaving it out defaults to the legacy `sse` transport.

```json
{
  "mcpServers": {
    "kaupakaupa": {
      "type": "streamableHttp",
      "url": "https://mcp.kaupakaupa.is/mcp"
    }
  }
}
```

Roo Code: `mcp_settings.json` (global) or `.roo/mcp.json` (project), `type` must be `"streamable-http"`:

```json
{
  "mcpServers": {
    "kaupakaupa": {
      "type": "streamable-http",
      "url": "https://mcp.kaupakaupa.is/mcp"
    }
  }
}
```

Continue: a YAML file in `.continue/mcpServers/` (standalone files also need `name`, `version` and `schema` fields). MCP works only in agent mode.

```yaml
mcpServers:
  - name: kaupakaupa
    type: streamable-http
    url: https://mcp.kaupakaupa.is/mcp
```

If a tool only supports stdio, use the bridge, which runs locally and forwards to the URL:

```bash
npx mcp-remote https://mcp.kaupakaupa.is/mcp
```

As a stdio entry: `"command": "npx", "args": ["mcp-remote", "https://mcp.kaupakaupa.is/mcp"]`. The `mcp-remote` docs were not checked for this guide.

Source: https://docs.cline.bot/mcp/adding-and-configuring-servers , https://roocodeinc.github.io/Roo-Code/features/mcp/using-mcp-in-roo , https://docs.continue.dev/customize/deep-dives/mcp (checked 2026-11-23)

## LM Studio and other local-model hosts

LM Studio: right sidebar, Program tab, Install, "Edit mcp.json", then add:

```json
{
  "mcpServers": {
    "kaupakaupa": {
      "url": "https://mcp.kaupakaupa.is/mcp"
    }
  }
}
```

If you paste into an existing file, copy only the inner entry, not a second outer `mcpServers` wrapper. The model you load must support tool calling. Other local hosts: not confirmed; most accept the same `mcpServers` + `url` shape, otherwise use `mcp-remote`.

Source: https://lmstudio.ai/docs/app/mcp (checked 2026-11-23)

## Developers

### OpenAI Responses API

```python
from openai import OpenAI

client = OpenAI()
resp = client.responses.create(
    model="YOUR_MODEL",
    tools=[{
        "type": "mcp",
        "server_label": "kaupakaupa",
        "server_url": "https://mcp.kaupakaupa.is/mcp",
        "require_approval": "never",
    }],
    input="Find the cheapest 55 inch TV in Iceland and list the shops.",
)
print(resp.output_text)
```

The API supports Streamable HTTP and HTTP/SSE servers. Replace `YOUR_MODEL` with a model you have access to.

Source: https://developers.openai.com/api/docs/guides/tools-connectors-mcp (checked 2026-11-23)

### Anthropic Messages API MCP connector

The connector is in beta and needs a beta header. The docs list `mcp-client-2026-09-15` and `mcp-client-2025-11-20`; `mcp-client-2025-04-04` is deprecated. Each server in `mcp_servers` needs a matching `mcp_toolset` in `tools`.

```bash
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "anthropic-beta: mcp-client-2026-09-15" \
  -H "content-type: application/json" \
  -d '{
    "model": "YOUR_MODEL",
    "max_tokens": 1000,
    "messages": [{"role": "user", "content": "Find a cheap kettle in Iceland."}],
    "mcp_servers": [{"type": "url", "url": "https://mcp.kaupakaupa.is/mcp", "name": "kaupakaupa"}],
    "tools": [{"type": "mcp_toolset", "mcp_server_name": "kaupakaupa"}]
  }'
```

No `authorization_token` is needed. The `anthropic-version` value and the `"type": "url"` field in `mcp_servers` are copied from memory of the same docs page, not re-read line by line; check them against the source.

Source: https://platform.claude.com/docs/en/agents-and-tools/mcp-connector (checked 2026-11-23)

### OpenAI Agents SDK (Python)

```python
import asyncio
from agents import Agent, Runner
from agents.mcp import MCPServerStreamableHttp

async def main():
    async with MCPServerStreamableHttp(
        name="KaupaKaupa",
        params={"url": "https://mcp.kaupakaupa.is/mcp"},
    ) as server:
        agent = Agent(name="Shopper", instructions="Use the tools to answer.", mcp_servers=[server])
        result = await Runner.run(agent, "Where is a Nintendo Switch cheapest?")
        print(result.final_output)

asyncio.run(main())
```

Source: https://openai.github.io/openai-agents-python/mcp/ (checked 2026-11-23)

### MCP Python SDK

```python
import asyncio
from mcp import Client

async def main():
    async with Client("https://mcp.kaupakaupa.is/mcp") as client:
        tools = await client.list_tools()
        print([t.name for t in tools.tools])
        result = await client.call_tool("search_products", {"query": "kaffivel"})
        print(result)

asyncio.run(main())
```

This is the current `main` branch API (a URL means Streamable HTTP). Older releases use `streamablehttp_client` with `ClientSession`; match the snippet to your installed version.

Source: https://py.sdk.modelcontextprotocol.io/client/transports/ (checked 2026-11-23)

### MCP TypeScript SDK

```ts
import { Client, StreamableHTTPClientTransport } from '@modelcontextprotocol/client';

const client = new Client({ name: 'kk-test', version: '1.0.0' });
await client.connect(new StreamableHTTPClientTransport(new URL('https://mcp.kaupakaupa.is/mcp')));
const { tools } = await client.listTools();
const result = await client.callTool({ name: 'search_products', arguments: { query: 'kaffivel' } });
```

This is the v2 package layout on `main` (`@modelcontextprotocol/client`). Older v1 code imports from `@modelcontextprotocol/sdk/client/...`; that layout was not re-checked.

Source: https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/docs/clients/connect.md , https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/docs/get-started/first-client.md (checked 2026-11-23)

### MCP Inspector

See Quick test. Source: https://modelcontextprotocol.io/docs/tools/inspector (checked 2026-11-23)

## Troubleshooting

- `GET /mcp` returns 405: normal. Clients must POST; there is no server-push stream.
- 429 or "rate limit reached": wait the number of seconds in the message. Shared hosted-assistant traffic can hit the limit sooner than your own use.
- Client only supports stdio: run `npx mcp-remote https://mcp.kaupakaupa.is/mcp` and register that command.
- Client only supports the old SSE transport: not supported by this server; use `mcp-remote` or a newer client.
- Protocol-version errors on connect: the server negotiates versions 2024-11-05 through 2025-11-25. A client that asks for 2026-07-28 in `initialize` gets 2025-11-25 back and can continue on it. A client that uses the newer sessionless request form (no `initialize`, the version on every request) is refused for now.
- Tools do not appear in a chat: check that the connector or server is switched on for that chat, and that the client is in agent or tool-calling mode.
