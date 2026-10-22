---
status: superseded by ADR 0003
---

# Separate power for the Adapter

The Adapter takes its power from a separate 5 V USB supply, not from the Host. The
native USB connection to the Host carries data only (VBUS not connected). Many Host
motherboards cut USB power in S3 and S5, and the Network front-end must work while the
Host sleeps. An unpowered Adapter can also pull the CEC line low. A production unit
either keeps this layout, or its documentation tells users to make sure that their Host
keeps USB power in sleep.
