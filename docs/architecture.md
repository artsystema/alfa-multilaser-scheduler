# Architecture

## Intended production boundary

```text
single source mesh
    ↓
Materialise Magics / Next-Gen Build Processor
    ↓
contours, hatches and vector blocks
    ↓
multi-optic eligibility, assignment and scheduling
    ↓
L1/L2 vector jobs
    ↓
existing ALT-compatible downstream workflow
    ↓
ALFA-280
```

The scheduler describes which validated exposure vectors each optic should execute and in what order. It does not drive lasers or galvos.

## Layering

1. **Model** — vendor-neutral build, layer, vector, block, laser and schedule entities.
2. **Geometry** — scan fields, reachability, partitioning, distance and gas-flow zones.
3. **Scheduling** — assignment and ordering strategies.
4. **Simulation** — clock, timing, validation and metrics.
5. **Visualization** — deterministic inspection of layer schedules.
6. **Adapters** — JSON first; Zenodo, Materialise and ALFA later.

Vendor-specific types must not leak into layers 1–5.

## Separate concerns

- **Eligibility:** which optics may process a block.
- **Assignment:** which eligible optic owns it.
- **Scheduling:** ordering and approximate concurrent timing.
- **Validation:** independent rejection of invalid output.

## Constraint hierarchy

Hard constraints:

- scan-field reachability;
- configured interaction/collision exclusion;
- directional gas/plume exclusion;
- explicitly single-laser regions;
- unresolved mandatory production values.

Soft objectives apply only after hard constraints pass:

- makespan;
- idle time;
- jump distance;
- thermal proximity;
- plume risk outside the hard exclusion zone;
- repeated layer-boundary risk.

