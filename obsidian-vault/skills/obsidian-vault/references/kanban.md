# Kanban Plugin Reference

The Kanban plugin renders markdown files as visual board views with draggable
cards organized into columns (lanes). The underlying file is plain markdown —
headings become lanes, list items become cards. This makes Kanban boards
programmatically editable, but the format is strict: malformed markdown breaks
the board view.

Read this before creating or editing any Kanban board file. An edit that looks
fine in source view can silently corrupt the board rendering.

**Official repo:** https://github.com/mgmeyers/obsidian-kanban

---

## File Structure

A Kanban board is a regular `.md` file with a specific frontmatter key and
a strict markdown structure:

```markdown
---

kanban-plugin: board

---

## Lane Name

- [ ] Card text
- [ ] Another card
- [x] Completed card


## Another Lane

- [ ] Card in second lane


## Archive

- [x] Archived card


%% kanban:settings
```
{"kanban-plugin":"board","key":"value"}
```
%%
```

### Required Elements

1. **Frontmatter:** Must contain `kanban-plugin: board` (or `kanban-plugin: basic`
   for the older format). This tells Obsidian to render the file as a board.
2. **Lanes:** Each `## Heading` becomes a column/lane on the board.
3. **Cards:** Each `- [ ] text` or `- text` list item under a lane becomes a card.
4. **Settings block:** A `%% kanban:settings ... %%` comment block at the very
   end of the file stores board configuration as JSON.

### Frontmatter Variants

```yaml
---
kanban-plugin: board       # Standard board view
---
```

The `board` value is the current standard. Older boards may use `basic`.
Do not add other frontmatter properties unless the user requests it — the
Kanban plugin only reads `kanban-plugin`.

---

## Card Syntax

### Basic Cards

```markdown
- [ ] Simple task card
- [x] Completed card
- A card without a checkbox
```

Cards can have checkboxes (`- [ ]`, `- [x]`) or be plain list items (`- `).
Whether checkboxes show depends on the board's `show-checkboxes` setting.

### Cards with Dates

```markdown
- [ ] Card with due date @{2025-02-01}
- [ ] Card with date link [[2025-02-01]]
```

The `@{date}` syntax is Kanban-specific. The plugin can also parse dates from
wikilinks if `link-date-to-daily-note` is enabled in board settings.

### Cards with Metadata

Cards support Kanban-specific metadata using `<!-- -->` HTML comments or
content after the card text:

```markdown
- [ ] Card text ^card-id
```

Block IDs (`^id`) on cards are preserved and can be linked from other notes.

### Cards with Tasks Plugin Emoji Syntax

If the Tasks plugin is active and the board uses checkboxes, emoji syntax
on cards is parsed by the Tasks plugin:

```markdown
- [ ] Fix login bug 📅 2025-02-01 ⏫
- [ ] Write tests ⏳ 2025-01-28 📅 2025-02-01
```

This means:
- Tasks queries can find items from Kanban boards
- Toggling checkboxes in the board triggers Tasks plugin behavior
- Dates, priority, and recurrence work as documented in `references/tasks.md`

### Multi-line Cards

Cards can contain sub-items and additional content:

```markdown
- [ ] Main card text
	- Sub-item one
	- Sub-item two
	- [ ] Sub-task
```

Sub-items are indented with a tab or spaces under the parent card.

---

## Lane Structure

### Standard Lanes

Lanes are `##` headings. The order of headings determines the left-to-right
order of columns on the board:

```markdown
## Backlog
## In Progress
## Review
## Done
```

### Archive Lane

The Kanban plugin supports a special archive lane. Archived cards are moved
here (typically via the board UI). The archive lane:
- Is usually named `## Archive` but the name is configurable
- May or may not be visible in the board view
- Stores completed/archived cards with optional dates

When `archive-with-date` is enabled in settings, archived cards get a date
appended:

```markdown
## Archive

- [x] Old task @{2025-01-15}
```

### Empty Lines Between Lanes

The Kanban plugin expects **two blank lines** between the last card of one lane
and the next lane heading. This is important when programmatically editing:

```markdown
## Lane One

- [ ] Card


## Lane Two

- [ ] Another card
```

Removing these blank lines can cause the board to render incorrectly.

---

## Board Settings Block

Every Kanban board file ends with a settings block wrapped in an Obsidian
inline comment (`%% ... %%`):

