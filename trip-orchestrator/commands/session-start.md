---
name: session-start
description: Load context and summarize trip planning status
---

# Session Start

Follow the session start protocol from the trip-orchestrator skill:

1. **Read CLAUDE.md** — current phase, last session pointer, open questions, preferences
2. **Read TASKS.md** — active, blocked, next, done
3. **Read the last session log** (follow the pointer in CLAUDE.md)
4. **Check for new trip-related information** — if email tools are available, check for booking confirmations or changes using the trip's email label
5. **Validate phase state** — does the declared phase match what actually exists on disk? Flag any discrepancies.
6. **Summarize** in 10-15 lines: current phase, top active tasks, blockers/deadlines, suggested focus. Ask the user if they want to go with that or redirect.

Keep it conversational. The user has been living this trip — they need a quick refresh, not a formal briefing.
