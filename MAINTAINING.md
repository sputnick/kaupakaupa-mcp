# Maintaining this repository

This repository documents a server that lives elsewhere. The server at `https://mcp.kaupakaupa.is/mcp` is the source of truth: its own instructions and tool descriptions are what every client receives. The files here must describe what the live server does, and nothing it does not yet do.

## When the server changes

Update this repository in the same piece of work as any change to the server's tools, their inputs or outputs, its instructions, its limits or its address. Do not publish a description of a tool before that tool is live.

| Server change | Update here |
|---|---|
| A tool is added, removed or renamed | The tools table in `README.md`; the "Which tool when" table and any recipe in the skill; `CHANGELOG.md` |
| A tool's input or output field changes | Every example call in `skills/kaupakaupa-shopping/SKILL.md` and `references/recipes.md` that uses it |
| The server's instructions gain or lose a warning | Sections 2 and 5 of the skill, and "Known limits of the data" in `README.md` |
| A data problem is fixed in the backend (matching, categories, brands, pictures) | Remove or soften the matching workaround in the skill and in `README.md`; a stale workaround wastes calls |
| Limits change (calls per minute or day, page depth, batch size) | "Limits and privacy" in `README.md`; section 8 of the skill |
| Protocol versions or transport change | The top of `docs/clients.md` and its troubleshooting section |
| The server's address changes | `.mcp.json`, `README.md`, section 1 of the skill, and every snippet in `docs/clients.md` |
| A new market is served | `README.md` (market and currency lines) and the recipe examples |

## Check before you commit

1. Connect to the live server and list its tools. Every tool name in `README.md`, the skill and the recipes must be in that list, and every tool in that list must be in the README table.

   ```bash
   npx @modelcontextprotocol/inspector --server-url https://mcp.kaupakaupa.is/mcp --transport http
   ```

2. Run each example call in the skill and recipes that you touched, and read the answer.
3. Run `claude plugin validate .` in this folder. The repository is also a Claude Code plugin (`.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`, `.mcp.json`), and the skill keeps to the frontmatter fields other agents accept (`name`, `description`).
4. Add a dated line to `CHANGELOG.md`.

## Client setup steps go stale

`docs/clients.md` records the page and date each client's steps were checked against. Client menus change often. Re-check a section against its source page before you rely on it, and update the date.

## What does not belong here

No server code, no internal ticket numbers, no private repository names, and nothing about how the catalogue is built beyond what a user of the tools needs.
