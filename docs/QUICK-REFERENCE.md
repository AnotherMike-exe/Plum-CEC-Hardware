# Quick Reference: Plum-CEC-Hardware

> The commands and pins you actually use on this project.

---

## Daily commands

```bash
# ERC and DRC (after M8 starts)
kicad-cli sch erc KiCad/*.kicad_sch
kicad-cli pcb drc KiCad/*.kicad_pcb

# Fab outputs (attach to a GitHub release, not to git)
kicad-cli pcb export gerbers -o KiCad/Fabrication/ KiCad/*.kicad_pcb
```

## Pins

| Function | ESP32-C3 pin | HDMI pin |
|---|---|---|
| CEC data | GPIO10 | 13 |
| Ground | GND | 17 |
| Native USB D− | GPIO18 | — |
| Native USB D+ | GPIO19 | — |
| Status LED | GPIO8 | — |
| BOOT / recovery | GPIO9 | — |
| **Do not connect** | — | 18 (+5 V), 19 (HPD) |

Full pin notes: [BenchWiring.md](BenchWiring.md)

## CEC timing

| Item | Value |
|---|---|
| Bit period (nominal) | about 2.4 ms |
| Logic level | 3.3 V, open-drain |

## Troubleshooting

### Native USB device does not show
→ Hold GPIO9 low and push reset (download mode).
→ Make sure that the firmware does not use GPIO18 or GPIO19.

### TV ignores CEC
→ Make sure that CEC is enabled in the TV settings.
→ Measure pin 13 continuity through the breakout.

## Project links

- [Architecture](ARCHITECTURE.md)
- [Dev setup](DEV-SETUP.md)
- [Firmware repo](https://github.com/AnotherMike-exe/Plum-CEC-Firmware)
- [Issues](https://github.com/AnotherMike-exe/Plum-CEC-Hardware/issues)

---

**Scope note for whoever edits this file:** naming rules, Binhex conventions,
documentation layout, and the git workflow are Plum-wide standards and live in the
`plum-standards` skill. Do not restate them here — a copy in every repo is a copy
that goes stale.
