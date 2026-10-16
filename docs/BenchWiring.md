# Bench Wiring — Plum-CEC-Hardware

The breadboard setup for milestones M0 to M7. The PCB decision is M8.

## Parts

| Item | Notes |
|---|---|
| HDMI passthrough breakout (male and female, pins exposed) | Inline, or a tap only. Pins 13 (CEC) and 17 (GND) must pass through. |
| ESP32-C3-DevKitC-02 | The micro-USB port goes to a USB-UART bridge, not to the native USB of the C3. |
| USB breakout (USB-A plug or USB-C pigtail, D+ and D− exposed) | For the native USB phase (Phase 3). |
| Jumper wires, breadboard | |
| USB cables | Micro-USB for the devkit. A second data-only USB path for native USB. |
| Logic analyzer (recommended) | To examine CEC bit timing and ACK slots. |

## Pin plan

| Function | Pin | Notes |
|---|---|---|
| CEC data | GPIO10 | Open-drain, direct connection. CEC is 3.3 V logic. Make sure GPIO10 is on the header. |
| CEC ground | GND to HDMI pin 17 | |
| Native USB D− / D+ | GPIO18 / GPIO19 | Reserved. Do not use for other functions. |
| Console | UART0, through the bridge | |
| BOOT button | GPIO9 (strapping) | Keep it accessible for recovery. |
| Onboard RGB LED | GPIO8 (strapping) | Status indicator. |
| Do not use for CEC | GPIO2, 8, 9, 18, 19 | Strapping pins and USB pins. |
| **Do not connect** | HDMI pin 18 (+5 V), pin 19 (HPD) | |

## Wiring

```
HDMI breakout (passthrough)           ESP32-C3-DevKitC-02
  pin 13 CEC  ───────────────────────  GPIO10
  pin 17 GND  ───────────────────────  GND

USB breakout (native CDC, data only)
  D−  ───────────────────────────────  GPIO18
  D+  ───────────────────────────────  GPIO19
  (VBUS not connected)

micro-USB ────► PC (power, flashing, UART0 console through the bridge)
```

Leave VBUS of the USB breakout disconnected. The micro-USB port powers the board. Make
sure on the bench that the C3 enumerates without VBUS.

## Recovery

If the native USB device does not show, hold GPIO9 low and push reset. The chip then
starts in download mode.

## Captures

Put logic analyzer captures and bench photos in `Breadboard/`.
