# obsidian-vault

Always-on Obsidian conventions for Claude Cowork vaults. Every note Claude
touches follows Obsidian Flavored Markdown rules, stays plugin-compatible,
and respects your vault's existing conventions.

## What it does

Keeps Claude's note editing consistent and safe across sessions — wikilinks
instead of markdown links, valid frontmatter, correct callout syntax, plugin-aware
formatting — without you having to remind it every time.

The skill activates automatically whenever Claude detects an Obsidian vault
in the working directory. Core conventions are always in context; detailed plugin
syntax (Dataview, Tasks, Templater, Bases, Canvas) and the full property type
reference load from reference files only when the task calls for them.

## Getting started

Install as a Cowork plugin:

1. Open **Settings > Plugins** in the Claude desktop app
2. Click **Add marketplace** and enter `aliasunder/cowork-plugins`
3. Enable **obsidian-vault**

Then add this to your Cowork project settings (not required, but recommended
for reliability):

```
This project is an Obsidian vault.

Use the obsidian-vault skill for all note creation and editing.
Preserve existing links, frontmatter, and note structure unless asked otherwise.
Ask before renaming notes, moving files, or making vault-wide structural changes.
```

The skill will also suggest this snippet the first time it activates in a
new vault project.

## What's always on

- Obsidian Flavored Markdown: wikilinks, callouts, tags, tasks, frontmatter
- Safe editing rules: minimum viable change, no silent renames, no type drift
- Note audit: review any note for convention issues without editing it
- Frontmatter repair: fix YAML types, date formats, tag list structure
- Plain text → vault-ready note conversion

## Reference files (loaded on demand)

| File | Loaded when |
|---|---|
| `references/syntax.md` | Working with embeds, block references, Mermaid, inline comments |
| `references/plugins.md` | Writing Dataview queries, Tasks syntax, Templater templates, Bases schemas, Canvas JSON |
| `references/properties.md` | Creating or repairing frontmatter, designing a property schema |

## Structure

```
.claude-plugin/plugin.json
skills/obsidian-vault/
  SKILL.md                    # Core skill — always on
  references/
    syntax.md                 # Full OFM syntax reference
    plugins.md                # Dataview, Tasks, Templater, Bases, Canvas
    properties.md             # YAML property types, reserved keys, schemas
```

## Contributing

PRs welcome — especially for additional plugin conventions (Excalidraw,
Obsidian Git, Kanban, Periodic Notes, etc.) and vault-specific schemas.
Use `trip-planner/` as the reference implementation for structure and style.
