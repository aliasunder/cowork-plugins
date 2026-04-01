# cowork-plugins

Plugins for [Claude Cowork](https://claude.ai) — the desktop tool for task and file management. Installable via **Settings > Plugins > Add marketplace** using `aliasunder/cowork-plugins`.

## Plugins

| Plugin | Description |
|--------|-------------|
| [trip-orchestrator](trip-orchestrator/) | End-to-end trip planning project manager. Opinionated orchestrator-style skill — encodes a complete workflow (8 planning phases, session protocols, file conventions) rather than a set of independent tools. See its README for the design rationale. |

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
  commands/                     # Slash commands
  assets/                       # Static files (dashboards, templates)
```

See any existing plugin directory for a working example.

## License

MIT
