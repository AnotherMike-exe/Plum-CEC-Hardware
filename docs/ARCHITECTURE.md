# Architecture — Plum-CEC-Hardware

How the adapter hardware is built and why. `README.md` says what it does. This file says
what somebody must understand before they change it.

## Shape

An ESP32-C3 board that connects to the CEC line of an HDMI cable. The PC connects to the
board over USB. Home Assistant connects over WiFi. Now it is a breadboard with an
ESP32-C3-DevKitC-02 and two breakouts. At milestone M8 it becomes an inline HDMI 2.1
passthrough PCB with one USB-C port (ADR 0003 and ADR 0004). The firmware is in
[Plum-CEC-Firmware](https://github.com/AnotherMike-exe/Plum-CEC-Firmware).

## Components

| Component | Does | Built with |
|---|---|---|
| HDMI passthrough breakout | Gives access to CEC (pin 13) and GND (pin 17) | Purchased breakout |
| ESP32-C3-DevKitC-02 | MCU: CEC bit-bang, USB-CDC, WiFi | Espressif devkit |
| USB-UART bridge (on devkit) | Power, flashing, UART0 console | Devkit micro-USB |
| USB breakout | Native USB-CDC to the PC on GPIO18/19 | Purchased breakout |
| PCB (M8, design set, not drawn) | HDMI 2.1 passthrough, one USB-C port, isolated taps on CEC, SCL and SDA | KiCad 10 |

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
| Devkit micro-USB (bench, from the Mac) | The whole board | The board stops. An unpowered devkit can load the CEC line. |
| PCB USB-C port (charger or Host port) | The whole board | The board is a passive HDMI cable. The isolated taps do not load the lines (ADR 0004). |

## External dependencies

| Depends on | For | When it is down |
|---|---|---|
| TV with CEC enabled | All CEC functions | No CEC traffic. The adapter stays idle. |
| USB-C power (charger or Host port) | Power, and the USB front-end on a Host port | The adapter goes off. HDMI still passes through. |

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
- **Physical**: the adapter does not tap the HDMI +5 V and HPD pins, so it cannot damage
  the HDMI source. On the PCB those pins pass straight through.

## Known limits

- On the bench, an unpowered ESP32 can pull the CEC line low through its protection
  diodes. The PCB uses one BSS138-type MOSFET for each tap (ADR 0004).
- USB power can stop in S3 and S5. Always-on network control needs a charger or a Host
  port that keeps 5 V in S3 (ADR 0003).
- The adapter shares the physical address of the source behind it. It cannot be a CEC
  switch, because it does not rewrite the EDID (ADR 0004).

## Decisions

### MCU

**Chose**: ESP32-C3
**Over**: RP2040 (pico-cec), ESP32-S3
**Because**: it has native USB and WiFi on one chip, and costs less. The S3 stays the
fallback.
**Revisit when**: the C3 cannot wake the Host over USB (remote wakeup, firmware plan
step 14). Gate A and Gate B passed.

### PCB shape

**Chose**: passive inline HDMI 2.1 board, one USB-C port, isolated taps (ADR 0003, 0004)
**Over**: a separate power input with OR-ing (ADR 0001), an HDMI repeater with EDID
rewrite
**Because**: one cable for the user, HDMI still passes without power, and no MCU in the
HDMI 2.1 link
**Revisit when**: the step 14 bench inputs fail

### CEC GPIO

**Chose**: GPIO10
**Over**: GPIO2, 8 and 9
**Because**: those are strapping pins, and GPIO18/19 are the USB pins.
**Revisit when**: the PCB layout needs a different pin.
