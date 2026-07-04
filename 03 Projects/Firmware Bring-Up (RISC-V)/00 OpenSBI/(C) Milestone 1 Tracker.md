---
milestone: 1
title: OpenSBI
status: active
started: 2026-07-04
---

# Milestone 1 Tracker — OpenSBI

> Source plan: [[06 Milestone Plan/(C) Milestone Plan.md]]
> This is a step-by-step accountability tracker, broken down from the M1 done-criteria. Update status inline as you go. Link your own consolidation notes under "Your Notes" per step — I won't write into those, that's your synthesis.

## Quick Glance

**Progress: 0/9 steps closed**

| Step | Status |
|---|---|
| 1 — Toolchain + QEMU setup | Not started |
| 2 — Ground the mental model | Not started |
| 3 — Clone OpenSBI, read before building | Not started |
| 4 — Build for the `generic` platform | Not started |
| 5 — Boot on QEMU virt, observe handoff | Not started |
| 6 — Prove S-mode handoff with payload | Not started |
| 7 — Study the SBI spec deliberately | Not started |
| 8 — Write it up | Not started |
| 9 — Self-certification gate | Not started |

> Status values: `Not started` · `In progress` · `Done` · `⚠ Flagged` (checked but low-confidence — revisit before Step 9)
> Update both this table and the step's own `**Status:**` line together so they don't drift out of sync.

**Verification process:** when a step is reported finished, it does not get marked `Done` on say-so alone. Claude quizzes against that step's Acceptance Criteria first — if the answers hold up, mark `Done`; if shaky, mark `⚠ Flagged` and revisit before moving on. This applies per step, not just at the Step 9 gate.

**Milestone 1 done when:**
- [ ] Can explain and demonstrate the boot chain from reset to S-mode handoff
- [ ] Has built OpenSBI from source and run it on QEMU virt machine
- [ ] Understands the SBI spec well enough to explain what it provides to the next layer up

---

## Step 1 — Toolchain + QEMU setup
**Status:** Not started

**Task:** Install `riscv64-unknown-elf-gcc` (or linux-gnu variant) and `qemu-system-riscv64`. Verify both run (`--version`).

**Why:** Everything downstream depends on this. Mechanical, not learning — get it out of the way fast.

**Acceptance Criteria:**
- [ ] `riscv64-unknown-elf-gcc --version` (or linux-gnu variant) runs without error
- [ ] `qemu-system-riscv64 --version` runs without error and reports a version supporting `-M virt`

**Your Notes:** _(link here once created)_

---

## Step 2 — Ground the mental model before touching code
**Status:** Not started

**Task:** Be able to answer, unaided: what are M/S/U privilege modes; why does execution start in M-mode at reset; what's the role of `mtvec`/`mepc`/`mstatus`; why must firmware run before Linux on real silicon.

**Why:** Skipping this and jumping to a build means booting something without understanding why it booted — the exact premature-milestone trap the project CLAUDE.md warns against. This is the conceptual floor of the whole stack.

**Acceptance Criteria:**
- [ ] Can state, unaided and out loud, what M/S/U privilege modes are for and why RISC-V has more than one
- [ ] Can explain why execution starts in M-mode at reset (not S or U)
- [ ] Can describe what `mtvec`, `mepc`, and `mstatus` each do, in your own words, without looking it up mid-explanation
- [ ] Can state why bare-metal firmware (not Linux) must run first on real silicon

**Your Notes:** _(link here once created)_

---

## Step 3 — Clone OpenSBI, read before building
**Status:** Not started

**Task:** Read `docs/platform/qemu-virt.md` and the top-level README. Understand `FW_JUMP` vs `FW_PAYLOAD` vs `FW_DYNAMIC`, and why QEMU `virt` typically uses `fw_jump`.

**Why:** This firmware-type choice *is* the M→S handoff you need to explain later. Picking blindly defeats the point of the milestone.

**Acceptance Criteria:**
- [ ] Can explain the difference between `FW_JUMP`, `FW_PAYLOAD`, and `FW_DYNAMIC` in your own words
- [ ] Can state which one QEMU `virt` conventionally uses, and why
- [ ] Have picked and can justify the firmware type you'll build in Step 4 (not just copied a command)

**Your Notes:** _(link here once created)_

---

## Step 4 — Build for the `generic` platform
**Status:** Not started

**Task:** `make PLATFORM=generic` with your toolchain. Produce `fw_jump.elf` (and optionally `fw_payload.elf` for Step 6).

**Why:** "generic" is a deliberate OpenSBI design choice — one binary, board specifics resolved at runtime via device tree. Understanding why previews the device-tree concept Milestone 2 leans on hard.

**Acceptance Criteria:**
- [ ] `make PLATFORM=generic` completes with no errors
- [ ] `build/platform/generic/firmware/fw_jump.elf` (and/or `fw_payload.elf`) exists on disk
- [ ] Can explain, in your own words, why `PLATFORM=generic` works across boards instead of a per-board target

