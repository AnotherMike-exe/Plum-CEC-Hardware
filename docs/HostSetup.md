# Host Setup

The bench Host and its sleep and wake settings. Checked on 2026-10-21 (plan step 3).

## Host

| Item | Value |
|---|---|
| OS | CachyOS, kernel 7.2.9-1-cachyos, KDE Plasma 6 (Wayland) |
| Motherboard | MSI MAG B550 TOMAHAWK MAX WIFI (MS-7C91), BIOS 2.A0 |
| GPU | RTX 2080, open kernel module, kernel suspend notifiers |
| HDMI | GPU HDMI out → breakout J2 → TV HDMI 1 |

## BIOS

MSI Click BIOS, Advanced mode.

| Path | Item | Value |
|---|---|---|
| Settings → Advanced → Power Management Setup | ErP Ready | Disabled |
| Settings → Advanced → Wake Up Event Setup | Wake Up Event By | BIOS |
| Settings → Advanced → Wake Up Event Setup | Resume By USB Device | Enabled |

ErP must be disabled. When it is on, the board cuts USB standby power in sleep.

## Linux sleep settings

A script on the Host applies these settings. It is idempotent and keeps a backup of each
file it changes.

```bash
sudo bash ~/plumcec/apply_root.sh   # apply
sudo bash ~/plumcec/undo_root.sh    # undo
```

| Setting | File | Value |
|---|---|---|
| Sleep mode | `/etc/systemd/sleep.conf.d/50-plumcec.conf` | `MemorySleepMode=deep` (ACPI S3) |
| Idle action | `/etc/systemd/logind.conf.d/50-plumcec.conf` | `IdleAction=ignore` |
| Wake device | `/etc/udev/rules.d/90-plumcec-steam-puck.rules` | Steam Controller Puck `28de:1304`: `power/wakeup=enabled`, `power/control=on` |
| Wake-on-LAN | NetworkManager, wired profile on `enp42s0` | `wake-on-lan magic` |

`sleep.target` and `suspend.target` stay unmasked.

## USB wake

| Device | USB ID | `power/wakeup` |
|---|---|---|
| Steam Controller Puck | `28de:1304` | enabled |
| Adapter (C3 USB Serial/JTAG) | `303a:1001` | none (the device does not offer remote wakeup) |

The Adapter must not wake the Host. Its configuration descriptor has
`bmAttributes 0xc0` (self powered, no remote wakeup), so Linux gives it no
`power/wakeup` file.

Use the Adapter by its stable path. `ttyACM0` is the Steam Controller Puck.

```
/dev/serial/by-id/usb-Espressif_USB_JTAG_serial_debug_unit_58:CF:79:D8:71:70-if00
```

## CEC tools

| Tool | Version | Source |
|---|---|---|
| `cec-ctl`, `cec-compliance` | v4l-utils 1.32.0 | pacman |
| `cec-client` | libcec 8.1.7 | pacman |
| `inputattach` with `--pulse8-cec` | 1.8.1 | Built from the SourceForge tarball, installed to `/usr/local/bin` |
| `pulse8-cec` kernel module | in-tree | kernel 7.2.9-1-cachyos |

To remove `inputattach`: `sudo rm /usr/local/bin/inputattach`.

## Check

```bash
cat /sys/power/mem_sleep                  # s2idle [deep]
inputattach --help | grep -i pulse8       # --pulse8-cec
modinfo -F filename pulse8-cec
lsusb -d 303a:1001                        # the Adapter on native USB
```
