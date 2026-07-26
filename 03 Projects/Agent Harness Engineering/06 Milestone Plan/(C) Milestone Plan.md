# Milestone Plan — Agent Harness Engineering

> Living document. No dates, no weekly cadence — milestone-gated, same reasoning as Firmware Bring-Up: this is self-directed and time is irregular.
> Two phases: **Phase 1 (Learn)** then **Phase 2 (Build)** — don't blend them.

## Baseline (as of 2026-07-26)
- **Experience:** Used LLM agent tools (Claude Code, etc.) as a consumer, never built an agent orchestration system.
- **Stack preference:** Python.
- **Real API:** Yes, but only once the orchestration logic/structure is validated — not for early iteration.
- **Why this project exists:** Ties to the firmware career goal — the actual target is applying a working harness pattern at the day job to make firmware development more efficient. This isn't a toy exercise; the semiconductor-company simulation is a low-stakes proving ground for a pattern meant to be reused for real.

## The Target Shape
A **Product Leader** agent that can be asked technical questions directly, discuss details, gather information by delegating to **Component Technical Leader** agents (one per component of the simulated processor/product), each of whom delegates further to an **Implementer** + **Reviewer** pair. The Product Leader should be able to synthesize what comes back up the chain into a coherent plan, then delegate implementation accordingly — a real working hierarchy, not a scripted demo.

```
Product Leader (main orchestrator)
├── Component A Technical Leader (component orchestrator)
│   ├── Implementer
│   └── Reviewer
├── Component B Technical Leader
│   ├── Implementer
│   └── Reviewer
└── ...
```

## Phase 1 — Learn the Landscape

### Milestone 1 — Harness Landscape Survey (ACTIVE)
**Done when:** Have surveyed at least 3-4 real approaches to hierarchical multi-agent orchestration — e.g. the pattern Claude Code itself uses (main agent + subagents, tool scoping per agent type), the Claude Agent SDK, LangGraph, CrewAI, AutoGen — and can explain, for each, how it handles: (a) hierarchical delegation, (b) context/state handoff between orchestrator and worker, (c) tool/permission scoping per role, (d) a distinct "reviewer" role vs. an "implementer" role. Recorded as a comparison doc in `00 Harness Landscape/`.

### Milestone 2 — Architecture Design
**Done when:** A chosen stack is picked with explicit written reasoning (not just "this one seemed popular"), and the org chart above is mapped onto that stack's actual primitives — which construct is the Product Leader, which is a Component Lead, how does a spec flow down and a result flow back up, how is the Reviewer role kept distinct from the Implementer (separate context? separate permissions? separate prompt only?). Recorded in `01 Architecture Design/`. This is the design a stranger could pick up and implement from.

## Phase 2 — Hands-On Build

### Milestone 3 — Product Leader Prototype
**Done when:** A real, working Product Leader agent exists that can take a genuine technical question or spec (about the simulated semiconductor product) and produce a sensible delegation breakdown — even if the Component Lead / Implementer / Reviewer layers below it are stubbed or mocked at this stage. Real conversation, real reasoning, not a hardcoded response.

### Milestone 4 — First Vertical Slice
**Done when:** One full branch of the org chart — one Component Technical Leader with its Implementer + Reviewer — works end-to-end with real API calls. The Product Leader delegates a real task to this one branch and gets back an implemented-and-reviewed result it can present back.

### Milestone 5 — Scale Out
**Done when:** Additional Component Leads (mirroring Component B, C, ...) are added following the same pattern, and the Product Leader can coordinate multiple parallel component branches against a shared spec — this is where it stops being "one lucky vertical slice" and becomes a real generalizable pattern.

### Milestone 6 — Full Simulated Scenario
**Done when:** A complete simulated semiconductor product spec (from `05 Simulated Semiconductor Product/`) has been run through the full hierarchy end-to-end, friction points are documented, and there's a clear view of what would need to change to point this at a real firmware task at work.

## Log
_Add dated entries here as milestones progress or the plan gets adjusted._

- **2026-07-26** — Project created. Milestone 1 (Harness Landscape Survey) active.
