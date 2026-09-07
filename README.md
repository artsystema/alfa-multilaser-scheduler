# ALFA Multilaser Scheduler

Vendor-neutral simulation and scheduling of LPBF exposure vectors across multiple optics, initially targeting the dual-laser ALFA-280 workflow.

The project schedules **exposure workload**, not CAD solids. Materialise remains the intended slicing/build-preparation boundary, while ALT remains authoritative for machine execution, scanner timing, interlocks and firmware.

## Current status

**Planning complete; Milestone M0 is ready for implementation.**

The locked sequence is:

1. collect Materialise/ALT evidence in parallel;
2. build the vendor-neutral M0 simulator;
3. prove Materialise per-vector optic ownership (Gate G1);
4. prove an ALT-compatible downstream representation (Gate G2);
5. validate real layers offline;
6. print conservative coupons;
7. qualify the process;
8. optimize toward 1.6–1.8× exposure speedup.

## Start here

- [Complete engineering plan](docs/project-plan.md)
- [Current Codex task](docs/codex-m0.md)
- [Architecture boundaries](docs/architecture.md)
- [Knowns and unknowns](docs/unknowns.md)
- [Validation plan](docs/validation-plan.md)
- [Project rules](AGENTS.md)
- [Upload printer jobs](datasets/alfa_samples/incoming/) and [sample handling instructions](datasets/alfa_samples/README.md)

## M0 scope

M0 is a deterministic Python simulator with:

- vendor-neutral layer/vector/block models;
- configurable block partitioning;
- single-laser baseline, static split, LPT, conflict-aware LPT and banded scheduling;
- scan-field and directional gas/plume constraints;
- validation, metrics and 2D visualization;
- synthetic fixtures and deterministic tests.

Materialise and ALT code must remain interfaces/stubs until genuine documentation and artifacts are supplied.

## Safety boundary

This repository is **not** a printer controller. It must not generate raw galvo commands, replace safety interlocks, invent material parameters or patch production machine files using guessed binary offsets.

## Package

- Repository: `alfa-multilaser-scheduler`
- Python package: `multilaser`
- Planned CLI: `multilaser`

## License

No license has been selected yet.

