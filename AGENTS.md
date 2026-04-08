# Repository Guidelines

This file provides guidance to Claude Code (claude.ai/code), Codex CLI, and other AI agents when working with code in this repository.

## Project Overview

`cowork-plugins` is a collection of plugins for **Claude Cowork** — a mode in the Claude desktop app that gives Claude access to files and a sandboxed shell. Plugins extend Cowork with skills (specialized capabilities) and commands (slash-command workflows).

This is a public marketplace installed via **Settings > Plugins > Add marketplace** using `aliasunder/cowork-plugins`.

## Repository Structure

```
cowork-plugins/
  README.md                             # Project overview and install instructions
  CONTRIBUTING.md                       # Release process and CI docs
  LICENSE                               # MIT license
  .claude-plugin/marketplace.json       # Marketplace config (version, plugin list)
  .github/workflows/
    auto_release.yml                    # Tag-triggered build + GitHub release
    manual_release.yml                  # UI-triggered version bump + release
  <plugin-name>/
    .claude-plugin/plugin.json          # Required: name, version, description
    skills/<skill-name>/SKILL.md        # Skill definition files
    commands/                           # Slash command files
    assets/                             # Static files (dashboards, templates)
```

### Current Plugins

| Plugin | Directory | Description |
|--------|-----------|-------------|
| Trip Planner | `trip-planner/` | Multi-week trip planning across 15+ sessions with cross-validated research, budget tracking, printable daily cards, and phase-based workflow |

## Plugin Architecture

Each plugin lives in its own top-level directory. Cowork discovers plugins automatically via `.claude-plugin/plugin.json`.

### plugin.json

Required fields:
- `name` — display name
- `version` — semver
- `description` — short description

### Skills (`skills/<skill-name>/SKILL.md`)

Skills are specialized capabilities loaded by Claude when a task matches the skill's description. Each skill has:
- A descriptive header
- Trigger conditions
- Step-by-step workflow instructions

### Commands (`commands/`)

Slash commands are workflow files invoked explicitly by the user.

## Development Guidelines

- Each plugin is self-contained in its own top-level directory
- No build system or package manager — plugins are markdown/JSON files
- Follow existing plugin structure when adding new plugins
- Use the `trip-planner/` plugin as a reference implementation
- `.claude-plugin/plugin.json` is required for Cowork discovery

## Releasing

All plugins share a single version. Two CI workflows handle releases:

- **Manual Release** (`manual_release.yml`): Triggered from Actions UI. Pick patch/minor/major → bumps all version files → commits → tags → builds `.skill` artifacts → creates GitHub release.
- **Auto Release** (`auto_release.yml`): Triggered by `v*` tag push. Validates version files match the tag → builds artifacts → creates GitHub release.

### Version files (must stay in sync)

- `.claude-plugin/marketplace.json` → `metadata.version` + each `plugins[].version`
- `<plugin>/.claude-plugin/plugin.json` → `version`

See `CONTRIBUTING.md` for full details.

## Adding a New Plugin

1. Create a top-level directory: `my-plugin/`
2. Add `.claude-plugin/plugin.json` with name, version, and description matching the current marketplace version
3. Add skills under `skills/<skill-name>/SKILL.md`
4. Add the plugin entry to `.claude-plugin/marketplace.json`
5. Add commands under `commands/` if needed
6. Add static assets under `assets/` if needed
7. Document the plugin in the root `README.md` plugins table

CI auto-discovers plugins via `*/.claude-plugin/plugin.json` — no workflow edits needed.
