---
name: obsidian-vault
description: >
  Edit notes in an Obsidian vault. Use for: creating or editing .md files that
  will render in Obsidian, writing or repairing frontmatter/properties, writing
  wikilinks, embeds, callouts, tasks, block references, tags, Mermaid diagrams,
  or any Obsidian Flavored Markdown. Also use for plugin-aware tasks: writing
  Dataview queries, Tasks plugin syntax, Templater templates, Bases property
  schemas, or Canvas JSON.

  NOT for: .md files outside an Obsidian vault (READMEs in code repos, GitHub
  issue bodies, static-site content like Hugo or Jekyll, project documentation
  in non-vault repos), generic markdown linting unrelated to Obsidian rendering,
  or Obsidian plugin development (that is a separate TypeScript skill).

  ALWAYS use this skill when working in an Obsidian vault — detected by the
  presence of a .obsidian/ directory in the working directory or any ancestor
  (Cowork projects often live inside a vault as a subdirectory), a CLAUDE.md
  that identifies the project as living inside a vault, or any request involving
  .md files where Obsidian will be the renderer (wikilinks, frontmatter,
  callouts, embeds). Also trigger when the user mentions anything like "create
  a note", "update frontmatter", "write a Dataview query", "fix this callout",
  "add a task", or any note-editing task.

  If a vault-embedded Cowork project exists in the working directory, this skill
  should be active — period.
---

# Obsidian Vault Skill

You are working inside an Obsidian vault. Your job is to create and edit notes
that render correctly, stay plugin-compatible, and follow vault conventions
consistently across sessions.

## How This Skill Works

This skill contains the core conventions and safe editing rules you need for
every note interaction. For detailed syntax and plugin-specific rules, it points
to reference files in `references/` that you load when needed — not upfront.

**Reference files** (read on demand — but always read before acting, not after):
- `references/syntax.md` — Full Obsidian Flavored Markdown syntax: block
  references, embeds, Mermaid diagrams, inline comments, advanced wikilink
  patterns. **Read before working with any of these constructs.**
- `references/plugins.md` — Plugin-aware conventions: Dataview, Tasks, Templater,
  Bases, Canvas. **Read before writing plugin syntax, queries, or templates.**
- `references/properties.md` — Full YAML property type reference: reserved keys,
  type rules, edge cases, common schemas. **Read before creating or repairing
  frontmatter, or when designing a property schema.**

These files contain the detailed rules that prevent silent rendering errors and
plugin-incompatible output. Reading them after you've already written something
means you'll need to redo it.

---

## Guiding Principles

1. **Render safety first.** Never write Markdown that Obsidian core or installed
   plugins will misparse or silently corrupt.
2. **Minimum viable change.** When editing an existing note, change only what was
   asked. Do not reorder sections, alter heading hierarchy, or modify frontmatter
   keys unless explicitly requested.
3. **Prefer wikilinks for internal references.** Use `[[Note Name]]` over relative
   markdown links for anything that lives inside the vault.
4. **Keep metadata stable.** Preserve existing frontmatter keys and their types.
   Add new keys only at the end of the frontmatter block.
5. **Ask before restructuring.** If a request implies renaming notes, splitting
   files, or changing folder structure, confirm before acting — these operations
   can break links vault-wide.
6. **One convention per vault.** If the vault uses Tasks plugin emoji syntax,
   don't introduce plain checkboxes. If it uses Dataview inline fields, don't
   add YAML-only properties. Read existing notes before writing new ones.

---

## Core Obsidian Flavored Markdown

### Frontmatter / Properties

Always placed at the very top of the file, fenced with `---`, before any content.

```yaml
---
title: Note Title
date: 2025-01-15
tags:
  - project
  - active
aliases:
  - Alternative Name
status: in-progress
---
```

**Core property rules:**
- `tags` and `aliases` — always YAML lists, never inline strings
- Dates — ISO 8601: `2025-01-15` or `2025-01-15T14:30:00`
- Booleans — lowercase `true` / `false`
- Numbers — unquoted: `rating: 4.5`
- Links in properties — always quoted: `related: "[[Other Note]]"`
- No wikilinks inside `tags` or `aliases` values

For the full property type reference and reserved keys, read
`references/properties.md` before creating or repairing frontmatter.

---

### Internal Links (Wikilinks)

```markdown
[[Note Name]]                    Basic link
[[Note Name|Display Text]]       Link with alias
[[Note Name#Heading]]            Link to heading
[[#Heading in same note]]        Same-note heading link
```

