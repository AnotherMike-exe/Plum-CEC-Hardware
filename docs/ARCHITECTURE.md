# Architecture — Plum-CEC-Hardware

How the adapter hardware is built and why. `README.md` says what it does. This file says
what somebody must understand before they change it.

## Shape

An ESP32-C3 board that connects to the CEC line of an HDMI cable. The PC connects to the
board over USB. Home Assistant connects over WiFi. Now it is a breadboard with an
ESP32-C3-DevKitC-02 and two breakouts. At milestone M8 it can become a KiCad PCB with
an inline HDMI connector. The firmware is in
[Plum-CEC-Firmware](https://github.com/AnotherMike-exe/Plum-CEC-Firmware).

## Components

| Component | Does | Built with |
|---|---|---|
| HDMI passthrough breakout | Gives access to CEC (pin 13) and GND (pin 17) | Purchased breakout |
| ESP32-C3-DevKitC-02 | MCU: CEC bit-bang, USB-CDC, WiFi | Espressif devkit |
| USB-UART bridge (on devkit) | Power, flashing, UART0 console | Devkit micro-USB |
| USB breakout | Native USB-CDC to the PC on GPIO18/19 | Purchased breakout |
| PCB (M8, not started) | HDMI inline, power OR-ing, CEC line protection | KiCad 10 |

## Signal flow

```
PC (pulse8-cec driver) ──USB-CDC── GPIO18/19 ─┐
                                              ├─ ESP32-C3 ── GPIO10 ── HDMI pin 13 ── TV
Home Assistant ─────────WiFi──────────────────┘
                     micro-USB ── bridge ── UART0 console (logs only)
```

## Power

| Source | Feeds | When it is down |
|---|---|---|
| Devkit micro-USB (PC) | The whole board | The board stops. An unpowered board can load the CEC line. |
| Separate 5 V supply (PCB) | Network control during PC sleep | Not designed yet. Needs diode OR-ing with USB VBUS. |

## External dependencies

| Depends on | For | When it is down |
|---|---|---|
| TV with CEC enabled | All CEC functions | No CEC traffic. The adapter stays idle. |
| PC USB port | Power and the USB control path | The adapter goes off, unless a separate supply is fitted. |

## Deployment

The bench setup sits next to the PC. Fab outputs go on a GitHub release, not in git.

## CI/CD and secrets

| Workflow | Triggers on | Does |
|---|---|---|
| `Review.yml` | pull request, push to `main` | KiCad ERC and DRC (skips while `KiCad/` has no project), automated review |

| Secret | Used by | Still to create |
|---|---|---|
| `ANTHROPIC_API_KEY` | `Review.yml` | yes |

## Security model

- **Trust boundary**: the CEC bus is unauthenticated. Every device on it can send any
  frame. The USB path trusts the local PC.
- **Authentication**: none at the hardware level. Network authentication is a firmware
  concern.
- **Physical**: the HDMI +5 V and HPD pins stay disconnected, so the adapter cannot
  damage the HDMI source.

## Known limits

- An unpowered ESP32 can pull the CEC line low through its protection diodes. Fix on
  the PCB: a series diode or a buffer.
- USB power can stop in S3 and S5. Always-on network control needs a separate supply.

## Decisions

### MCU

**Chose**: ESP32-C3
**Over**: RP2040 (pico-cec), ESP32-S3
**Because**: it has native USB and WiFi on one chip, and costs less. The S3 stays the
fallback.
**Revisit when**: the native USB go/no-go test (M3, tests T1 to T6) fails.

### CEC GPIO

**Chose**: GPIO10
**Over**: GPIO2, 8 and 9
**Because**: those are strapping pins, and GPIO18/19 are the USB pins.
**Revisit when**: the PCB layout needs a different pin.
