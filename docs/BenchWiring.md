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

## Setup procedure

Do the sections in order. The total time is about 60 to 90 minutes. You need a
multimeter in continuity mode, jumper wires and tape.

### A. Measure the HDMI breakout (5 minutes)

1. Disconnect the breakout from the TV and the Host.
2. Measure from header `CEC` to header `DDC_GND`. The meter must not beep.
3. Measure from header `GND` to header `DDC_GND`. Write down the result.
4. Put tape on the header pins `HP_DET` and `+5V`.

### B. Check the devkit header (2 minutes)

1. Find GPIO4, GPIO10, GPIO18, GPIO19 and GND on the devkit header labels.
2. If one of them is not on the header, stop. Report the missing pin.

### C. Wire the bench (20 minutes)

CAUTION: Disconnect the devkit micro-USB cable before you wire. Disconnect the HDMI
cables from the TV and the Host.

1. Connect GPIO10 to header `CEC`.
2. Connect a devkit GND pin to header `DDC_GND`.
3. Connect GPIO4 to GPIO10 with a short jumper.
4. Connect USB breakout D− to GPIO18.
5. Connect USB breakout D+ to GPIO19.
6. Connect USB breakout GND to a devkit GND pin.
7. Make sure that the USB breakout VBUS is not connected.
8. Make sure that the tape covers `HP_DET` and `+5V`.
9. Connect the Host HDMI cable to breakout J2 (female).
10. Put breakout J1 (male) into TV HDMI 1.
11. Connect the USB breakout to a Host USB port.
12. Connect the devkit micro-USB to the Mac.
13. Take a photo of the bench. Save it in `Breadboard/BenchWired.jpg`.

### D. Set the TV (10 minutes)

Go to Settings → External inputs → BRAVIA Sync settings.

1. Set "BRAVIA Sync control" to on.
2. Set "Wake-up on signal" to off, if the menu has it.
3. Set "Idle TV standby" to off. On this model, it can be under Settings → Power.
4. Write down the name and value of every item on the BRAVIA Sync settings screen.

### E. Set the Host BIOS (15 minutes)

1. Restart the Host and open the BIOS setup.
2. Set "ErP" (or "EuP") to disabled.
3. Set "Wake from S3 by USB" (or "USB power in S3") to enabled.
4. Save the settings, and let CachyOS start.
5. Optional: connect a network cable to the Host for Wake-on-LAN.

### F. Report

Send the results of A and B, the list from D, and "done" for C and E. The next
session then checks the wiring from the Host and the Adapter (plan steps 4 and 5).

## Recovery

If the native USB device does not show, hold GPIO9 low and push reset. The chip then
starts in download mode.

## Captures

Put bench photos and timing captures in `Breadboard/`.
