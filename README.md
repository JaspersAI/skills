# Jaspers skills

Official [Agent Skills](https://agentskills.io) from Jaspers, for AI coding agents and for Jaspers Terminal:

| Skill | For |
| --- | --- |
| [jaspers-screener](skills/jaspers-screener/SKILL.md) | Screening US public companies and reading their SEC filings against criteria through the Jaspers Screener MCP server (`https://s.jsprai.com/mcp`). |
| [jaspers-terminal-plugin](skills/jaspers-terminal-plugin/SKILL.md) | Writing, testing, and releasing a Jaspers Terminal plugin with `@jaspers-ai/sdk`. |
| [jaspers-terminal-skill](skills/jaspers-terminal-skill/SKILL.md) | Writing a skill for Jaspers Terminal. |

## Install

```sh
npx skills add JaspersAI/skills
```

The same skills are published with SHA-256 digests at https://jsprai.com/.well-known/agent-skills/index.json.

## About Jaspers

Jaspers builds [Jaspers Terminal](https://github.com/JaspersAI/terminal), the open-source agentic terminal for public markets, and runs hosted SEC data services that agents reach over MCP. Agent instructions: https://jsprai.com/agents.md.

## License

[MIT](LICENSE)
