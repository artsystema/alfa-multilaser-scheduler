# Codex task — Milestone M0

## Objective

Build a vendor-neutral Python dual-laser LPBF scheduling simulator. Do not implement Materialise- or ALT-specific behavior beyond explicit interfaces/stubs.

## Required work

1. Define explicit models for `Layer`, `ScanVector`, `VectorBlock`, `Laser`, `ScanField`, `RestrictedZone`, `ScheduleEntry` and `MachineProfile`.
2. Define a versioned internal JSON interchange schema.
3. Implement configurable block partitioning.
4. Implement a timing model for scanning, jumps, settling and vector transitions.
5. Implement:
   - single-laser baseline;
   - static dual split;
   - deterministic LPT;
   - conflict-aware LPT;
   - directional gas/plume exclusion;
   - banded conflict-aware scheduling.
6. Implement deterministic validation and metrics.
7. Create synthetic fixtures for rectangle, annulus, two lobes/bridge, sharp corners, partial FOV, directional plume conflict and an infeasible-concurrency case.
8. Build a 2D layer viewer with L1/L2 assignment, scan fields, gas-flow direction, interaction zones, execution order and a time position.
9. Export PNG, SVG, JSON schedules and CSV metrics.
10. Build a benchmark CLI.
11. Update `docs/unknowns.md` and `docs/decision-log.md` as implementation choices are made.
12. Run all tests and synthetic benchmarks before finishing.

## Hard rules

- Hard constraints may never be violated to improve makespan.
- Idle an optic when no safe pairing exists.
- Keep process parameters opaque and referenced.
- Do not invent Materialise SDK calls or ALT file fields.
- Do not implement direct machine control.
- Production export must fail closed while mandatory values are unknown.

## Definition of done

- installation works from a clean Python 3.11+ environment;
- JSON layer input/output is documented and tested;
- all required schedulers run deterministically;
- all accepted schedules have zero hard, FOV and configured plume violations;
- metrics distinguish exposure speedup from whole-machine/build time;
- visualization can reveal concurrent block pairs and conflict zones;
- synthetic regression tests pass;
- an appropriate large synthetic layer reaches at least 1.6× predicted exposure speedup when its configured constraints permit it;
- assumptions and unqualified machine values remain visible in `docs/unknowns.md`;
- README contains working setup, test and benchmark commands.

## Target command

```bash
python -m multilaser benchmark datasets/synthetic/large_rectangle.json
```

Expected report shape:

```text
Single laser predicted exposure : ... s
Dual laser predicted exposure   : ... s
Speedup                         : ...×
Parallel efficiency             : ...%
L1 utilization                  : ...%
L2 utilization                  : ...%
Hard conflicts                  : 0
FOV violations                  : 0
Fog-zone violations             : 0
```

## Stop condition after M0

Do not proceed automatically into sophisticated lookahead, reinforcement learning or production integration. The next priority is Gate G1: prove that one Materialise part can retain one topology while different vector blocks receive different optic ownership. Gate G2 then proves that ownership can reach an ALT-compatible representation.

