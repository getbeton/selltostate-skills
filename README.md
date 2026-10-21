# Sell to State skills and MCP for AI agents

[![skills.sh](https://skills.sh/b/getbeton/selltostate-skills)](https://skills.sh/getbeton/selltostate-skills)

Give your agent government procurement data: tenders and contract awards from official sources in more than 60 countries, in one schema. Find tenders that still accept bids, research a public buyer, see which contracts a competitor won, and set tender alerts.

Data and API: [Sell to State](https://selltostate.com/?utm_source=github&utm_medium=referral&utm_campaign=sts-skills). Every tender links to the official notice.

## What is in this repo

| Part | File | Used by |
|---|---|---|
| 5 skills | `skills/*/SKILL.md` | Claude Code, Codex, Cursor, Gemini CLI, GitHub Copilot, OpenClaw, Hermes and other agents that read SKILL.md |
| Remote MCP server | `.mcp.json`, `mcp.json`, `gemini-extension.json` | The same agents, plus Claude.ai and ChatGPT |
| Plugin manifests | `.claude-plugin/`, `plugin.json` | Claude Code plugin and marketplace, the Agent Plugins format (ChatGPT, Codex, Cursor) |

## Skills

| Skill | Use it to |
|---|---|
| `find-open-tenders` | Find tenders that accept bids, by keyword and country, with deadline and official link |
| `research-public-buyer` | Profile a ministry, council, hospital or university: spend, sectors, top suppliers, open tenders |
| `competitor-government-wins` | See the public contracts a company won, or which companies win contracts for a subject |
| `tender-alerts` | Save a search and get new matches by email, webhook, Google Sheets or a polling job |
| `sell-to-state-api` | Reference for the REST API and the MCP tools: auth, filters, errors, limits |

## Install

You need a Sell to State account with an active plan. [Start here](https://app.selltostate.com/?utm_source=github&utm_medium=referral&utm_campaign=sts-skills).

**Claude Code** (skills and MCP together):

```
/plugin marketplace add getbeton/selltostate-skills
/plugin install sell-to-state@sell-to-state
```

**Any agent that reads skills** ([skills.sh](https://skills.sh)):

```bash
npx skills add getbeton/selltostate-skills
```

**Gemini CLI** (skills and MCP together):

```bash
gemini extensions install https://github.com/getbeton/selltostate-skills
```

**Claude.ai, ChatGPT, Cursor or any MCP client**: add a remote MCP server with this URL. The client signs you in with OAuth.

```
https://app.selltostate.com/api/mcp
```

**Without MCP**: the skills call the REST API with a key. Create one at [app.selltostate.com/account/keys](https://app.selltostate.com/account/keys?utm_source=github&utm_medium=referral&utm_campaign=sts-skills) and set it in your shell:

```bash
export STS_API_KEY=sts_...
```

## Ask your agent

- "Find UK tenders for payroll software that still accept bids."
- "Who are the top suppliers of Countess of Chester Hospital NHS Foundation Trust?"
- "Which German public contracts did Rohde & Schwarz Cybersecurity win?"
- "Alert me every day about new open tenders for database support in Germany and Austria."

## Links

- Countries and data depth: [selltostate.com/countries](https://selltostate.com/countries/?utm_source=github&utm_medium=referral&utm_campaign=sts-skills)
- MCP server: [selltostate.com/mcp](https://selltostate.com/mcp/?utm_source=github&utm_medium=referral&utm_campaign=sts-skills)
- API docs: [docs.selltostate.com](https://docs.selltostate.com/?utm_source=github&utm_medium=referral&utm_campaign=sts-skills)
- Privacy: [selltostate.com/privacy](https://selltostate.com/privacy/) · Terms: [selltostate.com/terms](https://selltostate.com/terms/)
- Support: support@selltostate.com

## License

MIT. The data is subject to the [Sell to State terms](https://selltostate.com/terms/) and the terms of each official source.
