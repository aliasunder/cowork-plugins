# cowork-plugins

A collection of [Cowork](https://claude.ai) plugins — installable via **Settings > Plugins > Add marketplace** using `aliasunder/cowork-plugins`.

## Plugins

| Plugin | Description |
|--------|-------------|
| [trip-orchestrator](trip-orchestrator/) | End-to-end trip planning project manager. 8 planning phases, session protocols, research methodology, budget tracking, and material production across unlimited Cowork sessions. |

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
