# Jaspers skills

Official [Agent Skills](https://agentskills.io) from Jaspers, for AI coding agents and for Jaspers Terminal:

| Skill | For |
| --- | --- |
| [jaspers-screener](skills/jaspers-screener/SKILL.md) | Screening US public companies and reading their SEC filings against criteria through the Jaspers Screener MCP server (`https://s.jsprai.com/mcp`). |
| [jaspers-research](skills/jaspers-research/SKILL.md) | Searching and reading one company's SEC filings, the filing database, and verified citations through the Jaspers Research MCP server (`https://analyst-api.jsprai.com/mcp/open`). |
| [jaspers-terminal-plugin](skills/jaspers-terminal-plugin/SKILL.md) | Writing, testing, and releasing a Jaspers Terminal plugin with `@jaspers-ai/sdk`. |
| [jaspers-terminal-skill](skills/jaspers-terminal-skill/SKILL.md) | Writing a skill for Jaspers Terminal. |

## Install

```sh
npx skills add JaspersAI/skills
```

The same skills are published with SHA-256 digests at https://jsprai.com/.well-known/agent-skills/index.json.

## As an Agent Plugin

`plugin.json` and `mcp.json` make this repository an [Agent Plugin](https://agent-plugins.org/): a client that reads the manifest gets the skills under `skills/` and the two Jaspers MCP servers, `https://s.jsprai.com/mcp` and `https://analyst-api.jsprai.com/mcp/open` (OAuth 2.0 sign-in with a Jaspers account; see https://jsprai.com/auth.md). Contributors and coding agents: read [AGENTS.md](AGENTS.md).

## About Jaspers

Jaspers builds [Jaspers Terminal](https://github.com/JaspersAI/terminal), the open-source agentic terminal for public markets, and runs hosted SEC data services that agents reach over MCP. Agent instructions: https://jsprai.com/agents.md.

## License

[MIT](LICENSE) for the skills in this repository. Jaspers Terminal itself is open source under the [Business Source License 1.1](https://github.com/JaspersAI/terminal/blob/main/LICENSE); its SDK, `@jaspers-ai/sdk`, is MIT.
