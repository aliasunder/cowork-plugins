# obsidian-vault

Let Claude work in your Obsidian vault without breaking it. Links stay linked, frontmatter stays queryable, callouts render, and Claude speaks Dataview, Tasks, Kanban, Meta Bind, Templater, Bases, and Canvas fluently. Built for power users with opinionated setups — the plugin detects your vault's conventions and follows them, rather than assuming defaults.

## What you get

**Convention-aware editing.** Every vault is different — link style, frontmatter schemas, task formats, tag conventions. The plugin detects your vault's patterns on first use, confirms them with you, and persists the choices so future sessions stay consistent. No more fighting Claude to stop converting your markdown links to wikilinks (or vice versa).

**Frontmatter that stays queryable.** Dataview, Bases, and the Properties pane silently break when property types drift — a date becomes a string, a list becomes a single item, a boolean gets capitalized. The plugin enforces type consistency, ISO 8601 dates, lowercase booleans, and proper list syntax so your queries keep working.

**Deep plugin fluency.** Not just syntax — the plugin understands how plugins interact. Dataview TASK queries vs Tasks plugin queries. Kanban board structure that Tasks can still parse. Meta Bind inputs that modify frontmatter Dataview reads. Templater templates that produce Tasks-compatible output. Each plugin has its own reference file with interaction gotchas documented.

**Safe output rules.** Before returning any note, the skill runs a checklist: frontmatter fenced at top, existing keys preserved, block IDs intact, links in the vault's convention, tags correctly formatted, Kanban board structure intact, no silent type drift. You get the edit you asked for, not an accidentally restructured file.

**Works with the setup most people have.** Your vault is usually a parent folder containing many Cowork projects, not a folder Claude created from scratch. The plugin's activation signals understand this — ancestor `.obsidian/` detection, a vault-aware CLAUDE.md pointer, Obsidian-specific phrasing in your request — so it activates correctly in projects that live inside an existing vault.

## How it works

The plugin activates whenever Claude sees vault-related work — creating a note, editing frontmatter, writing a Dataview query, editing a Kanban board — inside a Cowork project that lives in (or is) an Obsidian vault.

When active, core Obsidian Flavored Markdown conventions and safe-output rules stay loaded in Claude's working context. Deeper reference material — plugin-specific guidance, full OFM syntax, the property type catalog — lives in separate files that Claude pulls on demand. This keeps the core skill lean (~2,500 words) while having comprehensive guidance available when a task calls for it.

On first use in a new vault, the skill proactively detects conventions: link style (from `app.json` and existing notes), task format (from Tasks plugin config), tag patterns, frontmatter schemas, template setup, and Kanban usage. It confirms findings with you and persists them in your project's CLAUDE.md or auto-memory so they carry across sessions.

## Getting started

Install as a Cowork plugin:

1. Open **Settings > Plugins** in the Claude desktop app
2. Click **Add marketplace** and enter `aliasunder/cowork-plugins`
3. Enable **obsidian-vault**

Then add this to your Cowork project settings (strongly recommended for vault-embedded projects):

```
This project lives inside an Obsidian vault (parent directory).

Use the obsidian-vault skill for all .md file creation and editing in
this project — notes, session logs, CLAUDE.md/TASKS.md, and any
operational docs (they all render in Obsidian since the project lives
in the vault).
Preserve existing links, frontmatter, and note structure unless asked
otherwise.
Ask before renaming notes, moving files, or making vault-wide structural
changes.
```

The skill will also suggest this snippet — along with detected conventions — the first time it activates in a new vault-embedded project.

## Structure

```
.claude-plugin/plugin.json              # Plugin manifest
skills/obsidian-vault/
  SKILL.md                              # Core skill — conventions, detection, safe-output rules
  references/
    syntax.md                           # OFM syntax (embeds, block refs, Mermaid, comments, math)
    properties.md                       # YAML property types, reserved keys, schemas
    core-plugins.md                     # Properties, Bases, Canvas, Daily Notes, Templates, Graph
    dataview.md                         # Queries, inline fields, DataviewJS, implicit fields
    tasks.md                            # Emoji format, custom statuses, dates, dependencies, queries
    kanban.md                           # Board structure, card syntax, safe editing, interactions
    meta-bind.md                        # INPUT/VIEW/BUTTON syntax, property binding, actions
    templater.md                        # Template syntax, folder templates, Templater vs core Templates
```

## Contributing

PRs welcome — especially for additional plugin conventions (Excalidraw, Obsidian Git, Periodic Notes, etc.) and cross-plugin interaction gotchas. Use `trip-planner/` as the reference implementation for structure and style.
