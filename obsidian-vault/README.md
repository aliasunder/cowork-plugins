# obsidian-vault

Obsidian conventions applied automatically when Claude creates or edits
notes in your vault. Wikilinks preserved, frontmatter types stable, callout
syntax correct — so every `.md` file Claude writes in a vault-embedded
Cowork project renders properly in Obsidian without you having to remind
it every session.

## What it does

Keeps Claude's note output consistent and safe across sessions — wikilinks
instead of markdown links, valid frontmatter, correct callout syntax,
plugin-aware formatting — without you having to remind it every time.

The skill activates when Claude sees vault-related work — creating notes,
editing frontmatter, writing Dataview queries — inside a Cowork project
that lives in (or is) an Obsidian vault. Core conventions are loaded with
the skill; detailed plugin syntax (Dataview, Tasks, Templater, Bases,
Canvas) and the full property type reference load from reference files
only when the task calls for them.

## Getting started

Install as a Cowork plugin:

1. Open **Settings > Plugins** in the Claude desktop app
2. Click **Add marketplace** and enter `aliasunder/cowork-plugins`
3. Enable **obsidian-vault**

Then add this to your Cowork project settings (not strictly required, but
strongly recommended — it's the most reliable activation signal for
projects that live inside a vault as a subdirectory):

```
This project lives inside an Obsidian vault (parent directory).

Use the obsidian-vault skill for all .md file creation and editing in this
project — notes, session logs, CLAUDE.md/TASKS.md, and any operational
docs (they all render in Obsidian since the project lives in the vault).
Preserve existing links, frontmatter, and note structure unless asked
otherwise.
Ask before renaming notes, moving files, or making vault-wide structural
changes.
```

The skill will also suggest this snippet the first time it activates in a
new vault-embedded project.

## What the skill covers

- Obsidian Flavored Markdown: wikilinks, callouts, tags, tasks, frontmatter
- Safe output rules: minimum viable change, no silent renames, no type drift
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
