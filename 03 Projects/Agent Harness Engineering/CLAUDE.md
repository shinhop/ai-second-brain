# Agent Harness Engineering

A hands-on project to learn how modern LLM agent orchestration harnesses actually work, by simulating a semiconductor company's engineering org as a hierarchy of agents: a Product Leader (main orchestrator) who delegates to Component Technical Leaders (component orchestrators), each of whom delegates to an Implementer + Reviewer pair. Ties directly to the firmware career goal — the end target is applying this at the day job to make firmware development more efficient, not just a toy exercise. Solo project, self-directed, milestone-gated (no calendar cadence, matching the same reasoning as the Firmware Bring-Up project).

## Claude's Role

Claude's job here is to be both the research partner for Phase 1 (surveying harness options with real trade-off reasoning, not just naming frameworks) and the architecture sounding board for Phase 2 (does this design actually reflect the org chart, does the context/permission model make sense, is a milestone genuinely done or just "it ran once").

If a session is drifting without moving toward the current milestone, nudge me back: "Is this exploring the harness landscape / building toward [current milestone], or is it scope creep into a feature that isn't needed yet?"

## Process

This project has two distinct phases — don't blend them:

**Phase 1 — Learn the Landscape (current)**
1. Survey existing approaches to hierarchical multi-agent orchestration (e.g. the pattern Claude Code itself uses for subagents, Claude Agent SDK, LangGraph, CrewAI, AutoGen, raw tool-use architectures).
2. For each, evaluate specifically against what this project needs: hierarchical delegation, context/state handoff between orchestrator and workers, tool/permission scoping per role, and support for a "reviewer" role distinct from an "implementer" role.
3. Land on a chosen stack with explicit reasoning — recorded in `00 Harness Landscape/`.
4. Design the architecture — map Product Leader → Component Leads → Implementer/Reviewer onto the chosen framework's actual primitives (agents, tools, sub-agents, message passing) — recorded in `01 Architecture Design/`.

**Phase 2 — Hands-On Build**
5. Build bottom-up in vertical slices, not horizontally across all roles at once: first a working Product Leader that can take a real technical question/spec and produce a sensible delegation plan (even with stubbed lower levels), then one full vertical branch (one Component Lead + its Implementer/Reviewer pair) end-to-end with real API calls, then generalize to additional Component Leads.
6. Use the `05 Simulated Semiconductor Product/` folder to hold the mock product spec(s) that drive the simulation — the scenario the org chart is "building."
7. Real Claude API calls once the structure is proven — don't burn API budget iterating on orchestration logic that stubbed responses can validate just as well.

## Folder Structure

```
Agent Harness Engineering/
├── CLAUDE.md                              ← You are here
├── COMMANDS.md                            ← Skills/commands reference
├── 00 Harness Landscape/                  ← Phase 1: survey of orchestration approaches, trade-offs, chosen stack + reasoning
├── 01 Architecture Design/                ← Phase 1: org-chart-to-framework mapping, message/context flow design
├── 02 Product Leader/                     ← Phase 2: top-level orchestrator implementation
├── 03 Component Leads/                    ← Phase 2: mid-level orchestrator implementations (one per component)
├── 04 Implementer and Reviewer Agents/    ← Phase 2: worker-role implementations
├── 05 Simulated Semiconductor Product/    ← The mock company/product spec driving the simulation
├── 06 Milestone Plan/                     ← Living plan: phases, milestones, done-criteria
├── 07 System/                             ← Scripts, env/config, API key setup notes (never actual secrets)
├── 08 Skills/                             ← Skill markdown files (NOT Claude Code skills)
├── 09 Attachments/                        ← Diagrams, screenshots, architecture sketches
└── 10 Iteration Logs/                     ← Notes on what to improve about the process itself
```

## Rules & Conventions

- **`(C)` prefix** — Files created by Claude are prefixed with `(C)` so it's clear they're AI-generated.
- **Editing rule** — Before editing any file without the `(C)` prefix, ask for permission first.
- **Skills** — All reusable scripts/automations are saved as markdown files in `08 Skills/`, NOT as Claude Code skills.
- **No secrets in the vault** — API keys/credentials live in local env vars or a gitignored `.env`, never written into notes.
- **No calendar pressure** — Progress is measured by milestone completion only, same reasoning as the Firmware Bring-Up project.
- **Don't blend phases** — Phase 1 is research/decisions, Phase 2 is building. Resist the urge to start coding before the architecture decision is actually made.
- **Reasoning over verdicts** — When comparing harness options or proposing a design, always explain the why and the trade-off, not just a recommendation.

## Current Status

> **Last updated:** 2026-07-26
> **Status:** Just created. Active milestone: **Milestone 1 — Harness Landscape Survey** (Phase 1).
