**Live:** https://console.dada-tuda.ru

# Dada Cloud - public kit

Everything of Dada Cloud that runs on your machine or inside your agent: the
Claude Code plugin (MCP), agent skills and pointers to the CLI. The platform
itself is closed; this repository is what you install.

| Piece | Where | Install |
|---|---|---|
| Claude Code plugin (MCP, 61 tools) | [`mcp-plugin/`](mcp-plugin) | `/plugin marketplace add DadaDevelopment/dada-cloud` then `/plugin install dada-cloud@dada-cloud` |
| Agent skill | [`skills/dada-cloud/`](skills/dada-cloud/SKILL.md) | `npx skillsbd add DadaDevelopment/dada-cloud` or copy into `.agents/skills` / `.claude/skills` |
| MCP endpoint (any client) | `https://console.dada-tuda.ru/mcp` | OAuth via Dada ID, or `Authorization: Bearer <token>` for server-side agents |
| CLI `ddc` + releases | [DadaDevelopment/ddc](https://github.com/DadaDevelopment/ddc) | `curl -fsSL https://raw.githubusercontent.com/DadaDevelopment/ddc/main/install.sh \| sh` |

Docs: [Control Dada Cloud from an AI agent](https://console.dada-tuda.ru/docs/mcp-ai-agents).

This repository is generated from the Dada Cloud monorepo by
`scripts/sync_public_repo.sh`; pull requests are read, but changes land through
the monorepo.
