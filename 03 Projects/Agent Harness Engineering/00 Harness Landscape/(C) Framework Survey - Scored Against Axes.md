---
created: 2026-07-26
phase: 1
milestone: 1
type: survey
tags: [harness, agents, orchestration, phase-1, comparison]
rubric: "[[(C) Harness Anatomy - Survey Rubric]]"
---

# Framework Survey — Scored Against the Six Axes

> Scored against [[(C) Harness Anatomy - Survey Rubric]]. Research pass 2026-07-26.
> Legend: ✅ first-class · 🟡 possible with work · ❌ not supported / fights the framework

## Scoring table

| Framework | A1 Hierarchy | A2 Contract | A3 Context | A4 Permissions | A5 Verification | A6 Evals/Replay |
|---|---|---|---|---|---|---|
| **Claude Agent SDK** | ✅ (3+ levels, opt-in) | 🟡 free text only | ✅ | ✅ | 🟡 DIY via Bash | 🟡 traces, no eval harness |
| Claude Code subagents (files) | ✅ same engine | 🟡 free text only | ✅ | ✅ | 🟡 DIY | ❌ interactive only |
| **Managed Agents (CMA)** | ❌ **1 level, enforced** | ✅ typed events | ✅ | ✅ | ✅ **Outcomes + grader** | ✅ Console traces |
| **LangGraph** | ✅ subgraphs, any depth | ✅ **typed state** | 🟡 opt-out default | ❌ build it | 🟡 you wire nodes | ✅ **checkpoint replay** |
| CrewAI | 🟡 manager auto-gen | ❌ role prose | 🟡 | ❌ | ❌ | 🟡 |
| AutoGen | 🟡 nested chat | ❌ conversation | 🟡 | ❌ | ❌ | 🟡 |
| Raw tool-use | ✅ you decide | ✅ you decide | ✅ you decide | ✅ you decide | ✅ you decide | ✅ you decide |

## The headline findings

### 1. Managed Agents enforces **one level** of delegation

`multiagent: {type: "coordinator", agents: [...]}` gives a coordinator a roster of up to 20 agents, each running in its own context-isolated thread. But **rostering an agent that itself carries a roster fails the create/update with a validation error** — this is enforced, not silently flattened.

**This kills the 3-level org chart on CMA.** Product Leader → Component Lead → Implementer/Reviewer is depth 2 below root. CMA gives you depth 1.

The workaround exists but changes the design: flatten to Product Leader → Component Leads, and have each Lead run its own internal implement→review loop rather than delegating to separate agents. That's arguably closer to how a real tech lead works — but it means the Implementer and Reviewer stop being distinct agents with distinct permissions, which was the whole point of Axis 4.

### 2. Claude Agent SDK nesting is opt-in, and the default moved twice

Current behavior (Claude Code v2.1.217+): **subagents cannot spawn subagents by default.** Set `CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH` to 2 or higher to enable it. From v2.1.172 through v2.1.216 nesting was on by default, capped at five layers.

So a 3-level hierarchy **is** supported — but it's a config flag, not the default, and the default flipped inside a five-version window. Pin the version in `07 System/` and note the env var, or Milestone 4 will break on an upgrade.

### 3. The delegation contract is the weakest point in every LLM-native harness

Claude Agent SDK, verbatim: *"The only content you pass from parent to subagent is the Agent tool's prompt string."* And upward: *"The parent receives the subagent's final message as the Agent tool result, but may summarize it in its own response."*

So the contract is **free text down, free text up, with lossy re-summarization at each hop.** Across three levels that's two summarization boundaries between the Product Leader and the code. There is no typed task object and no schema validation.

**LangGraph is the opposite** and this is its single biggest advantage: graph state is a typed object (TypedDict/Pydantic), so the handoff is schema-checked at every boundary. If Axis 2 is the real architecture — and it is — LangGraph is the only candidate that treats it as one.

