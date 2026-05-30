# EMG Biosignal System

A multi-channel surface-EMG gesture recognition system, built end to end:

**Custom analog front-end → STM32 acquisition → FPGA digital signal processing (FFT) → machine-learning classification**, with on-FPGA inference as the headline goal.

Two-person project. Summer timeline: **June 11 – Sept 24**, with a 3-week in-person window (**July 10 – Aug 1**) used for the hard physical integration work.

---

## What this repo is

This is the single source of truth for the project. Code, designs, docs, and decisions all live here. It's also what lets us work apart — the simulation-first design and written interface contracts mean each track can progress independently and combine cleanly.

## Repo structure

| Folder | Contents | Owner |
|---|---|---|
| `docs/` | Roadmap, **interface contracts**, decisions log, datasheets | Shared |
| `firmware/` | STM32 bare-metal C (acquisition, DMA, UART) | Hardware |
| `fpga/` | SystemVerilog + testbenches (UART RX, FFT, classifier) | Hardware |
| `hardware/` | KiCad schematic, PCB layout, BOM (custom AFE) | Hardware |
| `ml/` | Training, models, feature pipeline | Software |
| `tools/` | Live visualizer, data-capture tool, STM32 emulator | Software |
| `data/` | Captured EMG (small) + scripts to fetch Ninapro (NOT committed) | Shared |
| `integration/` | Glue scripts/configs that wire the tracks together | Shared |

## Ownership split

- **Hardware track:** `firmware/`, `fpga/`, `hardware/`
- **Software track:** `ml/`, most of `tools/`
- **Shared:** `docs/`, `integration/`, `data/`

## Start here (Phase 0)

1. Read `docs/ROADMAP.md` — the phased plan and scope tiers.
2. Read `docs/INTERFACE_CONTRACTS.md` — **fill these in before writing dependent code.**
3. Log every significant decision in `docs/DECISIONS.md`.
4. Both partners: do HDLBits for a weekend (the FPGA go/no-go test).
5. Order parts (see roadmap Phase 0 checklist) — including the oscilloscope.

## The two convergence points (where the tracks must sync)

1. **UART bridge** — `firmware/` ↔ `fpga/`, governed by the UART interface contract.
2. **Weight handoff** — `ml/` → `fpga/` or `firmware/`, governed by the fixed-point format contract.

Both contracts live in `docs/INTERFACE_CONTRACTS.md`. Write them first.

## Workflow

- Branch for non-trivial work; merge via pull requests so each partner sees the other's changes.
- Commit often with clear messages.
- Tag milestones (`git tag`) when a module is complete and working — these are the fallback points.
- Testbenches live alongside their FPGA modules (`fpga/<module>/tb/`). A testbench *is* an async contract.

## Scope tiers (the guardrail against scope creep)

- **CORE** — the must-hit spine. A complete, defensible project.
- **REACH** — the strong version.
- **FULL** — the headline ambition (on-FPGA ML classifier, multi-channel, polished demo).

See `docs/ROADMAP.md` for what's in each tier. **Finished beats ambitious — always stand on finished ground.**
