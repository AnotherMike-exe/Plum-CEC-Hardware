# CLAUDE.md — Plum-CEC-Hardware

Project memory for Claude Code. Keep it true: a line that is wrong is worse than a line
that is missing, because it is trusted.

## What this is

The hardware for a DIY HDMI-CEC adapter. An ESP32-C3 connects to the HDMI CEC line, and
appears to Linux as a Pulse-Eight USB-CEC dongle. It also takes commands over WiFi. The
goal is a gaming PC that wakes and sleeps the TV like a console. The firmware is in the
sibling repo [Plum-CEC-Firmware](https://github.com/AnotherMike-exe/Plum-CEC-Firmware).
The original outline is in `_resources/Notes/cec-adapter-overview.md`.

**Stage**: prototype (breadboard, milestone M0)
**Deployed on**: nothing yet

## Stack

| Part | Choice |
|---|---|
| EDA | KiCad 10 (`kicad-cli` 10.0.6 on the dev Mac) |
| MCU | ESP32-C3 (DevKitC-02 on the bench) |
| KiCad edits | Konnect MCP only |

## Layout

```
KiCad/          KiCad project (empty until M8)
Breadboard/     logic analyzer captures and bench photos
docs/           every document except README.md
_resources/     dev references and datasheets, never in git
```

## Commands

```bash
kicad-cli sch erc KiCad/*.kicad_sch     # ERC (once a schematic exists)
kicad-cli pcb drc KiCad/*.kicad_pcb     # DRC (once a PCB exists)
```

## Conventions

**Naming**: PascalCase for directories and document filenames. KiCad has no binding
convention, so KiCad project files are PascalCase too (for example `KiCad/PlumCec.kicad_pro`).
Net names use `UPPER_SNAKE_CASE` (for example `CEC`, `USB_DP`). Constants are
`UPPER_SNAKE_CASE`.

**Project-specific rules**:
- Change KiCad files through Konnect MCP only. Load the `konnect` and `kicad-workflow`
  skills before KiCad work.
- Do not connect HDMI pin 18 (+5 V) or pin 19 (HPD).
- Keep GPIO18/19 for USB. Do not use GPIO2, 8 or 9 for CEC.
- A pin change here also changes the firmware. Update both repos.

## How to work here

**Plan mode** for anything beyond a single-file edit: a schematic change, a layout
change, anything touching CI. Show the plan and wait for a yes.

**A subagent** for work that parallelizes: a datasheet read, a part search, an
independent design review. The main thread integrates the result.

**Ask early.** An architecture question answered before the work costs a minute. The
same question answered after costs the work.

Standing rules for this repo: rebase, never merge. `_resources/` never enters git. CI
green before merge.

## The phase loop

Each phase of the build runs the same five steps.

1. `/resume-session` — restore the state the last phase left
2. Build. Name `tdd-workflow` only for a bug fix, where the test is the reproducer
3. `verification-loop` — the gate at the end of the phase
4. Review in parallel: `code-reviewer`, `security-reviewer` and
   `kicad-design-review-agent`
5. `/learn-eval`, then `/save-session`

Commit at the end of every phase. A local commit costs nothing and gives the review a
diff to read.

`blueprint` holds the phase plan between sessions. Each step in it carries a brief that a
fresh session can execute cold.

## Gotchas

- No PCB work starts before M8. The bench results (M1 to M7) decide the PCB needs.

## Where the rules live

The Plum Solutions standards — naming, Binhex Docker layout, documentation rules, git
workflow — are in the `plum-standards` skill, not copied here. A copy in every repo is a
copy that goes stale.

This file holds only what is true of **this** project.
