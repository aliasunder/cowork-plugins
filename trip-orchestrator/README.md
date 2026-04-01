# trip-orchestrator

A Cowork skill that acts as an end-to-end trip planning project manager. It guides multi-session travel planning from first idea through departure day — tracking planning phases, managing dependencies between tasks, running session start/end protocols, and providing phase-appropriate methodology via reference files.

## Why this exists

Planning a multi-week international trip across 15+ Cowork sessions requires consistent structure: where does booking info go, when should restaurants be researched vs activities, how do you hand off context between sessions, and how do you make sure completed tasks actually get tracked. Without a skill, every session starts from scratch and the agent reinvents the wheel on file structure, task management, and research methodology.

Rather than splitting this across multiple independent skills, it uses a single orchestrator that loads specialized reference files on demand — keeping the full workflow context available in every session.

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

## Why a single orchestrator, not multiple skills

Most Cowork plugins use a **toolkit pattern** — a collection of independent skills where each handles one task (review code, run incident response, research hotels). That works well when skills are genuinely independent.

Trip planning is different. It's a **sequential, phase-dependent workflow** that runs across many sessions: you can't research restaurants before choosing cities, can't build an itinerary before booking hotels, and every session needs to pick up exactly where the last one left off. A multi-skill approach (separate skills for setup, research, itinerary building, session management) breaks down because:

- **Skills can't invoke each other at runtime.** When the agent activates a research skill, it doesn't see the session management skill's rules for how to close a session.
- **The agent has to guess which skill to use.** A user saying "let's pick up where we left off and research restaurants" needs both session management and research, but only one skill loads.
- **Cross-skill consistency relies on file contracts.** Multi-skill plugins coordinate through shared files (CLAUDE.md, TASKS.md, memory/), which works if every skill respects the same conventions — but the agent only sees one skill's instructions at a time.

The orchestrator pattern solves this by putting the full workflow context — phases, dependencies, session protocol, file boundary rules — in a single skill, and deferring detailed methodology to reference files loaded on demand. This gives the same modularity as separate skills without the handoff problem.

**When to use which pattern:**

- **Toolkit (multiple skills):** Independent capabilities that don't depend on each other. Good for: engineering plugins (code-review, incident-response), productivity tools.
- **Orchestrator (single skill + references):** Sequential workflows with phase dependencies and multi-session state. Good for: project management, trip planning, any multi-week planning process.

## Other key design decisions

- **File boundary rules.** CLAUDE.md is a lean index (~200-300 lines), not a knowledge base. People details live in `memory/people/`, booking details in `memory/projects/`, methodology in the skill. This prevents the common failure mode where CLAUDE.md becomes a 500-line dump of everything.
- **Research → Guide pipeline.** Research files are broad comparisons. Guides are curated picks created *after* the user approves recommendations. This prevents premature commitment and gives the user a decision point.
- **Booking philosophy gathered early.** Flexibility vs cost, hotel star preference, refundability appetite — gathered in Phase 1 because it shapes every downstream booking decision.
- **Task completion = physical move.** Completed tasks must be moved to the Done section with strikethrough and date, not just checkbox-checked in Active. This is the most common failure mode without the skill.

## Using the skill

Install as a Cowork plugin or load the SKILL.md directly. The skill auto-triggers when it detects a trip planning project (CLAUDE.md with traveler profiles, memory/ directory) or when the user says anything like "plan a trip", "start a session", "research hotels", etc.

To start a new trip: use the `new-trip` command or just tell the agent you want to plan a trip.

## Evals

See `evals/README.md` for how the skill was tested and how to interpret results.