```markdown
%% kanban:settings
```
{"kanban-plugin":"board","link-date-to-daily-note":false,"move-dates":true,"new-note-folder":"path/to/folder","show-checkboxes":true}
```
%%
```

**Critical rule:** Never modify the settings block structure. If you need to
edit a board, leave the `%% kanban:settings ... %%` block exactly as-is.

Common settings keys:
| Key | Type | Description |
|---|---|---|
| `kanban-plugin` | String | Board type (`"board"`) |
| `show-checkboxes` | Boolean | Whether to show checkboxes on cards |
| `link-date-to-daily-note` | Boolean | Whether dates link to daily notes |
| `move-dates` | Boolean | Whether to move dates when cards are moved |
| `new-note-folder` | String | Folder for notes created from cards |
| `lane-width` | Number | Width of lanes in pixels |
| `show-relative-date` | Boolean | Show relative dates (e.g., "in 3 days") |

---

## Safe Editing Rules

When programmatically editing a Kanban board file:

### DO:
- Add new cards as `- [ ] text` list items under the appropriate lane heading
- Move cards between lanes by cutting from one lane's list and pasting under
  another lane's heading
- Edit card text in place
- Add or remove cards from any lane
- Preserve blank lines between lanes (two blank lines before each `##`)
- Preserve the settings block at the end of the file verbatim

### DO NOT:
- Change `##` headings without understanding they are lane names visible on
  the board
- Remove the `kanban-plugin: board` frontmatter
- Modify the `%% kanban:settings ... %%` block
- Add content between lanes that isn't a list item (no paragraphs, no
  sub-headings)
- Use `###` or deeper headings inside the board (the plugin only uses `##`)
- Remove the blank lines between the last card and the next lane heading
- Add YAML frontmatter properties other than `kanban-plugin` unless the user
  specifically requests it

### Moving Cards Between Lanes

To move a card from one lane to another, remove the list item from the source
lane and add it to the target lane:

**Before:**
```markdown
## In Progress

- [ ] Task A
- [ ] Task B


## Done
```

**After (moving Task A to Done):**
```markdown
## In Progress

- [ ] Task B


## Done

- [x] Task A
```

Note: When moving to a "done" lane, you may want to change `[ ]` to `[x]` if
the board uses checkboxes and the task is being marked complete. Check the
board's convention.

---

## Interaction with Other Plugins

### Kanban ↔ Tasks Plugin

Since Kanban cards are standard markdown list items, the Tasks plugin can
read them. This creates a natural workflow:

- **Board columns as workflow stages** — Instead of using custom task statuses
  (`[/]` for "in progress"), some users use Kanban lane position to indicate
  status. A task in the "In Progress" lane is in progress by virtue of its
  lane, not its checkbox character.
- **Tasks queries across boards** — A Tasks query like
  `path includes TASKS.md` finds all tasks on the board.
- **Emoji metadata on cards** — Adding `📅 2025-02-01 ⏫` to card text
  makes it queryable by the Tasks plugin.

### Kanban ↔ Dataview

Dataview can query Kanban boards, but it sees the raw markdown — it doesn't
understand lane structure. Dataview treats each card as a list item or task.
To query cards by lane, you'd need to use `section` metadata or parse the
heading context.

### Kanban ↔ Meta Bind

Meta Bind INPUT fields placed in a Kanban board file may not render correctly
in the board view (the board renders its own UI, not standard markdown). Avoid
placing Meta Bind syntax inside Kanban boards.

---

## Common Pitfalls

1. **Breaking blank lines** — Removing the blank lines between lanes causes
   the board to merge lanes or misrender. Always preserve `\n\n` between the
   last card and the next `##`.
2. **Editing the settings block** — The JSON in `%% kanban:settings ... %%`
   is the plugin's internal state. Editing it can break the board. Leave it
   alone.
3. **Sub-headings** — Using `###` inside a board creates unexpected rendering.
   The plugin only uses `##` for lanes.
4. **Mixed content** — Adding paragraphs, images, or code blocks between
   lanes (not as card content) confuses the board renderer.
5. **Frontmatter additions** — Adding properties beyond `kanban-plugin` to
   the frontmatter can interfere with the plugin's file detection. Some users
   do add properties (tags, aliases) successfully, but test before relying
   on it.
6. **Card indentation** — Sub-items must be indented with a tab or consistent
   spaces. Inconsistent indentation orphans sub-items.
