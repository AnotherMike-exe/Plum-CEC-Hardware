# TODO — Plum-CEC-Hardware

Small work that has no issue yet. This file lives at `docs/TODO.md`.

Anything a user would ask for goes to GitHub instead:

- A feature somebody wants → a feature request issue
- A defect → a bug report issue
- A release worth planning → the roadmap

## Now

The M0 to M6 plan is in Plum-CEC-Firmware `docs/Plans/ProofOfConcept.md`.

- [ ] M0: measure the HDMI breakout (pins 13 and 17 pass through)
- [x] M0: make sure GPIO10 is on the DevKitC-02 header
- [ ] M0: add the bench photo as `Breadboard/BenchWired.jpg`

## Next

- [x] Record the TV make, model and HDMI port number (see [TvSettings.md](TvSettings.md))
- [x] Make sure the C3 enumerates on native USB with VBUS disconnected (see [HostSetup.md](HostSetup.md))

## Someday

- [ ] M8: decide on a PCB (HDMI inline, power OR-ing, CEC line protection)

## Scaffold gaps

- [ ] .github/workflows/Review.yml — ERC/DRC job skips until `KiCad/` holds a project
- [ ] docs/ARCHITECTURE.md — PCB power and protection design is not started (M8)
