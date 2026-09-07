# Decision log

## 2026-09-07 — Repository and package names

- Repository: `alfa-multilaser-scheduler`
- Python package and planned CLI: `multilaser`
- Reason: identify the ALFA target without permanently limiting the architecture to exactly two optics.

## 2026-09-07 — Vendor-neutral M0 first

Build M0 without waiting for Materialise access. The simulator remains valuable for independent validation even if Materialise Load Balancer becomes the production scheduler.

## 2026-09-07 — Early external feasibility gates

After M0, prioritize:

- G1: one Materialise part with mixed L1/L2 vector-block ownership;
- G2: preservation of that ownership into an ALT-compatible downstream representation.

Do not invest heavily in advanced scheduling until these gates are resolved.

## 2026-09-07 — Workload, not geometry

The production goal is to distribute layer exposure vectors. The current inner/outer mesh split remains a control/benchmark only.

## 2026-09-07 — Safety boundary

ALT remains authoritative for real-time execution and machine safety. This repository will not own raw scanner synchronization, interlocks or firmware.

## 2026-09-07 — Performance target

Use 1.6–1.8× exposure speedup as the design target on suitable large layers. Do not pursue near-2× results by relaxing hard constraints.

