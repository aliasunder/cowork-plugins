# cowork-plugins

Plugins for **Claude Cowork** — [Cowork](https://support.anthropic.com/en/articles/11145-using-cowork-mode) is a mode in the Claude desktop app that gives Claude access to your files and a sandboxed shell for running code, building documents, and managing tasks. Plugins extend what Cowork can do by adding skills (specialized capabilities) and commands (slash-command workflows).

Install this marketplace via **Settings > Plugins > Add marketplace** using `aliasunder/cowork-plugins`.

## Plugins

| Plugin | Description |
|--------|-------------|
| [trip-planner](trip-planner/) | Plan a multi-week trip across 15+ sessions without losing context. Cross-validated research, budget tracking, printable daily cards, and a phase-based workflow from first idea to departure day. |
| [obsidian-vault](obsidian-vault/) | Let Claude edit your Obsidian vault without breaking it. Wikilinks stay linked, frontmatter stays queryable, callouts render — and Claude speaks Dataview, Tasks, Templater, Bases, and Canvas fluently. |

## Installing

In the Claude desktop app:

1. Open **Settings > Plugins**
2. Click **Add marketplace**
3. Enter `aliasunder/cowork-plugins`
4. Select which plugins to enable

Each plugin has its own `.claude-plugin/plugin.json`, skills, and commands. Cowork discovers them automatically.

## Adding a plugin

Each plugin lives in its own top-level directory with this structure:

```
my-plugin/
  .claude-plugin/plugin.json    # Required — name, version, description
  skills/my-skill/SKILL.md      # Skill definition
  commands/                     # Slash commands (optional)
  assets/                       # Static files (optional)
```

See any existing plugin directory for a working example.

## License

[MIT](LICENSE)
