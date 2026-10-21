# Bench Wiring — Plum-CEC-Hardware

The breadboard setup for milestones M0 to M7. The PCB decision is M8.

## Parts

| Item | Notes |
|---|---|
| HDMI passthrough breakout `04-JVD0001-01 Rev. 1A` | Male plug (J1) to female socket (J2), inline. An 8-pin header gives the low-speed signals. See [HDMI breakout](#hdmi-breakout). |
| ESP32-C3-DevKitC-02 | The micro-USB port goes to a USB-UART bridge, not to the native USB of the C3. The Mac gives power and the UART0 console through it. |
| USB breakout (USB-A plug or USB-C pigtail, D+ and D− exposed) | For the native USB phase (Phase 3). |
| Jumper wires, breadboard | |
| USB cables | Micro-USB for the devkit. A second data-only USB path for native USB. |
| Logic analyzer | Not used. The GPIO4 timing probe gives the CEC timing (plan step 6). |

## Pin plan

| Function | Pin | Notes |
|---|---|---|
| CEC data | GPIO10 | Open-drain, direct connection. CEC is 3.3 V logic. Make sure GPIO10 is on the header. |
| CEC ground | GND to HDMI pin 17 | The breakout header pin `DDC_GND` |
| Timing probe | GPIO4 | A jumper to GPIO10. An RMT RX channel reads the CEC line (plan step 6). |
| Native USB D− / D+ | GPIO18 / GPIO19 | Reserved. Do not use for other functions. |
| Console | UART0, through the bridge | |
| BOOT button | GPIO9 (strapping) | Keep it accessible for recovery. |
| Onboard RGB LED | GPIO8 (strapping) | Status indicator. |
| Do not use for CEC | GPIO2, 8, 9, 18, 19 | Strapping pins and USB pins. |
| **Do not connect** | HDMI pin 18 (+5 V), pin 19 (HPD) | |

## HDMI breakout

The header has 8 pins. The labels are on the J1/J2 side of the board. From left to
right, with the male plug (J1) on the left:

| Header label | HDMI pin | Bench use |
|---|---|---|
| `GND` | Other grounds (not measured yet) | Not connected |
| `HP_DET` | 19 (HPD) | **Do not connect** |
| `+5V` | 18 (+5 V) | **Do not connect** |
| `DDC_GND` | 17 (DDC/CEC ground) | Devkit GND |
| `DDC_DATA` | 16 (SDA) | Not connected |
| `DDC_CLK` | 15 (SCL) | Not connected |
| `RSVD` | 14 (Utility / HEAC+) | Not connected |
| `CEC` | 13 (CEC) | Devkit GPIO10 |

The other side has TMDS test points (TP1 to TP8). Do not touch them. The `HP_DET` and
`+5V` pins are next to `DDC_GND`. Put tape on them, so that a jumper cannot touch them.

Photos: `Breadboard/HdmiBreakoutLabelSide.jpg` and `Breadboard/HdmiBreakoutBackSide.jpg`.

## Wiring

```
HDMI breakout header                  ESP32-C3-DevKitC-02
  CEC      (pin 13) ─────────────────  GPIO10 ──┐
  DDC_GND  (pin 17) ─────────────────  GND      │ jumper
                                       GPIO4  ──┘
  HP_DET, +5V: taped, not connected

USB breakout (native USB, data only)
  D−  ───────────────────────────────  GPIO18
  D+  ───────────────────────────────  GPIO19
  GND ───────────────────────────────  GND
  (VBUS not connected)

Host HDMI out ──► breakout J2 (female) … J1 (male) ──► TV HDMI 1
USB breakout ──► Host USB port
micro-USB ──► Mac (power, flashing, UART0 console through the bridge)
```

Leave VBUS of the USB breakout disconnected. The micro-USB port powers the board. Make
sure on the bench that the C3 enumerates without VBUS.

## Recovery

If the native USB device does not show, hold GPIO9 low and push reset. The chip then
starts in download mode.

## Captures

Put logic analyzer captures and bench photos in `Breadboard/`.
