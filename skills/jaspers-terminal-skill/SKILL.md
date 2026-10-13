---
name: jaspers-terminal-skill
description: Write a skill for Jaspers Terminal - a SKILL.md in the Agent Skills format that the Terminal's assistant loads when a request matches it, such as a morning brief, a DCF layout, or a credit review. Use when asked to create, edit, or install a Jaspers Terminal skill, or to turn a repeated workflow in the Terminal into one.
license: MIT
metadata:
  author: Jaspers
  version: 1.0.0
  homepage: https://github.com/JaspersAI/terminal/blob/main/docs/skills.md
---

# Writing a Jaspers Terminal skill

A skill is written know-how the Terminal's assistant picks up when a request calls for it: how the user likes a DCF laid out, what a morning brief covers, which views a credit review opens. Jaspers uses the Agent Skills format (https://agentskills.io), so a skill written for Claude Code, Codex, or another client works here, and one written here works there.

The current reference is https://raw.githubusercontent.com/JaspersAI/terminal/main/docs/skills.md; read it when details matter.

## Format

A skill is a folder with a `SKILL.md`: YAML frontmatter that names it and says when to use it, then instructions in Markdown. Other files beside it (references, templates) are read when the instructions point at them by relative path. Skills live in `~/Jaspers/skills/<name>/`, and the app picks up changes as they are saved.

```markdown
---
name: morning-brief
description: A morning brief across the watchlist. Use when the user asks for a brief, a morning update, or what moved overnight.
argument-hint: [tickers]
---

# Morning brief

1. Place a watchlist with $ARGUMENTS, or the tickers on screen if none are given.
2. Put the biggest movers' news beside it.
3. Write a five-line summary in a document: what moved, why, what to watch today.
```

## Frontmatter

| Field | |
| --- | --- |
| `name` | Required. Lower-case letters, digits, and single hyphens; matches the folder name. |
| `description` | Required, up to 1,024 characters. What the skill does and when to use it. The assistant sees only this until the skill is loaded, so write it with the words a request would use. |
| `when_to_use` | Appended to the description. |
| `argument-hint` | Shown in the `/` menu, like `[ticker]`. |
| `arguments` | Names for `$name` substitution, in order: `arguments: [ticker, peer]`. |
| `disable-model-invocation` | `true`: only `/name` loads it; the assistant never loads it on its own. |
| `user-invocable` | `false`: the assistant may load it; `/name` does not. |
| `license`, `compatibility`, `metadata` | Shown, not acted on. `metadata.version` is shown as an installed skill's version. |
| `allowed-tools` | Shown, not enforced. |

Other fields (`context`, `model`, `hooks`, `paths`) are ignored.

## Arguments

`$ARGUMENTS` is everything after `/name` (or what the assistant passes); `$ARGUMENTS[0]` is its first word, and quotes group words. A skill that declares `arguments` also gets `$0` to `$9` and `$name`. Dollar amounts such as `$500`, `$5.2`, `$5,000`, `$5B`, and `$5%` are never read as arguments; write `\$` where a position could be meant. Arguments no placeholder took are added at the end.

## How the assistant uses a skill

The assistant's prompt lists every skill's name and description, nothing more, until one fits a request; then it loads the instructions and follows them, keeps the skill for the next few requests, and loads it again when a later one needs it. The user can load a skill with `/name` and what it works on after it (`/morning-brief AAPL MSFT`), and a scheduled task whose instructions start with `/name` loads it at every run. Settings > Skills lists every skill with an On/Off switch.

## What Jaspers does not do

Jaspers runs no code from a skill. Scripts are listed for the assistant to read, not run, and Claude Code's `` !`command` `` lines are replaced with a note. A skill runs in the conversation that loaded it, never in a separate agent. Write instructions as steps the assistant takes on the grid and in conversation: which views to place, what to compare, what to write up.

## Writing one well

- Name the jobs the skill is for in the description, in the user's words.
- Keep the body to numbered steps; put long reference material in files beside `SKILL.md` and point at them.
- Say what the result looks like on the grid: which views, where, and what the write-up contains.
- To install from a repository, ask the user to add it in Settings > Skills, or copy the folder into `~/Jaspers/skills/`.