Mitigation on the Agent SDK: define the contract yourself as a JSON blob inside the prompt string and require the child to write its result to a known file path, so the parent reads a file instead of a summary. That's Axis 2 built by hand on top of a harness that doesn't provide it.

### 4. LangGraph's default handoff is the opposite of context isolation

`create_handoff_tool` **passes the full message history** to the receiving agent by default. That's the inverse of the subagent model — no isolation unless you deliberately override it.

Not a flaw, a different philosophy: LangGraph's unit is shared state, not isolated context. But it means "context isolation" on LangGraph is something you implement, not something you get.

### 5. Verification: only Managed Agents ships a real gate

**Outcomes** (`user.define_outcome`) is the only built-in Reviewer in the entire survey: you supply a gradeable rubric, a **separate grader with an independent context window** scores each iteration, and the harness runs an iterate → grade → revise loop until `satisfied`, `max_iterations_reached`, or `failed`. Grader progress arrives as `span.outcome_evaluation_*` events.

That is structurally exactly the Reviewer role — separate context, distinct from the Implementer, and an actual gate rather than advisory text.

Everywhere else, verification is: give the agent Bash, tell it to run the build, and hope. Which is fine — a failing compiler is still a harder gate than any LLM reviewer — but it's plumbing you write.

### 6. Replay: LangGraph wins, and it's the rule-critical one

The project rule *"real API only once the structure is validated"* needs a stubbed-replay path.

- **LangGraph**: checkpointer saves state at every superstep; **replay** re-runs from a checkpoint without re-executing prior nodes, **fork** branches with modified state. This directly satisfies the rule.
- **CMA**: full Console trace per session, per-thread event history, `span.model_request_end` token accounting per call — excellent observability, but that's inspection, not deterministic replay.
- **Agent SDK**: session resume and separately-persisted subagent transcripts (resumable by `agentId`), but no replay-with-stubs.

Caveat worth recording: LangGraph checkpoints are **not durable execution** — a run lives in one process, and if the process dies the run dies. Fine for this project; a real constraint at work.

### 7. AutoGen is in maintenance mode

Microsoft moved AutoGen to maintenance in favour of the Microsoft Agent Framework. **Do not build on it.** Survey value only: its nested-chat and conversation-as-primitive model is the clearest example of the design this project is *not* doing.

## Per-framework reasoning

### Claude Agent SDK — the Claude Code harness as a library
`AgentDefinition` carries per-role `tools`, `disallowedTools`, `model`, `effort`, `permissionMode`, `maxTurns`, `skills`, `mcpServers`, `memory`, `background`. That is per-role model routing, per-role budget caps, and per-role permission scoping as **declarative config** — the strongest Axis 4 story here, and the documented example is literally a read-only code reviewer (`tools: ["Read", "Grep", "Glob"]`).

Also relevant: a subagent with the `SendMessage` tool receives the list of other named agents in the session, so Lead ↔ Implementer ↔ Reviewer peer messaging is available without routing everything through the parent. And v2.1.210+ scans subagent output for instruction-shaped patterns before the parent reads it — prompt-injection hardening on the child→parent boundary, which matters once the Implementer's output flows upward.

**Weaknesses:** free-text contract (finding 3), no eval harness, verification is DIY, and the loop is hidden — which cuts against the learning goal.

### LangGraph — explicit control, nothing given for free
Typed state, arbitrary nesting via subgraphs, checkpoint/replay/fork, LangSmith traces and datasets for evals. Wins A2 and A6 outright.

Loses A4 badly: there is no permission system. Read-only-by-construction means you personally don't hand that node a write tool — enforced by your own wiring, with no harness backstop, no sandbox, no approval gate. For a firmware agent that will eventually touch a real repo, that's the thing you'd have to build before trusting it.

### CrewAI — fits the org chart on paper, fails on substance
`Process.hierarchical` auto-generates a manager agent that delegates and reviews outputs. Superficially it *is* the org chart. But the abstraction is role-play — agents are prompt personas, not permission boundaries. No tool scoping worth the name, no verification gates, no replay. Faster and cheaper on structured pipelines, which is real, but that's not what's being optimized here.

