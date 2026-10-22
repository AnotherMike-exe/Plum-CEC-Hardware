# A passive HDMI 2.1 passthrough board, not a repeater

The PCB is an inline HDMI board: a board-mount HDMI plug and a board-mount HDMI
receptacle on opposite edges. All 19 pins go straight through as copper, so without power
the board is a passive HDMI cable. The Adapter taps only CEC (pin 13), SCL (pin 15) and
SDA (pin 16), through isolation that does not load a line while the Adapter has no power.
It does not tap pin 18 (+5 V) or pin 19 (HPD).

## Board rules

- Target: HDMI 2.1 FRL (48 Gbit/s). No pigtail cables.
- Stackup, 4 layers: L1 components and low-speed signals, L2 and L3 ground, L4 the four
  HDMI pairs. 100 Ω differential, impedance control ordered from the fab.
- Each pair stays on one layer, with at most one via transition from end to end. Vias
  are through vias L1 to L4 only (no stub), symmetric for P and N, with two ground vias
  next to each signal via pair. Length match inside each pair.
- Pins 14 and 19 (eARC/HEAC) are routed as a pair.
- All HDMI ground pins and both connector shells join one ground plane. The pairs use it
  as their return path.
- Choose the connectors first. The mounting type (straddle or top mount) sets where the
  vias go.
- Layout: HDMI connectors on the top and bottom edges, ESP32-C3-MINI-1 on the right edge
  with the antenna over the edge or in a copper keep-out on all layers, USB-C on the left
  edge. Keep the antenna about 5 mm or more from the HDMI shells. USB D+ and D− cross the
  HDMI channel on L1, with the ground planes between.
- Isolation: one BSS138-type N-MOSFET for each tap (NXP AN10441 circuit), with the pull-up
  on the ESP32 side. With no power, the gate is at 0 V and the body diode is
  reverse-biased. The ESP32 GPIO stays open-drain in both directions.
- The button is on GPIO9 (boot strap pin, also the function key). The RGB LED is on GPIO8
  and is optional. No reset button: USB flashing needs none.
- The enclosure is 3D-printed plastic.

## Considered options

- **HDMI repeater with EDID rewrite** (as an HDMI soundbar does). The Adapter would get its
  own physical address, and the source behind it a new one. Rejected: the MCU must cut
  DDC and forward EDID, HDCP (0x3A) and SCDC (0x54) traffic. That breaks the passive
  passthrough without power, puts FRL link training at risk, and needs HPD control on
  pin 19. A real repeater uses an HDMI 2.1 repeater or retimer chip, which needs an NDA
  and an HDCP license. The owner does not plan this revision.
- **Split TX and RX on two GPIOs** (MOSFET for TX, Ioff buffer for RX). Rejected: it needs
  changes in `hdmi_cec` and in the TX read-back check. The single MOSFET keeps one pin.
- **An active DDC read.** Rejected: it can collide with the GPU on the DDC bus. The
  Adapter reads the EDID passively, when the source reads it (firmware ADR 0006).
