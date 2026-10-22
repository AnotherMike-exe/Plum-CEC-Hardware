---
status: accepted, supersedes ADR 0001
---

# One USB-C port for power and data

The PCB has one USB-C receptacle. It gives the Adapter its power and the USB 2.0 data
path to the Host. There is no second power input and no OR-ing. The user chooses what the
port connects to, and that choice sets the functions:

| USB-C connects to | CEC and Network front-end | USB front-end |
|---|---|---|
| A USB charger | Always on | No |
| A Host port that keeps 5 V in S3 | Always on | Yes |
| A Host port that loses 5 V in S3 | Off while the Host sleeps | Yes, while the Host is awake |
| Nothing | Off. The board is a passive HDMI cable. | No |

We chose this over ADR 0001 (a separate 5 V supply, USB data only) because one cable is
simpler for the user, and an unpowered Adapter no longer loads the CEC line (ADR 0004).
The user documentation must say which Host ports keep power in S3.

## Consequences

- The USB-C receptacle has 5.1 kΩ pull-downs on CC1 and CC2, so a C-to-C source gives 5 V.
- The USB specification limits a device to 2.5 mA while the bus is suspended. The Adapter
  with WiFi takes about 80 mA. Most motherboards do not enforce the limit. A board that
  does enforce it acts like a port that loses 5 V in S3.
