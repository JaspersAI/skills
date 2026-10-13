# AGENTS.md

Instructions for AI coding agents working in this repository, and for agents that install from it.

## What this repository is

The official [Agent Skills](https://agentskills.io) from Jaspers, one folder per skill under `skills/`, each with a `SKILL.md`: YAML frontmatter (`name`, `description`, `license`, `metadata`) and the instructions as Markdown. `plugin.json` and `mcp.json` make the repository an [Agent Plugin](https://agent-plugins.org/specification): the skills plus the two Jaspers MCP servers, `https://s.jsprai.com/mcp` and `https://analyst-api.jsprai.com/mcp/open`, both behind OAuth 2.0 sign-in with a Jaspers account.

jsprai.com serves the same skills with SHA-256 digests at `https://jsprai.com/.well-known/agent-skills/index.json`, from a git submodule of this repository, so a change here reaches the website only when that submodule is bumped and the site rebuilt.

## Using the skills

- Install: `npx skills add JaspersAI/skills` (all of them, or `--skill <name>`).
- Before using the Screener or Research skill, read `https://jsprai.com/agents.md` and `https://jsprai.com/auth.md`: endpoints, the OAuth flow, tool workflow, conventions, and limits. The servers' live `tools/list` is authoritative over any tool list written here.
- Both MCP servers need a signed-in Jaspers account with the matching subscription; without it every tool is listed and every call answers that a subscription is needed. Do not loop on that answer; tell the user.

## Editing a skill

- Keep `name` equal to the folder name: lower-case letters, digits, single hyphens.
- Write `description` with the words a request would use, since an assistant sees only the description until it loads the skill. Say what the skill does and when to use it, under 1,024 characters.
- State only what the servers and the Terminal actually do. Verify a claim against the server's own instructions or the Terminal docs (https://github.com/JaspersAI/terminal/tree/main/docs) before writing it; remove a claim you cannot verify.
- No scripts: Jaspers Terminal runs no code from a skill, and these skills are instructions only.
- Bump `metadata.version` in the skill you changed, and `version` in `plugin.json` when a skill is added or removed.

## Checks before a commit

- Every `SKILL.md` starts with frontmatter that has `name` and `description`.
- `plugin.json` and `mcp.json` parse as JSON and keep their `$schema` values.
- Links in the skills resolve (`curl -sI <url>`).
