# Plum-CEC-Hardware

> The hardware for a DIY HDMI-CEC adapter that a PC controls over USB and over WiFi.

GPUs have no CEC hardware, so a PC cannot turn a TV on or off, or select an input. This
repo holds the bench wiring and, later, the KiCad PCB for an ESP32-C3 adapter. The
adapter connects to the HDMI CEC line (pin 13). Linux sees it as a Pulse-Eight USB-CEC
dongle, and Home Assistant controls it over WiFi.

The firmware is in a separate repo: **[Plum-CEC-Firmware](https://github.com/AnotherMike-exe/Plum-CEC-Firmware)**.

---

## Usage

The project is at the breadboard stage. No PCB exists yet.

1. Get the parts in [docs/BenchWiring.md](docs/BenchWiring.md): an ESP32-C3-DevKitC-02,
   an HDMI passthrough breakout and a USB breakout.
2. Measure the HDMI breakout. Make sure that pins 13 (CEC) and 17 (ground) pass through.
3. Connect HDMI pin 13 to GPIO10, and HDMI pin 17 to GND.
4. Flash the firmware from
   [Plum-CEC-Firmware](https://github.com/AnotherMike-exe/Plum-CEC-Firmware).

Working when: the TV turns on and off from a CEC command, and its frames show in the
firmware log.

Full setup is in [docs/DEV-SETUP.md](docs/DEV-SETUP.md).

## Details

Pin assignments and the parts list are in
[docs/QUICK-REFERENCE.md](docs/QUICK-REFERENCE.md).

Known limitations:

- Do not tap HDMI pin 18 (+5 V) or pin 19 (HPD). On the bench, leave them unconnected.
  Do not power the board from HDMI.
- An unpowered board can pull the CEC line low. Keep the board powered while it is
  connected to the TV.
- USB power can stop while the PC sleeps. Network control during PC sleep needs a
  separate 5 V supply.

- [Architecture](docs/ARCHITECTURE.md): the design and the decisions behind it
- [Dev setup](docs/DEV-SETUP.md): from a clone to a working bench setup
- [Quick reference](docs/QUICK-REFERENCE.md): pins, parts and commands

## Attributions

Designed in [KiCad](https://www.kicad.org/). The adapter emulates the
[Pulse-Eight](https://www.pulse-eight.com/) USB-CEC protocol that the Linux
`pulse8-cec` driver supports.

Prior art: [esphome-native-hdmi-cec](https://github.com/Palakis/esphome-native-hdmi-cec)
and [pico-cec](https://github.com/gkoh/pico-cec).

Licensed under [MIT](LICENSE).

---

**Repository**: https://github.com/AnotherMike-exe/Plum-CEC-Hardware · **Firmware**: https://github.com/AnotherMike-exe/Plum-CEC-Firmware · **Maintainer**: Plum Solutions
