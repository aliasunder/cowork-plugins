# trip-orchestrator

A Cowork skill that acts as an end-to-end trip planning project manager. It guides multi-session travel planning from first idea through departure day — tracking planning phases, managing dependencies between tasks, running session start/end protocols, and providing phase-appropriate methodology via reference files.

## Why this exists

Planning a multi-week international trip across 15+ Cowork sessions requires consistent structure: where does booking info go, when should restaurants be researched vs activities, how do you hand off context between sessions, and how do you make sure completed tasks actually get tracked. Without a skill, every session starts from scratch and the agent reinvents the wheel on file structure, task management, and research methodology.

This skill replaces 4 separate trip-planner plugin skills (trip-setup, research-workflow, itinerary-planning, session-management) with a single orchestrator that loads specialized reference files on demand.

## Structure

```
.claude-plugin/plugin.json            # Plugin manifest
skills/trip-orchestrator/
  SKILL.md                            # Core skill — phases, session protocol, file conventions
  references/
    research-methodology.md           # 6-phase research process
    materials-guide.md                # How to produce traveler-facing deliverables
    budget-tracking.md                # Budget table conventions
    phase-guide.md                    # Detailed per-phase guidance
    tasks-and-dashboard.md            # TASKS.md format and dashboard setup
commands/
  new-trip.md                         # Scaffold a new trip project
  session-start.md                    # Load context and summarize status
  session-end.md                      # Write session log and reconcile tasks
assets/
  dashboard.html                      # Standalone kanban board for TASKS.md
evals/                                # Test scenarios and results (see evals/README.md)
```

## How the skill works

The skill defines 8 planning phases with dependency rules:

1. **Define** — who, when, budget, constraints, booking philosophy
2. **Route** — destination research, city selection, night allocation
3. **Transport** — flights, trains, transfers
4. **Accommodation** — hotels/B&Bs per city
5. **Activities** — day-by-day schedules per city
6. **Restaurants** — dining research and reservation planning
7. **Pre-Trip Prep** — itinerary guides, daily cards, packing lists, language sheets
8. **Final Check** — verify everything, reconcile budget

Each session follows a rhythm: **orient** (read CLAUDE.md, TASKS.md, last session log, summarize) → **work** → **close** (write session log, update CLAUDE.md, reconcile tasks).

The skill keeps SKILL.md lean (~320 lines) by deferring detailed methodology to `references/` files that get loaded on demand — only when the agent enters a research task, creates a deliverable, or updates the budget.

## Key design decisions

- **Single skill, not four.** Skills can't invoke other skills at runtime in Cowork, so a multi-skill approach would lose context between phases. One skill with reference files gives the same modularity without the handoff problem.
- **File boundary rules.** CLAUDE.md is a lean index (~200-300 lines), not a knowledge base. People details live in `memory/people/`, booking details in `memory/projects/`, methodology in the skill. This prevents the common failure mode where CLAUDE.md becomes a 500-line dump of everything.
- **Research → Guide pipeline.** Research files are broad comparisons. Guides are curated picks created *after* the user approves recommendations. This prevents premature commitment and gives the user a decision point.
- **Booking philosophy gathered early.** Flexibility vs cost, hotel star preference, refundability appetite — gathered in Phase 1 because it shapes every downstream booking decision.
- **Task completion = physical move.** Completed tasks must be moved to the Done section with strikethrough and date, not just checkbox-checked in Active. This is the most common failure mode without the skill.

## Using the skill

Install as a Cowork plugin or load the SKILL.md directly. The skill auto-triggers when it detects a trip planning project (CLAUDE.md with traveler profiles, memory/ directory) or when the user says anything like "plan a trip", "start a session", "research hotels", etc.

To start a new trip: use the `new-trip` command or just tell the agent you want to plan a trip.

## Evals

See `evals/README.md` for how the skill was tested and how to interpret results.
