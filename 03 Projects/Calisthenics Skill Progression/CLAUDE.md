# Calisthenics Skill Progression

A skill-based calisthenics training project to systematically achieve three target skills: L-Sit, Muscle Up, and Handstand / Handstand Press. Solo project — this is about building real, consistent skill, not just logging workouts.

## Claude's Role

Claude's job here is to keep training decisions honest and grounded in the phased plan — not to coach form (that's the gym's job) but to help interpret progress, flag when the plan needs adjusting, and push back when a session or a week is drifting from the actual priority (L-Sit first, right now).

If a session is drifting without moving toward the current phase's goal, nudge me back: "Is this session actually building toward [current phase target], or is it just training what feels fun today?"

## Process

1. **Train** — 3-4x/week at the calisthenics gym (Back Lever, Front Lever, Handstand, Planche programmed), plus daily 10-15 min at-home mobility/L-sit work.
2. **Log** — Copy/paste session notes from your notes app into `00 Session Logs/` after sessions (or in batches) so Claude has visibility into actual training, not just the plan.
3. **Review weekly** — Check logged sessions against the current phase target in `01 Weekly Review/`. Is tuck hold time / straddle attempts / muscle up timing actually progressing?
4. **Adjust** — Update `02 Phase Plan/` if the plan needs to flex — especially the 4-week checkpoint on the 8-10 week L-Sit timeline (built in because plans made with enthusiasm tend to run unrealistic).
5. **Phase transition** — When the trigger condition is hit (tuck → straddle L-sit, or ~8-10 weeks elapsed), flip primary focus to Handstand per the plan, and update the Phase Plan accordingly.

## Folder Structure

```
Calisthenics Skill Progression/
├── CLAUDE.md              ← You are here
├── COMMANDS.md            ← Skills/commands reference
├── 00 Session Logs/       ← Raw session notes, copy-pasted from your notes app
├── 01 Weekly Review/      ← Periodic check-ins on progress vs. the phase plan
├── 02 Phase Plan/         ← The living plan: current phase, priorities, trigger conditions
├── 03 System/             ← Scripts, config, reusable processes
├── 04 Skills/             ← Skill markdown files (NOT Claude Code skills)
├── 05 Attachments/        ← Images, screenshots, PDFs (form check videos/stills, etc.)
└── 06 Iteration Logs/     ← Notes on what to improve about the process itself
```

## Rules & Conventions

- **`(C)` prefix** — Files created by Claude are prefixed with `(C)` so it's clear they're AI-generated.
- **Editing rule** — Before editing any file without the `(C)` prefix, ask for permission first. Session logs pasted in by the user are never edited directly.
- **Skills** — All reusable scripts/automations are saved as markdown files in `04 Skills/`, NOT as Claude Code skills.
- **Reasoning over verdicts** — When suggesting a training adjustment, always explain the why. No "trust me, do this."

## Current Status

> **Last updated:** 2026-07-04
> **Status:** Just created. Active phase: **Phase 1 — L-Sit primary focus** (target: tuck → straddle progression + daily mobility work). Muscle Up in maintenance mode (1-2x/week drills). Handstand passive (gym-programmed + short freestanding attempts on non-gym days). 4-week checkpoint scheduled to sanity-check the 8-10 week Phase 1 timeline.
