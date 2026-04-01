---
name: new-trip
description: Scaffold a new trip planning project with full directory structure
---

# New Trip

Scaffold a new trip planning project. Follow Phase 1 (Define) from the trip-orchestrator skill.

## Steps

1. **Gather the basics** through natural conversation:
   - Who's traveling? (names, ages, relationship to each other)
   - When? (dates or approximate timeframe, any flexibility?)
   - Where? (specific destinations, region, or "we don't know yet")
   - Budget? (total, currency, any points/miles programs?)
   - What matters most? (pace/style, interests, dietary needs)
   - Any constraints? (health/mobility, travel anxiety, first-time traveler, no driving, etc.)

   Ask follow-up questions. The more detail you get now, the better every future decision will be.

2. **Create the project structure:**
   ```
   CLAUDE.md
   TASKS.md
   dashboard.html     — Copy from plugin's assets/dashboard.html
   memory/
     people/
     projects/
     sessions/
     research/
     guides/
     plans/
     glossary.md
     reference/
   outputs/
     tickets/
     receipts/
   archive/
   ```

3. **Populate starter files** (see "File Conventions → What Goes Where" in the skill for detailed boundaries):
   - **Traveler profiles** in `memory/people/{name}.md` — the authoritative source for each person: health/mobility details, dietary needs + strictness, travel anxieties, interests, aesthetic preferences, non-negotiables, past travel experience
   - **Project file** in `memory/projects/{trip-name}.md` — trip overview, budget breakdown, key constraints, booking philosophy (once gathered). This file grows to hold all booking details, payment tracking, and decision rationale as the project progresses.
   - **Itinerary skeleton** in `memory/itinerary.md` — dates and duration, cities TBD
   - **CLAUDE.md** — lean project index (~200-300 lines). Sections: Me (one-line summary), People (summary table with pointers to profiles — NOT full details), Terms, Projects (one-line + pointer), Trip Planning Status (current phase, last session, snapshot table, open questions), Preferences (project-wide conventions only — NOT per-traveler preferences), File Map. Do NOT include session protocols, planning methodology, phase descriptions, or any "how to plan" content — the skill provides all of that.
   - **TASKS.md** — seeded with Phase 1 completion tasks and Phase 2 research tasks
   - **Glossary** in `memory/glossary.md` — any terms/abbreviations that came up

4. **Write the first session log** in `memory/sessions/session-log-{date}-a.md`

5. **Suggest next steps** — usually destination research if not decided, or route comparison if destinations are known but order/duration isn't set.

**Only create the files listed above.** Do not create meta files about the scaffolding process (INDEX.md, MANIFEST.txt, README.md, EXECUTION_SUMMARY.md, etc.). The project structure is self-documenting.
