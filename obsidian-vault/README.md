# obsidian-vault

Let Claude work in your Obsidian vault without breaking it. Wikilinks stay wikilinks (not `[text](file.md)`), frontmatter stays queryable by Dataview and Bases (no silent YAML type drift), callouts render as callouts, and Claude speaks Dataview, Tasks, Templater, Bases, and Canvas fluently. Built for the setup most people actually have — a Cowork project living inside an existing vault, not a vault Claude invented.

## What you get

**Wikilinks that stay wikilinks.** Every `[[Note Name]]` survives edits. Claude won't quietly convert them to `[text](file.md)`, strip `.md` extensions, or break aliases. Block references (`^id`) are preserved.

**Frontmatter that stays queryable.** Dataview and Bases silently stop matching when property types drift — a date becomes a string, a list becomes a single item, a boolean gets capitalized. The plugin enforces type consistency, ISO 8601 dates, lowercase booleans, and proper list syntax for `tags` and `aliases` so your queries keep working.

**Callouts that render.** `> [!note]`, `> [!tip]`, `> [!warning]` — the full callout vocabulary with correct title, collapse, and nesting syntax. Claude won't drop the `[!type]` marker and leave callouts rendering as plain blockquotes.

**Fluent in Obsidian's plugin ecosystem.** Dataview queries (with inline fields and the right date functions), Tasks plugin emoji syntax, Templater template delimiters, Bases property schemas, and Canvas JSON node/edge structure — all covered. Detailed reference material loads on demand when the task calls for it.

**Safe output rules.** Before returning any note, the skill runs a checklist: frontmatter fenced at top, existing keys preserved, block IDs intact, tags correctly formatted, no silent type drift. You get the edit you asked for, not an accidentally restructured file.

**Works with the setup most people have.** Your vault is usually a parent folder containing many Cowork projects, not a folder Claude created from scratch. The plugin's activation signals understand this — ancestor `.obsidian/` detection, a vault-aware CLAUDE.md pointer, Obsidian-specific phrasing in your request — so it activates correctly in projects that live inside an existing vault.

## How it works

The plugin activates whenever Claude sees vault-related work — creating a note, editing frontmatter, writing a Dataview query, fixing a callout — inside a Cowork project that lives in (or is) an Obsidian vault.

When active, core Obsidian Flavored Markdown conventions (frontmatter rules, wikilinks, callouts, tasks, tags, safe-output checklist) stay loaded in Claude's working context. Deeper reference material — full OFM syntax, plugin-specific guidance, and the complete YAML property type catalog — lives in separate files that Claude pulls on demand when a task calls for them. This keeps the core skill lean while having detailed guidance available when needed.

Two activation signals matter most: a CLAUDE.md in your Cowork project that names the vault context (snippet in *Getting started* below), and Obsidian-specific phrasing in your request ("create a daily note", "fix this callout", "write a Dataview query"). Either one is enough; both together make activation reliable.

## Getting started

Install as a Cowork plugin:

1. Open **Settings > Plugins** in the Claude desktop app
2. Click **Add marketplace** and enter `aliasunder/cowork-plugins`
3. Enable **obsidian-vault**

Then add this to your Cowork project settings (not strictly required, but strongly recommended — it's the most reliable activation signal for projects that live inside a vault as a subdirectory):

```
This project lives inside an Obsidian vault (parent directory).

Use the obsidian-vault skill for all `.md` file creation and editing in
this project — notes, session logs, CLAUDE.md/TASKS.md, and any
operational docs (they all render in Obsidian since the project lives
in the vault).
Preserve existing links, frontmatter, and note structure unless asked
otherwise.
Ask before renaming notes, moving files, or making vault-wide structural
changes.
```

The skill will also suggest this snippet the first time it activates in a new vault-embedded project.

## Why a plugin?

The same conventions could live directly in each project's CLAUDE.md, but this isn't a short snippet — it's the full set of Obsidian Flavored Markdown rules, wikilink/frontmatter/callout conventions, and plugin-specific guidance for Dataview, Tasks, Templater, Bases, and Canvas. Dropped into a CLAUDE.md, it crowds out the project-specific context that file is actually meant to carry.

A plugin is a cleaner shape for it:

- Conventions live in one place, shared by every vault-embedded Cowork project — no N-copy maintenance as Obsidian and its plugins evolve.
- Reference material (full OFM syntax, plugin-specific guidance, property type catalog) loads only when a task calls for it, so Claude's working context stays lean.
- Your CLAUDE.md stays a short pointer to the skill, leaving room for the project-specific context it's meant for.

## Structure

```
.claude-plugin/plugin.json            # Plugin manifest
skills/obsidian-vault/
  SKILL.md                            # Core skill — conventions, activation, safe-output rules
  references/
    syntax.md                         # Full OFM syntax (embeds, block refs, Mermaid, comments)
    plugins.md                        # Dataview, Tasks, Templater, Bases, Canvas
    properties.md                     # YAML property types, reserved keys, schemas
```

## Contributing

PRs welcome — especially for additional plugin conventions (Excalidraw, Obsidian Git, Kanban, Periodic Notes, etc.) and vault-specific schemas. Use `trip-planner/` as the reference implementation for structure and style.
