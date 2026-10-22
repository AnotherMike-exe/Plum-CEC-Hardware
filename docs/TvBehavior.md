# TV Behavior

How the bench TV reacts on the CEC bus. Recorded on 2026-10-21 (plan step 5, M1).
The TV settings are in [TvSettings.md](TvSettings.md).

## Bus

| Logical address | Device | Physical address | Vendor ID |
|---|---|---|---|
| 0 | TV, Sony FW-55BZ40H | `0.0.0.0` | `08:00:46` (Sony) |
| 4 | Adapter, Playback 1 | `1.0.0.0` (HDMI 1) | none yet |
| 5 | Soundbar "Infini Pro" (Audio System) | `3.0.0.0` (HDMI 3, ARC) | `00:25:D1` |

The Adapter ran the upstream `hdmi_cec` component in promiscuous mode, with the M1 test
package (firmware `packages/m1_bench.yaml`).

## Frames sent by the Adapter

The soundbar is not visible on the bench. Its column comes from the bus only.

| Frame | Name | TV on | TV off | Soundbar (from the bus) |
|---|---|---|---|---|
| `40:04` | Image View On | Switches to HDMI 1 after about 2 s | **Turns on.** It shows its own source first, and switches to HDMI 1 after 2 to 25 s. | Reports "On" right after the TV wakes |
| `4F:82:10:00` | Active Source | **Switches to HDMI 1** (Routing Change `0.0.0.0` → `1.0.0.0`, then Set Stream Path `1.0.0.0`) | No change. It does not wake the TV. | No frames |
| `40:36` | Standby to TV | **Turns off** | No change | The TV mutes it (`05:44:43`) and ends ARC (`05:C4`, `50:C5`, `05:C2`) |
| `4F:36` | Standby broadcast | **Turns off** | No change | Same frames as Standby to TV |
| `40:9D:10:00` | Inactive Source | No change on the bus | No change | No frames |

## Frames sent by the TV

| Event | Frames |
|---|---|
| Idle, every 60 s | `05:8F` Give Device Power Status, answer `50:90:00` "On" |
| Off with the remote | Mute and ARC end to the soundbar, Sony vendor command `0F:A0:08:00:46:00:09:00:01`, **`0F:36` Standby broadcast**, `5F:72:00` System Audio Mode off |
| Off from an Adapter Standby | The same frames, **but no `0F:36`** |
| On | `0F:84:00:00:00` Report Physical Address, `0F:87:08:00:46` Vendor ID, `0F:82:00:00` Active Source (the TV itself), then System Audio Mode and ARC start with the soundbar |
| Input change | `0F:80:<old>:<new>` Routing Change, for example `0F:80:10:00:20:00` for HDMI 1 → HDMI 2 |
| A new device on the bus | `04:83` Give Physical Address, then `04:46` Give OSD Name and `04:8C` Give Device Vendor ID, 5 times each, 2 s apart, when there is no answer |

The TV also sends Sony vendor commands (`0F:A0:08:00:46:...`) that this plan does not
decode. The soundbar answers the vendor commands sent to it with Feature Abort
(`50:00:A0:04`).

## Decisions

**Host sleep frame: Standby to TV (`40:36`).** It turns off the TV the same as the
broadcast, and the soundbar gets the same frames. It does not command every device on the
bus.

**Host wake: Image View On, then Active Source.** Image View On wakes the TV. Active
Source alone does not. Active Source then makes the TV switch to HDMI 1 at once.

**"TV off, Host sleeps" (ADR 0005, optional):** use the TV `0F:36` broadcast. The TV
sends it only when a person turns it off, not when the Adapter does.

## Open items

- Many Adapter sends needed 2 to 6 attempts. On the first attempts the TV gave no ACK, or
  the frame broke after 1 to 4 bytes. All frames got through. Plan step 6 measures the
  bit timing with the probe on GPIO4.
- The Adapter does not answer Give OSD Name or Give Device Vendor ID yet. The kernel
  driver answers them while Attached (plan step 8). The Adapter must answer them while
  Detached (plan step 9).
