# Milestone Plan — Firmware Bring-Up (RISC-V)

> Living document. No dates, no weekly cadence — progress is milestone-gated because training time is genuinely irregular. A skipped weekend is not a failure.
> Source reasoning lives in the top-level `GOALS.md` — this is the working copy for day-to-day tracking.

## Baseline (as of 2026-07-04)
- **C:** Professional level from career experience. Room to improve, but not a learning priority here.
- **Rust:** Some past exposure/projects, currently rusty. Not the main goal right now — system knowledge takes priority since Rust isn't yet widely adopted in this space.
- **Linux kernel:** Has built a kernel before, touched kconfig and device trees, written simple drivers — but self-assessed as "pretty new," no deep mental model of the full boot chain yet.
- **Environment:** Starting on QEMU (widely used, good learning tool in its own right). Real RISC-V board purchase is not a cost concern — timing is the only question.
- **Context:** Self-directed. Not required by the day job, but currently working with RISC-V professionally, so this is free leverage. Job's main goal is still just delivering what's asked — this project is separate, personal depth-building.
- **Time:** Weekends, inconsistent commitment. Explicitly project/milestone-oriented rather than calendar-oriented to avoid the plan breaking on the first skipped week.

## Why Bottom-Up, This Order
The real RISC-V boot chain, bottom to top: **Reset → OpenSBI (M-mode) → U-Boot (S-mode) → Linux kernel → userspace.** Sequencing follows this exactly:

1. **OpenSBI first** — closest to silicon, RISC-V-specific, teaches privilege levels (M/S/U) and the SBI spec. This is the actual bottom, not Linux.
2. **U-Boot second** — next layer up, chain-loaded by OpenSBI. Teaches device trees and boot-time kconfig in a full-chain context, not isolation.
3. **Linux kernel third** — deepens existing shallow exposure, but now inside a stack that's entirely self-built (own OpenSBI, own U-Boot).
4. **Userspace/rootfs fourth** — closes the loop: a fully custom system, every layer self-built, booting end-to-end in QEMU.
5. **Coreboot + EDK2 last** — deliberately not RISC-V-native, so they're best understood as alternate firmware philosophies once a real reference stack (steps 1-4) exists to compare against. This is also where the "glue that ties the ecosystem together" becomes visible — why UEFI/EDK2 and Coreboot solve boot differently than the SBI model.

**Rust** runs as a parallel, non-gating side-track (`05 Rust Side-Track/`) — small no_std bare-metal RISC-V exercises, picked up on low-energy weekends or when the main track stalls. It does not block any milestone below.

## Milestones

### Milestone 1 — OpenSBI (ACTIVE)
**Done when:** Can explain and demonstrate the boot chain from reset to S-mode handoff; has built OpenSBI from source and run it on QEMU virt machine; understands the SBI spec well enough to explain what it provides to the next layer up.

### Milestone 2 — U-Boot (NOT STARTED)
**Done when:** Custom U-Boot build boots on QEMU chain-loaded by the Milestone 1 OpenSBI build; can explain device tree usage and kconfig choices made; U-Boot successfully loads a kernel from a custom location.

### Milestone 3 — Linux Kernel (NOT STARTED)
**Done when:** Custom kernel config builds and boots on QEMU, loaded by the Milestone 2 U-Boot build (full self-built chain: OpenSBI → U-Boot → kernel); has written or meaningfully improved a driver tied into the device tree, not just used a stock config.

**Board purchase trigger:** once this milestone (or Milestone 4) is done — real hardware becomes useful once there's a working reference to compare it against, not before.

### Milestone 4 — Userspace / Rootfs (NOT STARTED)
**Done when:** A minimal rootfs (buildroot/busybox or similar) is integrated into the full boot flow — a complete, self-built RISC-V Linux system booting end-to-end in QEMU, every layer authored/configured by hand.

### Milestone 5 — Coreboot & EDK2 (NOT STARTED)
**Done when:** Has explored both as alternate firmware architectures, and can articulate concretely how each differs from the OpenSBI/U-Boot model built in Milestones 1-4 — the actual "glue across the ecosystem" understanding that was the original stated goal.

## Rust Side-Track (parallel, non-gating)
No done-criteria that block anything — just a running list of exercises to rebuild fluency (e.g. a toy SBI client in Rust, a minimal no_std program on RISC-V). Revisit if it's stalled for a long time and system-knowledge milestones are moving fine — that's a sign it's safe to invest more here.

## Log
_Add dated entries here as milestones progress or the plan gets adjusted._

- **2026-07-04** — Project created. Milestone 1 (OpenSBI) active.
