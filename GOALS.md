# Calisthenics Goals
I want to follow a skill based approach. Meaning I want to learn different skills and train for it while I gain strength and skills at the same time. I have several skills that I aim for but I'll select three to focus on:
* Handstand / Handstand presses
* L-Sit
* Muscle Up

## Current Level (as of 2026-07-04)
* **Handstand:** Comfortable with wall drills (chest-to-wall and back-to-wall). Attempting freestanding without fear, but inconsistent. Longest freestanding hold: 9s on parallettes.
* **Muscle Up:** Can do one kipping muscle up. Can do several band-assisted reps. Not acquiring the skill — fixing timing/technique to string together more than one rep.
* **L-Sit:** Can hold a tuck L-sit for 15-20s. Core endurance is decent; the real limiter is hamstring/hip flexibility, which is currently poor. Goal: work toward a full L-sit on parallettes.

**Constraints to train around:** hamstring/hip flexibility is poor (directly limits L-Sit). Wrists occasionally hurt but manageable with parallettes. Shoulder strength is a general weak point, already being addressed.

**Training capacity:** 3-4x/week at a calisthenics gym, 1hr sessions programmed around Back Lever, Front Lever, Handstand, and Planche. Willing to add a daily 10-15 min at-home session (no/minimal equipment) for supplemental work.

## Priority & Reasoning
Torn between L-Sit (head says foundation) and Handstand (heart says it's the one I really want). Decided to prioritize **L-Sit first**, because:
- My actual goal includes "Handstand *presses*," not just the static hold. The press is a compression/straight-arm strength movement — the exact strength built by progressing L-Sit → straddle L-sit → full L-sit. Prioritizing L-Sit is the most direct path to the handstand press, not a detour from it.
- The named limiter on L-Sit (hamstring/hip flexibility) is a mobility problem I haven't actually trained yet — it's not just "do more reps," so it needs deliberate, separate work.
- Muscle Up isn't a new-skill acquisition, it's a technique fix — low volume, drill-focused work fits around anything else without competing for primary focus.

## Plan

**Phase 1 (~8-10 weeks) — Primary focus: L-Sit**
- Strength: tuck → single-leg → straddle progression on parallettes, plus compression/hollow body work
- Mobility: daily 10-15 min hamstring/hip flexor mobility routine (non-negotiable — this is the actual bottleneck)
- Track weekly whether tuck hold time and straddle attempts are progressing

**Maintenance track — Muscle Up**
- 1-2x/week, low volume, drill-focused: false grip holds, transition-specific drills
- Goal is fixing timing, not building new strength — don't let this creep into a full training block

**Passive/ongoing — Handstand**
- Keep leveraging existing gym programming (already covers this)
- Add short, frequent freestanding attempts on non-gym days (~5 min, little and often) — balance is a neurological skill, frequency beats grinding for a new max hold
- Becomes primary focus in Phase 2

**Phase 2 (trigger: tuck → straddle L-sit achieved, or ~8-10 weeks elapsed)**
- Shift primary focus to freestanding Handstand consistency
- Begin tuck handstand press negatives, leveraging compression strength built in Phase 1

## Open Questions / Revisit Points
- Confirm after ~4 weeks whether the 8-10 week timeline for Phase 1 still feels realistic, given the tendency to plan unrealistically under enthusiasm — adjust rather than force it.
- Consider setting up a training log (e.g. in `04 Reviews/`) to track sessions objectively instead of going on vibes.

# Firmware Engineer Goals
My long term goal is learning more and more about the linux ecosystem and various opensource projects that are related to firmware. Also I'm focused on learning for a particular architecture which is RISC-V even though I'm interested in general knowledge as well. As a firmware engineer I don't have a particular love for the hardware but I'm aware I need to know some bits of it and I'm prepared to do so. Several projects that I want familiarity with are:
* Coreboot
* U-boot
* EDK2
* OpenSBI (RISC-V specific)
* Linux (Both Kernel and User Space)
Notice that I don't want only to be familiarized with the projects, I want to understand the glue that ties them together for the full ecosystem to work.

I love programming so I want to be proficient in two languages which are:
* C
* Rust
Also interested in python as well but is more of a side quest.

## Current Level (as of 2026-07-04)
* **C:** Professional level from career experience. Room to improve, but not a learning priority right now.
* **Rust:** Some past exposure/projects, currently rusty. Deprioritized for now — system knowledge comes first, since Rust isn't yet widely adopted in this space.
* **Linux kernel:** Built a kernel before, touched kconfig and device trees, written simple drivers — but self-assessed as "pretty new," no deep mental model of the full boot chain yet.
* **Environment:** Starting on QEMU. Real RISC-V board purchase is not a cost concern, just a timing question — triggered once the QEMU chain is self-built end to end.
* **Context:** Self-directed, not required by the day job — but currently working with RISC-V professionally, so this is free leverage. Job's main goal stays "deliver what's asked"; this is separate, personal depth-building.
* **Time:** Weekends, genuinely inconsistent commitment.

## Priority & Reasoning
Decided on a **bottom-up sequence, milestone-gated rather than calendar-gated**, because:
- The real RISC-V boot chain, bottom to top, is Reset → OpenSBI (M-mode) → U-Boot (S-mode) → Linux kernel → userspace. Bottom-up means starting at OpenSBI — closest to silicon, RISC-V-specific — not at Linux, which is what "bottom-up" superficially might suggest given how much more familiar Linux already is.
- Coreboot and EDK2 aren't RISC-V-native, so they're sequenced last — best understood as alternate firmware philosophies once a working reference stack exists to compare them against. That comparison is also where "the glue that ties the ecosystem together" actually becomes visible.
- Milestone-gating (not weekly cadence) directly matches the stated constraint: weekend time is genuinely irregular, and a plan hung on weeks would break on the first skipped weekend.
- Rust runs as a parallel, non-gating side-track — it doesn't block any system-knowledge milestone, since fluency there isn't the current priority.

## Plan

**Milestone 1 — OpenSBI:** build from source, run on QEMU virt machine, understand SBI spec and M/S/U privilege levels well enough to explain the reset-to-S-mode handoff.

**Milestone 2 — U-Boot:** custom build, chain-loaded by the Milestone 1 OpenSBI build, loading a kernel from a custom location. Deepens device tree / kconfig exposure in a full-chain context.

**Milestone 3 — Linux Kernel:** custom kernel config booting on QEMU via the self-built OpenSBI → U-Boot chain; write or meaningfully improve a driver tied to the device tree, not just use a stock config.
*(Board purchase triggers around here or Milestone 4 — real hardware becomes useful once there's a working reference stack to compare it against.)*

**Milestone 4 — Userspace/Rootfs:** minimal rootfs integrated into the full boot flow — a complete, self-built RISC-V Linux system booting end-to-end in QEMU.

**Milestone 5 — Coreboot & EDK2:** explore both, articulate concretely how each differs from the OpenSBI/U-Boot model built in Milestones 1-4.

**Rust side-track (parallel, non-gating):** small no_std bare-metal RISC-V exercises, picked up whenever there's low-energy time or the main track stalls.

## Open Questions / Revisit Points
- No calendar checkpoints by design — revisit when a milestone is claimed "done" to sanity-check it wasn't called done prematurely (e.g. "it booted once" vs. actually understanding why).
- If the Rust side-track stalls for a long time while system-knowledge milestones move fine, that's a signal it's safe to invest more there.