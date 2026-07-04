# Firmware Bring-Up (RISC-V)

A bottom-up, self-directed firmware engineering project to build real understanding of the RISC-V boot/firmware ecosystem — OpenSBI, U-Boot, Linux (kernel + userspace), and eventually Coreboot/EDK2 — plus the glue that ties them together. Starts on QEMU; a real RISC-V board is a milestone reward once the QEMU chain is fully self-built. Solo project, no external deadline — driven by milestones, not weeks, because training time is genuinely irregular (weekends, when available).

## Claude's Role

Claude's job here is to keep the bottom-up sequencing honest and to help interpret what's actually being learned at each layer — not to write the firmware code, but to help debug conceptually, explain how a layer fits into the whole stack, and flag when a "milestone" is being called done prematurely (e.g. booting something without understanding why it booted).

If a session is drifting without moving toward the current milestone, nudge me back: "Does this actually deepen understanding of [current milestone], or is it a detour from the bottom-up chain we agreed on?"

## Process

1. **Work milestone by milestone** — no calendar cadence. Progress happens whenever weekend time is available; a skipped week is not a failure, because this project is explicitly not time-gated.
2. **Sequence is bottom-up, matching the real boot chain**: OpenSBI (M-mode, closest to silicon) → U-Boot (S-mode bootloader) → Linux kernel → userspace/rootfs → Coreboot & EDK2 (explored last, as alternate firmware philosophies once a reference stack exists to compare against).
3. **Each milestone folder** holds notes, build logs, and takeaways for that layer. Move to the next milestone only when the current one's "done" criteria (defined in the Milestone Plan) are actually met — not just "it booted once."
4. **Rust side-track runs in parallel**, whenever there's low-energy time or the main track stalls — it is explicitly not gating any main-track milestone, since system knowledge is the priority right now, not Rust fluency.
5. **Board purchase** is triggered after the Linux kernel or userspace milestone (own kernel booting clean on QEMU) — real hardware teaches things emulation can't, but only once there's a working reference to compare it against.

## Folder Structure

```
Firmware Bring-Up (RISC-V)/
├── CLAUDE.md                    ← You are here
├── COMMANDS.md                  ← Skills/commands reference
├── 00 OpenSBI/                  ← Milestone 1: M-mode firmware, SBI spec, privilege levels
├── 01 U-Boot/                   ← Milestone 2: S-mode bootloader, device trees, kconfig
├── 02 Linux Kernel/             ← Milestone 3: full-chain kernel bring-up (deepens prior shallow exposure)
├── 03 Userspace/                ← Milestone 4: minimal rootfs, end-to-end custom boot
├── 04 Coreboot and EDK2/        ← Milestone 5: alternate firmware philosophies, cross-ecosystem comparison
├── 05 Rust Side-Track/          ← Parallel, non-gating: no_std bare-metal RISC-V Rust exercises
├── 06 Milestone Plan/           ← The living plan: sequencing, done-criteria, board-purchase trigger
├── 07 System/                   ← Scripts, config, reusable build setups (e.g. QEMU launch scripts)
├── 08 Skills/                   ← Skill markdown files (NOT Claude Code skills)
├── 09 Attachments/              ← Screenshots, boot logs, diagrams
└── 10 Iteration Logs/           ← Notes on what to improve about the process itself
```

## Rules & Conventions

- **`(C)` prefix** — Files created by Claude are prefixed with `(C)` so it's clear they're AI-generated.
- **Editing rule** — Before editing any file without the `(C)` prefix, ask for permission first.
- **Skills** — All reusable scripts/automations are saved as markdown files in `08 Skills/`, NOT as Claude Code skills.
- **No calendar pressure** — Never frame progress in terms of weeks elapsed. Progress is measured by milestone completion only.
- **Reasoning over verdicts** — When suggesting what to tackle next or how a layer fits together, always explain the why.

## Current Status

> **Last updated:** 2026-07-04
> **Status:** Just created. Active milestone: **Milestone 1 — OpenSBI on QEMU**. Rust side-track available whenever, non-gating.
