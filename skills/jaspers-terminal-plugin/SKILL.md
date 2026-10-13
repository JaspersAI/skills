---
name: jaspers-terminal-plugin
description: Write, test, and release a plugin for Jaspers Terminal with @jaspers-ai/sdk - a data source, a view, or an MCP connection that shows on the Terminal's grid. Use when asked to build or change a Jaspers Terminal plugin, add a data vendor to the Terminal, or publish a plugin to Jaspers Hub.
license: MIT
metadata:
  author: Jaspers
  version: 1.0.0
  homepage: https://github.com/JaspersAI/terminal/blob/main/docs/writing-a-plugin.md
---

# Writing a Jaspers Terminal plugin

Jaspers Terminal ships no plugins of its own: market data, filings, and screeners all arrive as plugins, and built-in views (a table, a chart, a single number) render whatever any of them returns. A plugin is a TypeScript package written against one MIT-licensed SDK, `@jaspers-ai/sdk`, which the app provides at run time.

Before writing code, fetch the current SDK reference and the walkthrough; the SDK is young and they decide where this summary and the code disagree:

- https://raw.githubusercontent.com/JaspersAI/terminal/main/docs/writing-a-plugin.md
- https://raw.githubusercontent.com/JaspersAI/terminal/main/docs/sdk-reference.md
- https://raw.githubusercontent.com/JaspersAI/terminal/main/docs/publishing-a-plugin.md

Official plugins to copy from: https://github.com/JaspersAI/plugin-yfinance, https://github.com/JaspersAI/plugin-sec, https://github.com/JaspersAI/plugin-fed, https://github.com/JaspersAI/plugin-tradingview.

## Shape of a plugin

A plugin is a folder whose `plugin.tsx` default-exports one declaration, `definePlugin({...})`, listing everything the plugin offers:

- `id`: the plugin's name.
- `secrets`: values the user supplies once, such as an API key, referenced as `${secret:name}`.
- `connections`: MCP servers the plugin ships or reaches, from `defineConnection({ command, env })`.
- `sources`: named data calls, from `defineSource`. A source is either one tool on an MCP server (`{ mcp: 'server', tool: 'quotes' }`) or a function the plugin runs itself. A source that returns a list of records produces rows, which feed the plugin's own views, the built-in table and chart, and the assistant alike. Rows are kept for the source's `ttl` and every run is filed in the user's local SQLite file.
- `views`: React components the assistant can drive, from `defineView(Component, { title, state, output, instructions, renders })`. `state` is a zod schema of what the assistant may set; `output` is what the view reports back; `instructions` tell the assistant when and how to use it; `renders` lists the sources it shows.

Three parts of the app read that one object: the main process runs the schemas, the plugin's own process runs its sources and `start`, and the window mounts its views.

## Views

A view runs in a sandboxed iframe with no network of its own and acts only through the SDK bridge. Inside it, `useData('plugin/source', args)` runs a source and `usePublish(panel, output)` reports what the view shows. Keep views small: read state, show data, publish output. A view that embeds a site names the site's exact https origin, and only that site loads.

## What plugin code may do

The SDK gives plugin code these capabilities, and nothing else: `fetch` (HTTP to the source's declared hosts only, every redirect checked again), `live` (values views read as they change), `jobs` (background work that outlives the call), `notify` (a line in the assistant's answer box), `llm` (completions on the user's model; the key never reaches the plugin), `tools` (the app's MCP connections), `files` (the plugin's own data folder, read and write; its install folder, read only), `state` (the app's state, read only), and `skills`. Declare the hosts you fetch from; undeclared hosts fail.

## Develop

A folder in `~/Jaspers/plugins/<id>` is a plugin of the user's own, which the app rebuilds whenever a file is saved. If the same plugin is installed from Hub or a release, remove it in Settings > Plugins first. Typical loop:

```sh
git clone <repo> ~/Jaspers/plugins/<id>
cd ~/Jaspers/plugins/<id>
npm install
npm run typecheck
npm test
```

## Release and publish

Bump `version` in `package.json`, commit, and push a tag; the Release workflow checks the plugin and attaches `<id>-<version>.zip` to a GitHub release:

```sh
npm version patch
git push --follow-tags
```

Users install by pasting the repository URL into Settings > Plugins. To publish on Jaspers Hub (https://hub.jsprai.com/), use Settings in the Terminal; Hub reviews every version before it is listed. A plugin runs with the user's permissions, so say plainly what it reads and where it connects.
