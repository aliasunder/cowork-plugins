# Plugin-Aware Conventions

Detailed conventions for working with Obsidian community and core plugins.
Read this before writing plugin-specific syntax, queries, or templates —
output that looks correct but breaks plugin assumptions is harder to debug
than output that is obviously wrong.

---

## Dataview

Dataview reads frontmatter properties and inline fields to build dynamic
views. Property names and types must be consistent across all notes in a
query set — inconsistency causes silent query failures, not visible errors.

### Frontmatter Properties for Dataview

Keep property names consistent across notes (case-insensitive but match
casing for readability). Avoid special characters — use hyphens, not spaces.

Date properties must be valid ISO 8601 for date comparisons:
```yaml
due: 2025-02-01        ✓
due: Feb 1, 2025       ✗  (won't compare correctly)
```

`tags` and `aliases` must be lists — Dataview treats a string value as a
single-item list but this can cause unexpected query behavior.

### Inline Fields

For properties that don't belong in frontmatter, use inline fields anywhere
in the note body:

```markdown
Rating:: 4
Author:: [[Jane Smith]]
Due:: 2025-02-01
Status:: active
```

- Double colon `::` with a space after
- Can appear anywhere in the note body
- Dataview treats them the same as frontmatter properties
- Links in inline fields don't need quoting

### Dataview Query Syntax

````markdown
```dataview
LIST FROM #project
WHERE status = "active"
SORT date DESC
```

```dataview
TABLE status, tags, due FROM "Projects"
WHERE due <= date(today) + dur(7 days)
SORT due ASC
```

```dataview
TASK FROM #project
WHERE !completed
GROUP BY file.name
```
````

**Query types:** `LIST`, `TABLE`, `TASK`, `CALENDAR`

**Common functions:**
- `date(today)`, `date(now)`, `dur(1 week)`, `dur(3 days)`
- `contains(tags, "value")`, `length(file.outlinks)`
- `file.name`, `file.path`, `file.folder`, `file.mtime`, `file.ctime`

**Common pitfalls:**
- `FROM "folder"` uses the folder path relative to vault root — include
  quotes and match case exactly
- `FROM #tag` matches the tag and all sub-tags (`#project` matches `#project/active`)
- Dataview doesn't update in real time in edit mode — switch to reading view

### DataviewJS

For complex views, DataviewJS runs JavaScript with full Dataview API access:

````markdown
```dataviewjs
const pages = dv.pages("#project").where(p => p.status === "active");
dv.table(["Name", "Status", "Due"], 
  pages.map(p => [p.file.link, p.status, p.due])
);
```
````

---

## Tasks Plugin

The Tasks plugin extends plain checkboxes with scheduling, recurrence, and
filtering. If the vault uses Tasks plugin, use its emoji syntax — don't mix
with plain checkboxes in the same query context.

### Task Emoji Syntax

```markdown
- [ ] Task description 📅 2025-02-01 ⏫
- [ ] Scheduled task ⏳ 2025-01-28 📅 2025-02-01
- [ ] Recurring task 🔁 every week 📅 2025-02-01
- [x] Completed task ✅ 2025-01-30
```

**Emoji meanings:**
| Emoji | Meaning |
|---|---|
| 📅 | Due date |
| ⏳ | Scheduled date |
| 🛳 | Start date |
| ✅ | Completion date |
| 🔁 | Recurrence rule |
| ⏫ | High priority |
| 🔼 | Medium priority |
| 🔽 | Low priority |
| ⏬ | Lowest priority |
| 🔺 | Highest priority |
| 🆔 | Task ID |
| ⛔ | Depends on (task ID) |

**Recurrence examples:**
```
🔁 every day
🔁 every week on Monday
🔁 every month on the 1st
🔁 every 2 weeks
```

### Tasks Query Blocks

````markdown
```tasks
not done
due before tomorrow
tags include #project
```

```tasks
done
done after 2025-01-01
sort by done date reverse
```
````

**Common filters:** `not done`, `done`, `due before [date]`, `due after [date]`,
`scheduled before [date]`, `has due date`, `no due date`, `tags include #tag`,
`path includes folder`, `heading includes text`

---

## Templater

Templater templates use `<% %>` delimiters. Do not include Templater syntax
in regular notes — it renders as literal text outside of template execution.

Template files live in the Templates folder (configured in Templater settings).

### Common Templater Expressions

```markdown
---
title: <% tp.file.title %>
date: <% tp.date.now("YYYY-MM-DD") %>
created: <% tp.file.creation_date("YYYY-MM-DD") %>
tags:
  - 
---

# <% tp.file.title %>

Created: <% tp.date.now("YYYY-MM-DD") %>
```

**Common functions:**
- `tp.file.title` — note title (filename without extension)
- `tp.file.folder(true)` — full folder path
- `tp.date.now("YYYY-MM-DD")` — current date, formatted
- `tp.date.tomorrow("YYYY-MM-DD")` — tomorrow's date
- `tp.file.creation_date("YYYY-MM-DD")` — file creation date
- `tp.system.prompt("Enter value")` — prompt user for input
- `tp.system.suggester(["A","B"], ["a","b"])` — show selection list

**User prompts in templates:**
```markdown
status: <% await tp.system.suggester(
  ["Active", "Archived", "Someday"],
  ["active", "archived", "someday"]
) %>
```

**Running JavaScript in Templater:**
```markdown
<%*
const name = await tp.system.prompt("Project name");
tR += name.toLowerCase().replace(/ /g, "-");
%>
```

---

## Bases (Obsidian 1.8+)

Bases are `.base` files that define database views over vault notes. They
query frontmatter properties. Property schemas must be consistent across
all notes in a Bases source folder.

**Property type compatibility:**
- Text properties: any string value
- Number properties: numeric values only — don't mix strings and numbers
- Checkbox properties: `true` / `false` only
- Date properties: ISO 8601 format only
- Multi-select: YAML list only — not comma-separated strings

**Common pitfalls:**
- A property defined as a number in one note but a string in another breaks
  Bases filtering and sorting for that column
- Bases reads the folder you point it at — all notes in that folder appear
  as rows, whether or not they have the expected properties
- Missing properties show as empty cells, not errors

**When designing a property schema for a Bases folder:**
- Define all expected property names and types upfront
- Use a template (Templater or core Templates) to ensure new notes start
  with the correct property structure
- Document the schema in a README note in that folder

---

## Canvas

Canvas files are JSON (`.canvas`), not Markdown. Do not attempt to read or
write `.canvas` files as plain text — use the JSON Canvas open spec.

**Node types:** `text`, `file`, `link`, `group`

**Minimal valid canvas structure:**
```json
{
  "nodes": [
    {
      "id": "unique-node-id",
      "type": "text",
      "text": "Node content",
      "x": 0, "y": 0,
      "width": 250, "height": 60
    }
  ],
  "edges": []
}
```

**Edge structure:**
```json
{
  "id": "unique-edge-id",
  "fromNode": "node-id-a",
  "fromSide": "right",
  "toNode": "node-id-b",
  "toSide": "left"
}
```

**Rules:**
- Every node and edge needs a unique `id` — use random strings, not sequential integers
- Edge `fromNode` and `toNode` must reference valid node IDs
- `fromSide` / `toSide` values: `top`, `right`, `bottom`, `left`
- `file` nodes use a `file` field with the vault-relative path: `"file": "Notes/My Note.md"`
- `link` nodes use a `url` field: `"url": "https://example.com"`
- Groups use `type: "group"` with a `label` field

When asked to create or modify a Canvas, write the JSON directly — do not
suggest the user click through the UI unless they ask for guidance.
