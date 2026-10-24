# Setteo plugin for coding assistants

The `setteo` plugin connects your coding assistant (Claude Code, Codex) to your Setteo account and
teaches it to edit your setter agents without degrading them.

It contains:

- **the Setteo MCP server** (`https://mcp.setteo.io`): the assistant reads and edits your agents,
  personas, scenarios, templates and leads, in the workspaces you authorize;
- **the `editing-agents` skill**: the assistant loads it on its own when you work on an agent or a
  persona.

You need the MCP permission on your Setteo account.

The full guide, in French, is on the Setteo docs: **https://docs.setteo.io**.

## Install in Claude Code

Terminal or desktop app:

```bash
claude plugin marketplace add SmartFunnel/setteo-ai-plugins
```

```bash
claude plugin install setteo@setteo
```

Then start Claude Code, type `/mcp`, pick the **setteo** server and sign in to Setteo in the
browser. That is where you choose the workspaces Claude can reach.

In the desktop app, the plugin then shows up in the Code tab under **+** → **Plugins**.

If you had added the server by hand (`claude mcp add … setteo`), remove it so it isn't loaded
twice: `claude mcp remove setteo`.

## Install in Codex

```bash
codex plugin marketplace add SmartFunnel/setteo-ai-plugins
```

```bash
codex plugin add setteo@setteo
```

Then open a new Codex session and sign in to Setteo when Codex asks for it. `/plugins` lists the
installed plugins.

## What the skill does

It is mostly useful when you pass on what is wrong with an agent or a bad conversation, for example:

- « Claire répond « Super ! » à chaque message, corrige. »
- « Le bootcamp revient du 16 au 20 novembre, prépare l'agent pour la nouvelle édition. »
- « Camille est trop froide, rends-la plus chaleureuse. »

With it, the assistant:

- first finds where the rule already lives (persona, context, step, setting) and rewrites it there,
  instead of adding a louder copy;
- writes no fix dates, ticket numbers or pasted conversations into the agent;
- keeps a campaign's dates in the agent context and checks their weekdays;
- describes the tone you want (react to a concrete detail, keep « vous »…) rather than banning words;
- shows you the final text before writing, then tests in the sandbox.

When you ask for something likely to degrade the agent (a « règle prioritaire » in every step, a
copy of the agent…), the assistant tells you once why and suggests something else. You decide: if
you keep your request, it does it.

You can also call it directly: `/setteo:editing-agents` in Claude Code, `$setteo:editing-agents`
in Codex.

## Updates

**Claude Code**: turn on auto-update once (`/plugin` → **Marketplaces** → **setteo** →
**Enable auto-update**), or run `claude plugin marketplace update setteo`. An open session keeps the
old version: type `/reload-plugins` or restart Claude Code.

**Codex**: run `codex plugin marketplace upgrade setteo`, then open a new session.

## Troubleshooting

- **The setteo server doesn't show up**: check the plugin is installed (`claude plugin list` or
  `codex plugin list`), then open a new session.
- **The connection is refused**: your Setteo account doesn't have the MCP permission. Ask Setteo
  support for it.
- **The assistant can't see a workspace**: in Setteo, open **Intégrations → MCP** and change the
  connection's workspaces.

## For maintainers

- The behaviour lives in `skills/` and `.mcp.json`, shared by every assistant. Each assistant has a
  thin adapter: `.claude-plugin/` for Claude Code, `.codex-plugin/` and
  `.agents/plugins/marketplace.json` for Codex.
- Bump `version` in `.claude-plugin/plugin.json` **and** `.codex-plugin/plugin.json` on every
  release, or users won't receive it.
- Validate before pushing: `claude plugin validate ./ --strict`.
- The skill's test cases are in `skills/editing-agents/evals/`
  (`claude plugin eval skills/editing-agents --scaffold`).
- A rule that must hold for every user belongs in the Setteo app (validation, tool
  descriptions, MCP resources), not in a skill: if breaking it causes a bug, it goes in the app;
  if it makes the agent worse, it goes in a skill.
