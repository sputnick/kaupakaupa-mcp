# KaupaKaupa MCP

Connect an AI assistant to [KaupaKaupa](https://kaupakaupa.is), a product search engine over Icelandic online shops. The assistant can search products, compare prices across shops, read delivery costs and check whether a sale is real.

The server speaks the Model Context Protocol (MCP). Add this address to any client that supports remote MCP servers:

```
https://mcp.kaupakaupa.is/mcp
```

No sign-in, API key or OAuth is needed. The server is anonymous and read-only: it never buys, reserves or changes anything.

This repository holds the setup guide for each client and an agent skill that teaches an assistant how to shop well with the tools. The server itself is not in this repository.

## Quick start

Claude Code:

```bash
claude mcp add --transport http kaupakaupa https://mcp.kaupakaupa.is/mcp
```

Claude.ai and Claude Desktop: Settings, Connectors, "Add custom connector", paste the address, choose "No sign in".

Cursor, LM Studio and most other clients take this entry in their MCP settings file:

```json
{
  "mcpServers": {
    "kaupakaupa": { "url": "https://mcp.kaupakaupa.is/mcp" }
  }
}
```

Steps for ChatGPT, Codex, Gemini CLI, VS Code, Zed, Cline, Roo Code, Continue, the OpenAI and Anthropic APIs and the MCP SDKs are in [docs/clients.md](docs/clients.md).

Then ask something:

- "Hvar er ódýrast að kaupa Nespresso kaffivél, og hver er sendingarkostnaðurinn?"
- "Find a waterproof men's winter jacket under 30,000 ISK, in stock, sorted by price."
- "Which shops sell the PlayStation 5 and how fresh are their prices?"

## Tools

| Tool | What it does |
|---|---|
| `search_products` | Keyword search with filters: category, price range, brand, shop, attributes, on sale, in stock, sort |
| `get_product` | One product: price, discount, delivery facts, when the price was checked, description, category trail |
| `get_products` | Up to 10 products at once, to compare candidates |
| `get_offers` | The shops whose listings were matched as the same product, cheapest first |
| `get_price_history` | How one product's price moved over 30 days, 90 days or all time |
| `browse_categories` | The category tree, one level at a time |
| `get_shop` | A shop's profile by domain, or shops by name |
| `list_markets` | The markets served, with currency and default language |

Every amount is `{amount, currency}` in whole units. Every product and shop `url` in an answer is a `go.kaupakaupa.is` link that takes the shopper to the shop. The only market today is `is` (Iceland), with prices in ISK.

The server sends its own usage instructions to the client when it connects, and each tool carries a description. A client that only has the address still gets everything it needs.

## The skill

[`skills/kaupakaupa-shopping`](skills/kaupakaupa-shopping/SKILL.md) is an agent skill with worked recipes: cheapest delivered price, finding a gift, checking a sale, splitting a basket across shops. It also covers what to do when the data is imperfect, for example when a search for a phone returns mostly phone cases.

In Claude Code, one command connects the server and installs the skill together (Claude Code 2.1.275 or later):

```
/plugin install kaupakaupa --marketplace sputnick/kaupakaupa-mcp
```

On older versions run `/plugin marketplace add sputnick/kaupakaupa-mcp`, then `/plugin install kaupakaupa@kaupakaupa-mcp`. The skill then runs as `/kaupakaupa:kaupakaupa-shopping`, and Claude loads it by itself when you ask a shopping question.

For any other agent, point it at this repository, or copy the folder into the agent's skills directory. For Claude Code without the plugin that is `~/.claude/skills/` for all projects or `.claude/skills/` in one project:

```bash
git clone https://github.com/sputnick/kaupakaupa-mcp
cp -r kaupakaupa-mcp/skills/kaupakaupa-shopping ~/.claude/skills/
```

A copied skill does not connect the server for you. Add the address first (see Quick start).

## Limits and privacy

- 60 tool calls per minute and 2,000 per day per client address. Over the limit a tool answers "rate limit reached, try again in N s". In `get_products` each product counts as one call.
- Hosted assistants such as ChatGPT and Claude.ai call from the vendor's servers, so their users share those limits.
- A search reaches at most 100 products (5 pages of 20). Narrow it with filters.
- Usage is counted per tool and client name, without the caller's address or the query text. Searches are logged like searches on the website.
- Shops that KaupaKaupa has withdrawn from its results are never returned by any tool.

## Known limits of the data

The catalogue is built by crawling shops and classifying their products automatically. Expect these, and see the skill for how to work around each:

- Matching the same product across shops misses some products, so an empty `get_offers` answer does not prove that only one shop sells it.
- Categories are assigned automatically and are sometimes wrong.
- The `brand` field is the shop's own value. It can be missing or hold the shop's name.
- Product pictures are the shops' own files. Some refuse to load.

## Feedback

Found a wrong price, a missed match or a tool that misbehaves? Open an issue in this repository and include the tool call and what you expected.

## Privacy

KaupaKaupa privacy policy: https://kaupakaupa.is/privacy/en/