- Always prefer `[[Note Name]]` over `[text](Note%20Name.md)` for internal links
- Use `|Display Text` when the note name is awkward in prose
- Do not add `.md` extensions to wikilinks

For block references, embeds, and advanced link patterns, read `references/syntax.md`.

---

### Callouts

```markdown
> [!note]
> Default callout with no title.

> [!tip] Custom Title
> Callout with a title.

> [!warning]- Collapsed by default
> Hidden until expanded.

> [!info]+ Expanded by default
> Visible but collapsible.
```

**Supported types:** `note`, `info`, `tip` / `hint` / `important`,
`abstract` / `summary` / `tldr`, `todo`, `success` / `check` / `done`,
`question` / `help` / `faq`, `warning` / `caution` / `attention`,
`failure` / `fail` / `missing`, `danger` / `error`, `bug`, `example`,
`quote` / `cite`

Use callouts for flagged content. Do not substitute callouts for headings.

---

### Tasks

Plain Obsidian tasks:
```markdown
- [ ] Incomplete task
- [x] Completed task
```

If the Tasks plugin is active, use its extended syntax. Read `references/plugins.md`
before writing Tasks plugin syntax.

---

### Tags

```markdown
#tag
#nested/tag
#tag-with-hyphens
```

- In frontmatter: always under `tags:` as a list
- Nested with `/` separator: `#project/active`
- No spaces; use hyphens or underscores

---

## Safe Output Rules

Before returning any `.md` file you wrote — whether new or edited — verify:

- [ ] New note opens with a fenced frontmatter block (match the vault's existing note schema — read a nearby note first if the schema isn't obvious)
- [ ] Frontmatter is at the very top, properly fenced with `---`
- [ ] Frontmatter property types consistent with other notes (no silent string ↔ number ↔ list drift — this is the specific failure mode that breaks Dataview and Bases queries later)
- [ ] No new frontmatter keys added unless requested (when editing an existing note)
- [ ] No existing frontmatter keys removed or renamed (when editing an existing note)
- [ ] Existing wikilinks are intact — not converted to markdown links (when editing an existing note)
- [ ] Block IDs (`^id`) are preserved if present (when editing an existing note)
- [ ] Callout syntax is valid (`> [!type]`)
- [ ] Task syntax is consistent with the vault's convention
- [ ] Tags use correct format (no spaces, nested with `/`)
- [ ] Inline comments (`%% ... %%`) preserved if present (when editing an existing note)
- [ ] No accidental section duplication (when editing an existing note)

---

## Note Audit

When asked to review a note for convention issues:

1. Check frontmatter: valid YAML, correct types, no wikilinks in tags/aliases,
   fenced correctly
2. Check links: internal references are wikilinks, not markdown links
3. Check callout syntax
4. Check task syntax consistency
5. Check tag format
6. Check for any markdown that will render incorrectly in Obsidian

Report each issue with its location and the corrected version. Do not edit
the file — only report unless told to fix.

---

## Setting Up a New Vault-Embedded Cowork Project

If a Cowork project has been created in (or alongside) a vault and doesn't
yet have an Obsidian-aware CLAUDE.md:

1. **Check for `.obsidian/` in the working directory or any ancestor
   directory** — a vault often contains many Cowork projects as
   subdirectories, so `.obsidian/` may live one or more levels above the
   Cowork project root
2. **Read any existing notes** in the vault to understand the vault's
   current conventions before writing anything new
3. **Recommend a project CLAUDE.md** — suggest the user add this to their
   Cowork project settings:

   ```
   This project lives inside an Obsidian vault (parent directory).

   Use the obsidian-vault skill for all .md file creation and editing in
   this project — notes, session logs, CLAUDE.md/TASKS.md, and any
   operational docs (they all render in Obsidian since the project lives
   in the vault).
   Preserve existing links, frontmatter, and note structure unless asked
   otherwise.
   Ask before renaming notes, moving files, or making vault-wide
   structural changes.
   ```

4. **Note what plugins appear active** based on vault files (`.obsidian/plugins/`
   or existing note syntax) and flag which reference files will be relevant

---

## What This Skill Does Not Do

- Rename or move files — always ask the user to use Obsidian's rename function
  to preserve link integrity
- Treat `.canvas` files as plain text — use the Canvas JSON rules in `references/plugins.md`
- Add Templater syntax to non-template notes
- Reformat an entire vault or batch-rename notes without explicit confirmation
- Change existing frontmatter property types without confirmation
