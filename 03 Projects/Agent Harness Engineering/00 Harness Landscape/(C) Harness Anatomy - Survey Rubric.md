---
created: 2026-07-26
phase: 1
milestone: 1
type: rubric
tags: [harness, agents, orchestration, phase-1]
---

# Harness Anatomy — Survey Rubric

> Purpose: a fixed yardstick for Milestone 1. Every framework surveyed gets scored against the **six decision axes** below, so the comparison doc is an evaluation rather than a list of feature bullets.
>
> Source: brainstorm session 2026-07-26. Expands Milestone 1's original criteria (a)–(d) to six.

## Premise corrections (read first)

Two framing errors that would otherwise propagate into the architecture:

**1. "LLMs work better with less context" is imprecise.**
They degrade on *irrelevant, stale, or contradictory* context, not on length per se. Three distinct failure modes hide under "too much context", and they have **different** fixes:

| Failure mode   | What happens                                               | What actually fixes it                              |
| -------------- | ---------------------------------------------------------- | --------------------------------------------------- |
| Distraction    | Signal buried in noise; model latches onto the wrong thing | Better retrieval / assembly ordering                |
| Poisoning      | A wrong fact enters early and persists as ground truth     | **Fresh context only.** Summarization entrenches it |
| Cost / latency | Every turn re-pays for the whole window                    | Compaction, prompt caching                          |

Consequence: summarization and isolation are **not** interchangeable tools.

**2. Multi-agent is not a context-management technique.**
Context isolation is a *side effect* of subagents, not their purpose. Their real cost: **the parent only ever sees a lossy summary of what the child saw.**

- Good fit → subtask is read-heavy and its output is genuinely compressible (research, review, "find where X is defined")
- Bad fit → subtasks must share evolving state (two Implementers editing coupled code)

Consequence for this project: the **Implementer + Reviewer pair is a strong fit**. **Parallel Component Leads at Milestone 5 are the danger zone** — that's where lossy handoff and concurrent writes collide.

## The full anatomy (6 layers)

Reference map of what a harness is made of. Not everything here discriminates between frameworks — see the axes section for what actually gets scored.

### L0 — The loop
The only true primitive: `model → tool call → result → model → …` until a stop condition. Stop conditions, max turns, malformed-tool-call handling. Everything else is scaffolding around this.

### L1 — What the model sees
- Context **assembly** — what enters the window each turn, in what order
- Context **compaction** — summarize/truncate under window pressure
- Context **offloading** — filesystem/notes as external memory
- Context **isolation** — fresh windows via subagents
- **Retrieval strategy** — on-demand `grep`/`glob` vs. pre-loaded vector RAG
- **Prompt caching** — changes the economics, which changes which strategies are affordable
- **System prompt / role definition** — least glamorous, highest leverage

### L2 — What the model can do
- Tool design: granularity, schema, and **error messages as a feedback channel**
- MCP as the tool-distribution protocol
- **Permission scoping** — per-role allowlists, path scoping, approval gates
- Sandboxing — containers, git worktrees, isolated filesystems

### L3 — How work is split
- Topology: hierarchical / flat / explicit graph
- **The delegation contract** — free text or structured object down? What schema comes back up?
- Parallelism, fan-in / synthesis, and who owns the plan
- Failure semantics — child times out or returns garbage: retry, escalate, abort?

### L4 — How you know it worked
- **Ground truth / verification** — compiler, tests, linter, QEMU boot
- Observability — traces, token accounting, replay
- **Evals** — how do you know a harness change improved anything?

### L5 — Operational
- Model routing per role (cheap for grunt work, strong for the Leader)
- Budget caps: tokens, turns, wallclock, dollars
- Retries, rate limits, backoff
- Persistence / resume mid-run; concurrent-write conflicts
- Human interface — the Product Leader must be *conversable*, not just a batch entrypoint

## The six decision axes (scoring rubric)

Each framework surveyed is scored on these. Anything not on this list is a tiebreaker, not a decision input.

### Axis 1 — Hierarchical delegation
Can an agent spawn and coordinate agents, and can *those* spawn agents in turn?
- [ ] Is 2-level delegation supported? 3-level (Leader → Lead → Implementer)?
- [ ] Is nesting a first-class primitive or a hack (agent-as-a-tool)?
- [ ] Sync or async children? Can a parent fan out in parallel and wait?

### Axis 2 — The delegation contract
**This is the actual architecture.** The diagram is trivial; the schema crossing the boundary is the engineering.
- [ ] What goes down: free-form prompt, or typed/structured task object?
- [ ] What comes back up: raw transcript, model-authored summary, or a schema-validated result?
- [ ] Can the parent inspect the child's intermediate steps, or only its final output?
- [ ] How is shared context (the product spec) passed without re-sending it to every child?

### Axis 3 — Context isolation & handoff
- [ ] Does each agent get a genuinely fresh window, or a shared/appended thread?
- [ ] Is compaction automatic, manual, or absent? Can it be overridden per role?
- [ ] Is there a supported external-memory pattern (filesystem, artifact store, scratchpad)?
- [ ] Does the design survive prompt caching, or does it invalidate the cache every turn?

### Axis 4 — Tool & permission scoping per role
**The mechanism that makes Reviewer genuinely ≠ Implementer.**
- [ ] Can tools be granted per agent type, not just globally?
- [ ] Can an agent be made **read-only** by construction?
- [ ] Path/directory scoping? Sandbox or worktree isolation?
- [ ] Approval gates / human-in-the-loop hooks?
> Design stance: if the only difference between Reviewer and Implementer is the prompt, that's one agent wearing two hats. **Reviewer should be read-only by tool grant.**

### Axis 5 — Verification & ground truth
- [ ] Can non-LLM verifiers (build, tests, linter, QEMU boot) be wired into the loop as gates?
- [ ] Can a failing gate force a retry/revision cycle automatically?
- [ ] Is the Reviewer a real gate, or advisory text the Implementer can ignore?
> An LLM Reviewer is a **weak** verifier next to a build that fails. For the firmware day-job target, compiler + static analysis + QEMU boot are worth more than any reviewer prompt.

### Axis 6 — Evals, observability & replay
- [ ] Traces: can a full run be inspected after the fact?
- [ ] Token/cost accounting per agent?
- [ ] **Deterministic replay with stubbed model responses?**
- [ ] Any eval harness, or is it roll-your-own?
> Direct dependency of the project rule *"real API only once the structure is validated."* That rule is only executable if a stubbed-replay path exists. If a framework can't do this, that's a real cost.

## Scoring table (fill during survey)

| Framework | A1 Hierarchy | A2 Contract | A3 Context | A4 Permissions | A5 Verification | A6 Evals/Replay |
|---|---|---|---|---|---|---|
| Claude Agent SDK | | | | | | |
| Claude Code subagent pattern | | | | | | |
| LangGraph | | | | | | |
| CrewAI | | | | | | |
| AutoGen | | | | | | |
| Raw tool-use (build it yourself) | | | | | | |

Legend: ✅ first-class · 🟡 possible with work · ❌ not supported / fights the framework

## Open questions to resolve during the survey

1. Does a 3-level hierarchy actually need framework support, or is "subagent as a tool" sufficient in every candidate?
2. What is the smallest useful delegation contract — is free-text enough at Milestone 3, deferring schemas to Milestone 4?
3. For the simulated product, what plays the role of the compiler? (Without a real build there is no ground truth, and Axis 5 collapses to LLM-judging-LLM.)
4. Is "raw tool-use, no framework" actually the right answer given the learning goal? A framework that hides the loop teaches less.

---
*Next step: research pass — current solutions scored against Axes 1–6.*
