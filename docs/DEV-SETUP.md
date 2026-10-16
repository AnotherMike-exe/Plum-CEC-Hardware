# Dev Setup — Plum-CEC-Hardware

From a fresh clone to a verified bench setup. If a step here fails, the project is
wrong, not the reader.

Target: 30 minutes, with the parts from [BenchWiring.md](BenchWiring.md) on the bench.

## Prerequisites

| Tool | Version | Check |
|---|---|---|
| KiCad | 10 or newer | `kicad-cli --version` |
| Multimeter | any | continuity mode |
| Logic analyzer | optional | PulseView or the vendor tool |
| git | any | `git --version` |

## 1. Clone

```bash
git clone https://github.com/AnotherMike-exe/Plum-CEC-Hardware.git
cd Plum-CEC-Hardware
git config pull.rebase true
mkdir -p _resources/{Examples,Research,Assets,Notes}
```

`pull.rebase` keeps history linear. `_resources/` is in `.gitignore`.

## 2. Verify the HDMI breakout

1. Set the multimeter to continuity mode.
2. Measure pin 13 between the male and the female side. It must pass through.
3. Measure pin 17 the same way.

## 3. Wire the bench

Follow [BenchWiring.md](BenchWiring.md). Connect only CEC (pin 13) and GND (pin 17) to
the devkit.

## 4. Flash the firmware

Follow the dev setup in
[Plum-CEC-Firmware](https://github.com/AnotherMike-exe/Plum-CEC-Firmware).

Working when: the TV powers on and off from a CEC command, and the firmware log shows
the TV frames.

## 5. Check the design files

```bash
kicad-cli sch erc KiCad/*.kicad_sch
kicad-cli pcb drc KiCad/*.kicad_pcb
```

These run only after the PCB phase (M8) starts. CI runs the same commands.

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|

Add a row the first time somebody hits a problem. A troubleshooting table written in
advance guesses. One written from real failures does not.

## Where the rest is

| Question | Document |
|---|---|
| How it is built and why | `docs/ARCHITECTURE.md` |
| How Claude should work here | `docs/CLAUDE.md` |
| Pins, parts and commands | `docs/QUICK-REFERENCE.md` |
| Wiring | `docs/BenchWiring.md` |
| What it does, for a user | `README.md` |
