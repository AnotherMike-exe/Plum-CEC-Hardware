# TV Settings

The bench TV and its CEC menu values. Recorded on 2026-10-21 (plan step 3).

## TV

| Item | Value |
|---|---|
| Model | Sony FW-55BZ40H |
| Host input | HDMI 1, physical address `1.0.0.0` |
| Other CEC device | Soundbar on HDMI 3 (ARC) |

## BRAVIA Sync settings

Path: Settings → External inputs → BRAVIA Sync settings.

| Item | Value | Why |
|---|---|---|
| BRAVIA Sync control | On (the switch is grey and locked) | CEC is on. The TV does not let you change it. |
| Auto devices off | On | The TV sends Standby to HDMI devices when it turns off. This puts the Host to sleep. |
| Auto TV on | On | Image View On from a device turns on the TV. This lets the Host wake the TV. |
| Device control keys | Normal | The TV sends remote key presses to the active device. |

The menu has no "Wake-up on signal" item on this model.

The "BRAVIA Sync control" switch is locked in the on position. The two items below it
stay active, and the TV makes them active only when CEC is on. The cause is probably
the ARC soundbar on HDMI 3. This does not block the plan.

## Power settings

Path: Settings → Power.

| Item | Before | Now |
|---|---|---|
| Idle TV standby | 4 hours | Off |

## BRAVIA Sync device list

Path: Settings → External inputs → BRAVIA Sync settings → BRAVIA Sync device list.

| Device | Input | Type |
|---|---|---|
| Audio system | HDMI 3 | Infini Pro |

The Host is not in the list. This is correct before plan step 5: the Host GPU has no
CEC, and the Adapter firmware does not run CEC yet. After step 5, the Adapter must show
on HDMI 1.
