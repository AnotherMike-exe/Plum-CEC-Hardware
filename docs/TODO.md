# TODO — Plum-CEC-Hardware

Small work that has no issue yet. This file lives at `docs/TODO.md`.

Anything a user would ask for goes to GitHub instead:

- A feature somebody wants → a feature request issue
- A defect → a bug report issue
- A release worth planning → the roadmap

## Now

The M0 to M6 plan is in Plum-CEC-Firmware `docs/Plans/ProofOfConcept.md`.

- [ ] M0: measure the HDMI breakout (pins 13 and 17 pass through)
- [ ] M0: make sure GPIO10 is on the DevKitC-02 header

## Next

- [ ] Record the TV make, model and HDMI port number (physical address `N.0.0.0`)
- [ ] Make sure the C3 enumerates on native USB with VBUS disconnected

## Someday

- [ ] M8: draw the schematic from ADR 0003 and ADR 0004. Choose the HDMI connectors first.

## Scaffold gaps

- [ ] .github/workflows/Review.yml — ERC/DRC job skips until `KiCad/` holds a project
- [ ] docs/ARCHITECTURE.md — PCB power tree, ESD protection and part numbers are not chosen (M8)
