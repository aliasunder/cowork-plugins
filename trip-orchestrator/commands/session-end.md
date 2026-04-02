---
name: session-end
description: Write session log and update project files
---

# Session End

Follow the session end protocol from the trip-orchestrator skill:

1. **Review the session's accomplishments** — tasks completed, files created/modified, decisions made, bookings placed

2. **Write a session log** to `memory/sessions/session-log-{date}-{sequence}.md`:
   - **Summary**: 2-3 sentences on what happened
   - **Changes Made**: Bullet list of files created/modified
   - **Key Decisions**: What was decided and *why* (most important section — future sessions depend on it)
   - **Open Items**: Anything unresolved
   - **Next Session**: What to focus on next

3. **Update CLAUDE.md**:
   - Update "Last session" pointer to the new session log
   - Update the phase if it changed
   - Move resolved items from Open Questions to Resolved (keep last ~2 sessions of resolved items)
   - Update the File Map if new files were created
   - Update budget numbers if bookings were made

4. **Reconcile TASKS.md**:
   - Mark completed tasks as done (move to Done section with completion date)
   - Add any new tasks that emerged during the session
   - When the last subtask of a parent task is done, close the parent immediately
   - Reorder Up Next based on current priorities

Write all files directly — don't show a verbose preview of what will go in each file before writing. A brief one-liner about what you're updating is fine, but the session log and file updates should just happen.