**Your Notes:** _(link here once created)_

---

## Step 5 — Boot it on QEMU virt, observe the handoff
**Status:** Not started

**Task:** Run:
```
qemu-system-riscv64 -M virt -nographic -bios build/platform/generic/firmware/fw_jump.elf
```
Confirm you see the OpenSBI banner (version, platform, extensions).

**Why:** "has run it on QEMU" is a literal done-criterion. Seeing the banner is your first real checkpoint — a hang afterward (no next-stage image) is expected and fine.

**Acceptance Criteria:**
- [ ] QEMU launches without error using the built `fw_jump.elf`
- [ ] OpenSBI banner is visible in the terminal output (version, platform, list of enabled extensions)
- [ ] Can point at the banner output and explain what at least 2 of the listed fields mean

**Your Notes:** _(link here once created)_

---

## Step 6 — Prove the S-mode handoff with a minimal payload
**Status:** Not started

**Task:** Write or reuse a tiny bare-metal S-mode program that issues one SBI `ecall` (e.g. console putchar / DBCN extension) using the SBI calling convention (`a7` = extension ID, `a6` = function ID, args in `a0`/`a1`...). Build as `fw_payload` or chain-load via `-kernel`.

**Why — the step most people skip and shouldn't:** booting OpenSBI alone only proves M-mode runs. Making an actual SBI call from S-mode is what proves you understand what OpenSBI hands to the next layer — done-criterion #1 and #3 both hinge on this, not just #2.

**Acceptance Criteria:**
- [ ] Payload builds cleanly and is loaded via `fw_payload` or `-kernel`
- [ ] The `ecall` executes and its visible effect (e.g. printed character) shows up in QEMU output
- [ ] Can state, from memory, what registers (`a7`/`a6`/`a0`...) were set and what extension/function ID was invoked
- [ ] Can explain what actually happens at the hardware/trap level between the `ecall` instruction and OpenSBI's trap handler servicing it

**Your Notes:** _(link here once created)_

---

## Step 7 — Study the SBI spec deliberately
**Status:** Not started

**Task:** At minimum cover the Base extension (probe/version); skim HSM, TIME, IPI, RFENCE conceptually. You don't need to call all of them — you need to be able to say what each gives U-Boot/Linux later.

**Why:** This is exactly what Milestone 2 (U-Boot) will consume. If you can't articulate what SBI provides, you can't judge whether U-Boot's usage of it in M2 makes sense — you'd just be pattern-matching code.

**Acceptance Criteria:**
- [ ] Can explain the Base extension (probe/version) and why probing capability matters for forward compatibility
- [ ] Can state, in one sentence each, what HSM, TIME, IPI, and RFENCE extensions provide to a next-stage OS/bootloader
- [ ] Can name at least one thing U-Boot or Linux would be unable to do cleanly without SBI providing it

**Your Notes:** _(link here once created)_

---

## Step 8 — Write it up
**Status:** Not started

**Task:** Consolidate: reset → M-mode → SBI init → S-mode handoff sequence; your firmware-type choice and why; what your Step 6 SBI ecall actually did under the hood.

**Why:** "Can explain" is a stated done-criterion, not "can do." Writing it forces the explanation to actually hold together end to end.

**Acceptance Criteria:**
- [ ] A consolidated note exists covering reset → M-mode → SBI init → S-mode handoff as one continuous narrative (not disjointed bullet fragments from each step)
- [ ] The write-up can be read start to finish by future-you (or a colleague) without needing to re-open the OpenSBI source to follow it
- [ ] Your firmware-type choice (Step 3) and your Step 6 ecall are both referenced and explained within it, not left as separate orphaned facts

**Your Notes:** _(link here once created)_

---

## Step 9 — Self-certification gate (do not skip)
**Status:** Not started

**Task:** Before opening `01 U-Boot/`, re-read the three M1 done-criteria at the top of this file and check each one honestly against what you actually did — not what you attempted. If any box can't be checked in good faith, that's the next thing to close, not U-Boot.

**Why:** This is the guard against calling a milestone done because you did "the steps" rather than because the criteria were actually met. Explicitly required by the bottom-up sequencing rule in this project's CLAUDE.md — no layer starts on a shaky one below it.

**Acceptance Criteria:**
- [ ] Every checkbox under all 3 top-level M1 done-criteria (top of this file) is checked in good faith, not assumed
- [ ] Every Step 1–8 Acceptance Criteria block is fully checked — no partial/"close enough" steps carried forward
- [ ] A closing log entry is written below summarizing what was actually learned, not just "done"

---

## Log
_Dated entries as steps close or the plan gets adjusted._

- **2026-07-04** — Tracker created. Step 1 next.