The trap: it will produce a convincing demo of the org chart fastest, and teach the least about why it works.

### Raw tool-use — right for a warm-up, wrong for the project
Everything is ✅ because everything is yours. The `while stop_reason == "tool_use"` loop is ~100 lines. Building it once is the single highest-information exercise in this whole survey. Building the entire project on it is re-implementing four frameworks badly.

## Recommendation

**Build on the Claude Agent SDK. Precede it with a raw-loop exercise. Read LangGraph as the deliberate contrast.**

The why, in order:

1. **It's the only candidate that supports the actual org chart with real permission boundaries.** 3 levels (opt-in), per-role tool grants, per-role models. CMA can't do depth. LangGraph can do depth but not permissions.
2. **Axis 4 is where the Reviewer becomes real, and this is the only harness that makes it declarative.** `tools: ["Read", "Grep", "Glob"]` is a stronger statement than any reviewer prompt.
3. **It's the pattern actually deployed at scale.** The end target is applying this at the day job to firmware work — the Claude Code harness is the one with production mileage on exactly that workload.
4. **The loop being hidden is a real cost** — hence the raw-loop warm-up first, so the SDK is a convenience rather than a black box.

**What has to be built by hand on top of it:**

- **The delegation contract** (Axis 2). Define a JSON task schema, embed it in the prompt string, require children to write results to a known path. Do not rely on the summarized tool result.
- **The verification gate** (Axis 5). A Bash-run build/test is the gate; the LLM Reviewer is advisory on top of it. Decide this before Milestone 4 or the Reviewer becomes theatre.
- **The stub/replay path** (Axis 6). Roll your own — record `AgentDefinition` + prompt → canned response, replay from disk. Non-negotiable given the project's own API-budget rule.

**Milestone 1 is answerable now.** Open question 4 from the rubric ("is raw tool-use actually right given the learning goal?") resolves to: *yes, as a bounded warm-up; no, as the project foundation.*

## Suggested plan change

Insert a **Milestone 0 — Raw Loop** before Milestone 2: ~100 lines of `while stop_reason == "tool_use"` with two hand-written tools, no framework. Done when it can complete a two-tool task and you can explain every line. Cheap, fast, and it converts the Agent SDK from magic into a labour-saving device.

---

## Sources

- [Subagents in the SDK — Claude Agent SDK docs](https://code.claude.com/docs/en/agent-sdk/subagents)
- [Claude Code nested subagents: 5 levels deep](https://claudefa.st/blog/guide/agents/nested-subagents)
- [Claude Agent SDK: production guide to tracing, subagents, and evaluation](https://inference.net/content/claude-agent-sdk-production-guide/)
- [Claude Agent SDK hooks and permissions](https://medium.com/@richardhightower/claude-agent-sdk-hooks-permissions-ask-the-model-for-permission-hooks-do-not-ask-anyone-f0baf89dd0b9)
- [LangGraph Multi-Agent Supervisor reference](https://reference.langchain.com/python/langgraph-supervisor)
- [langgraph-supervisor-py](https://github.com/langchain-ai/langgraph-supervisor-py)
- [Use time-travel — LangChain docs](https://docs.langchain.com/oss/python/langgraph/use-time-travel)
- [Why checkpoints aren't durable execution](https://www.diagrid.io/blog/checkpoints-are-not-durable-execution-why-langgraph-crewai-google-adk-and-others-fall-short-for-production-agent-workflows)
- [AutoGen vs CrewAI 2026 comparison](https://arsum.com/blog/posts/autogen-vs-crewai/)
- [Best multi-agent frameworks 2026](https://gurusup.com/blog/best-multi-agent-frameworks-2026)
- Managed Agents multiagent/outcomes/permission-policy details: bundled `claude-api` skill reference (`shared/managed-agents-*.md`)
