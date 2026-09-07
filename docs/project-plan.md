# Dual-Laser Load Balancing for ALFA-280

## Engineering / Codex Handoff Plan — v0.3

**Repository:** `artsystema/alfa-multilaser-scheduler`  
**Status:** implementation-ready for Milestone M0  
**Primary objective:** schedule the exposure workload of one continuous LPBF part between two lasers without splitting the source CAD into artificial inner/outer solids.  
**Target:** 1.6–1.8× simulated exposure speedup on suitable large layers, subject to zero hard-constraint violations.

---

## 1. Executive decision

The project will use a two-track sequence:

1. Build a vendor-neutral simulator and deterministic safe scheduler first.
2. Immediately after M0, test the decisive Materialise/ALT compatibility boundary.
3. Only after that gate passes, invest in more sophisticated scheduling and production integration.

The simulator is worthwhile even if Materialise's built-in Load Balancer becomes the production scheduler. It provides an independent way to model ALFA geometry, compare strategies, validate schedules, visualize conflicts, and benchmark Materialise output.

The decisive feasibility question is:

> Can one Materialise part retain one geometric topology while different hatch/vector blocks in a layer receive different optic ownership, and can that ownership survive into a representation accepted by the existing ALT workflow?

This question takes priority over advanced algorithm development after M0.

---

## 2. Locked priority order

### Priority 0 — collect evidence in parallel

Obtain and preserve:

- Materialise Next-Gen BP SDK documentation, examples, headers, licensing and runtime requirements;
- ALFA dual-optic scanner-field geometry;
- gas-flow direction and any known fog-zone guidance;
- current Magics/ALT intermediate and final artifacts;
- four minimal differential jobs: one cube on L1, the same cube on L2, two cubes split between L1/L2, and the current inner/outer workaround.

This evidence collection does not block M0.

### Priority 1 — Milestone M0: vendor-neutral simulator

Build the smallest complete deterministic system that can:

- represent layers, vectors, vector blocks, lasers, scan fields and schedules;
- partition synthetic layers into configurable blocks;
- estimate single- and dual-laser exposure time;
- implement static split, LPT, conflict-aware LPT and banded scheduling;
- enforce scanner-field, spatial-interaction and directional gas/plume constraints;
- validate every schedule;
- report metrics;
- visualize assignments and concurrent execution;
- reproduce results deterministically in tests.

M0 must not depend on Materialise or ALT libraries.

### Priority 2 — Gate G1: Materialise vector-level feasibility

As soon as M0 is complete and genuine Materialise BP documentation is available, create the smallest possible adapter experiment:

```text
one mesh
  ↓
one generated layer
  ↓
several vector blocks
  ↓
at least one block owned by L1
  ↓
at least one block owned by L2
```

No full build, optimization or production export is required at this gate.

### Priority 3 — Gate G2: ALT compatibility boundary

Determine whether Materialise's multi-optic vector ownership can reach the existing ALT-supported downstream workflow through:

1. an existing ALT importer/postprocessor; or
2. a small documented compatibility converter.

Differential artifact analysis may locate object, layer, vector or optic identity, but it must not become guessed binary patching of production machine files.

### Priority 4 — real-layer offline validation

If G1 and G2 pass:

- import real ALFA layers into the internal model;
- compare our scheduler with Materialise Load Balancer where available;
- validate reachability, conflict rules, gas-flow rules and deterministic output;
- calibrate the timing model against known jobs;
- decide whether the production route should use Materialise scheduling, our scheduler, or a hybrid.

### Priority 5 — conservative coupon printing

Begin only after offline validation. Keep qualified process parameters unchanged and alter scheduling/ownership only.

Progression:

1. existing single-laser control;
2. current two-mesh inner/outer control;
3. block-scheduled dual laser with conservative separation;
4. band optimization;
5. distance/plume optimization;
6. qualification of handoff regions and layer-to-layer boundary shifting.

### Priority 6 — optimize toward 1.6–1.8×

Tune block size, band width, minimum simultaneous distance, gas/fog zones, assignment balance, layer phase and jump penalties only after mechanical and metallurgical validation.

Do not pursue 1.95–2.0× at the expense of safe separation or material quality.

---

## 3. Production architecture

```text
Original single mesh
        │
        ▼
Materialise Magics
        │
        ▼
Next-Gen Build Processor / BP SDK
        │
        ▼
contours + hatch vectors + vector blocks
        │
        ▼
multi-optic assignment and safe scheduling
        │
     ┌──┴──┐
     ▼     ▼
    L1     L2
     └──┬──┘
        ▼
existing ALT-compatible downstream workflow
        │
        ▼
ALFA-280
```

ALT remains authoritative for real-time scanner execution, laser timing, safety interlocks and machine firmware. This project is a build-preparation and scheduling layer, not a machine controller.

Preferred integration paths:

1. Materialise built-in Load Balancer configured for ALFA.
2. Materialise Load Balancer plus custom ALFA constraints.
3. Our deterministic scheduler integrated into a custom BP.

Avoid direct galvo or laser control.

---

## 4. Success criteria

| Metric | Requirement |
|---|---:|
| Minimum useful simulated exposure speedup | ≥1.50× |
| Target simulated exposure speedup | 1.60–1.80× |
| Hard interaction violations | 0 |
| Scanner-field violations | 0 |
| Unqualified production exports | 0 |
| Deterministic identical-input output | 100% |
| Artificial CAD internal surfaces introduced | 0 |

Exposure improvement and whole-build improvement must be reported separately because recoating, Z movement and other fixed operations do not parallelize.

---

## 5. Core design rules

### Split exposure workload, not geometry

Assignment happens after slicing and process-region classification:

```text
one model → one layer → contours + hatches → vector blocks → L1/L2 ownership
```

The existing inner/outer mesh split remains a benchmark/control only.

### Separate eligibility, assignment and scheduling

- **Eligibility:** which lasers can reach and process a block.
- **Assignment:** which eligible laser owns it.
- **Scheduling:** execution order and approximate concurrent timing.

### Hard constraints never become penalties

The optimizer may idle a laser. It may not violate scanner reachability, collision/interaction, gas/plume or explicitly single-laser rules to improve makespan.

### Keep material parameters opaque

Blocks reference existing validated parameter sets. The scheduler does not invent power, speed, hatch spacing or metallurgy recipes.

### Start coarse

Explore configurable blocks around 5, 10, 15 and 20 mm. These are software test values, not qualified process settings. Do not start by alternating individual hatch lines.

### Contours are conservative in v1

One continuous external contour is assigned to one laser. Dual-laser scheduling initially applies to suitable hatch/core vectors.

### Gas flow is first-class

The interaction model must support a directional downwind plume/fog region, not only a symmetric distance circle.

### Avoid fixed vertical ownership boundaries

Support configurable layer-to-layer shifts of block origin, band origin, preferred ownership or boundary position to avoid a persistent stitch plane.

---

## 6. Minimum data model

Required entities:

- `Build`
- `Layer`
- `Laser`
- `ScanField`
- `ScanVector`
- `VectorBlock`
- `RestrictedZone`
- `ScheduleEntry`
- `MachineProfile`
- `ConflictModel`
- `ThermalRiskModel` interface

Minimum block fields:

```python
VectorBlock:
    id
    layer_id
    vectors
    bbox
    centroid
    total_scan_length
    estimated_scan_time
    eligible_lasers
    process_class
    parameter_set_ref
    neighbors
```

Minimum schedule entry:

```python
ScheduleEntry:
    laser_id
    block_id
    start_time
    end_time
    jump_from
```

Use a vendor-neutral JSON schema as the internal interchange format. Materialise and ALT remain adapters outside the scheduling core.

---

## 7. Constraint model

### H1 — scanner reachability

Support full-overlap and partial-overlap dual scan fields from the beginning.

### H2 — spatial interaction

Initial implementation may use configurable minimum block distance, but expose:

```python
ConflictModel.conflicts(a, b, time_a, time_b)
```

so the rule can evolve beyond Euclidean distance.

### H3 — directional gas/plume exclusion

Support a configurable oriented rectangle, capsule or cone extending downwind from active exposure.

Example profile fields:

```yaml
gas_flow:
  direction_deg: 0
  model: directional_capsule
  upstream_clearance_mm: null
  downwind_length_mm: null
  lateral_width_mm: null
```

`null` means unqualified. Production export must fail closed when a mandatory value is unresolved.

### H4 — single-laser regions

Allow contours, sharp features, narrow walls, small islands and manually marked regions to be restricted to one laser.

---

## 8. Scheduler implementation order

1. Single-laser baseline.
2. Static left/right split.
3. Deterministic LPT.
4. Conflict-aware LPT with reachability and directional plume constraints.
5. Banded conflict-aware scheduling.
6. Limited deterministic lookahead, only after priorities G1/G2 are resolved.

Do not begin with reinforcement learning, melt-pool AI or full thermal simulation.

Soft objective after all hard constraints pass:

```text
minimize:
  makespan
  + idle time
  + jump distance
  + thermal proximity risk
  + plume risk
  + repeated layer-boundary risk
```

---

## 9. Timing and metrics

Minimum timing model:

```text
T_scan  = total_vector_length / scan_speed
T_jump  = jump_distance / jump_speed
T_block = T_scan + T_jump + settle_time + transition_overhead
```

Required per-layer metrics:

- single-laser exposure time;
- dual-laser makespan;
- speedup and parallel efficiency;
- L1/L2 exposure time and utilization;
- L1/L2 idle time and jump distance;
- number of blocks;
- hard-conflict, plume-conflict and FOV-violation counts;
- assignment balance.

```text
speedup = single_laser_time / dual_laser_makespan
parallel_efficiency = speedup / 2
```

---

## 10. Mandatory visualization

Provide a 2D layer viewer with:

- geometry and vector blocks;
- L1/L2 coloring;
- scanner fields;
- gas-flow arrow and interaction zones;
- execution order;
- current concurrent pair;
- time slider or simple animation.

Export PNG, SVG, JSON schedule and CSV metrics. Visualization is a validation tool, not presentation polish.

---

## 11. Synthetic fixtures

M0 includes deterministic fixtures for:

1. large rectangle — ideal balancing case;
2. annulus — contour and disconnected hatch behavior;
3. two lobes with narrow bridge — geometry-dependent balance;
4. sharp corners — restricted/single-laser zones;
5. partial scan-field overlap — eligibility and ownership;
6. directional plume conflict — safe pairing must depend on gas direction;
7. infeasible concurrent case — scheduler must idle rather than violate a hard rule.

After M0, add the Yang et al. Zenodo examples through a separate adapter for research/regression comparison. Do not claim exact reproduction unless verified against the source implementation.

---

## 12. Repository structure

```text
dual-laser-alfa/
├─ README.md
├─ AGENTS.md
├─ pyproject.toml
├─ docs/
│  ├─ architecture.md
│  ├─ research.md
│  ├─ materialise-integration.md
│  ├─ alfa-integration.md
│  ├─ validation-plan.md
│  ├─ decision-log.md
│  └─ unknowns.md
├─ src/multilaser/
│  ├─ model/
│  ├─ geometry/
│  ├─ scheduling/
│  ├─ simulation/
│  ├─ adapters/
│  │  ├─ json_adapter.py
│  │  ├─ zenodo_adapter.py
│  │  ├─ materialise/
│  │  └─ alfa/
│  └─ visualization/
├─ tests/
│  ├─ unit/
│  ├─ integration/
│  ├─ golden/
│  └─ fixtures/
├─ datasets/
│  ├─ synthetic/
│  ├─ zenodo/
│  └─ alfa_samples/
└─ tools/
   ├─ compare_jobs.py
   ├─ inspect_binary.py
   └─ benchmark.py
```

Python is preferred for M0. If the BP SDK requires another language, use a thin native adapter or port only the necessary boundary after the real API is known.

---

## 13. Milestones and gates

### M0 — repository and simulator

Definition of done:

- complete vendor-neutral data model;
- JSON import/export;
- block partitioning;
- scan-field geometry;
- timing model;
- single-laser, static split, LPT, conflict-LPT and banded algorithms;
- directional gas-flow conflict model;
- validator and metrics;
- deterministic tests and synthetic fixtures;
- layer viewer and benchmark CLI;
- `docs/unknowns.md` distinguishes facts, assumptions and unqualified values;
- zero hard violations on accepted schedules;
- ≥1.6× simulated speedup on an appropriate large synthetic layer, where configured constraints permit it.

Target CLI:

```bash
python -m multilaser benchmark datasets/synthetic/large_rectangle.json
```

### G1 — Materialise vector ownership

**Pass:** one part can contain vector blocks owned by L1 and L2 without changing mesh topology.  
**Fail/red flag:** the customer BP cannot express or export per-block optic ownership.

### G2 — ALT-consumable representation

**Pass:** vector-level optic ownership survives through an existing importer/postprocessor or a documented small converter.  
**Fail/red flag:** Materialise requires an inaccessible OEM writer or ALT accepts laser identity only at mesh/object level.

If G1 or G2 fails, pause sophisticated scheduler work and reassess Materialise Professional Services, neutral toolpath environments or required ALT cooperation.

### M1 — published dataset adapter

Import supported Yang et al. Zenodo layer data, compare sequential/LPT behavior, and document verified versus approximate reproduction.

M1 may proceed while external interface evidence is being collected, but it is lower priority than G1/G2.

### M2 — ALFA artifact analysis

Build a comparison report from minimal real jobs. Classify findings as `CONFIRMED`, `LIKELY` or `UNKNOWN`. Do not build a production patcher.

### M3 — minimal Materialise adapter

Implement only against supplied official SDK documentation/examples. Prove one-layer, one-mesh, mixed-optic vector ownership before attempting full builds.

### M4 — offline real-layer validation

Calibrate timing, validate constraints and compare scheduler options using real ALFA layers.

### M5 — coupons and qualification

Use conservative separation, control coupons and inspection of density/porosity, stitch regions, dimensions, surface quality and required mechanical properties.

---

## 14. Differential ALT artifact set

Collect these with identical settings wherever possible:

- **Job A:** one cube, L1;
- **Job B:** same cube, L2;
- **Job C:** two cubes, one assigned to each laser;
- **Job D:** current inner/outer split assigned L1/L2;
- **Job E, if possible:** equivalent single-laser version of Job D.

Preserve every intermediate file, not only the final machine file.

`tools/compare_jobs.py` should report hashes, sizes, headers, strings, changed byte ranges and discoverable structures. Its purpose is to locate the supported interface boundary, not infer raw scanner commands.

---

## 15. Physical validation order

Use coupon geometry containing L1-dominant, mixed/handoff and L2-dominant regions, alongside single-laser controls.

Keep existing qualified power, speed, hatch and layer parameters constant initially. Change only ownership/scheduling so the result can be attributed.

No production component is used until conservative coupons pass internal inspection criteria.

---

## 16. Configurable parameters

Never hard-code:

- laser scan fields;
- gas-flow direction;
- safe separation;
- plume/fog geometry;
- block size;
- band width and origin;
- jump speed and settle overhead;
- contour policy;
- layer phase shift;
- restricted/single-laser regions.

Example:

```yaml
machine:
  name: alfa280-dual

lasers:
  - id: 1
  - id: 2

scheduler:
  algorithm: banded_conflict_lpt
  block_size_mm: 10
  band_width_mm: 30

interaction:
  safe_distance_mm: null
  gas_flow_model: directional

contours:
  dual_laser: false
```

---

## 17. Explicit non-goals for v1

- direct printer or galvo control;
- raw scanner commands;
- safety-interlock replacement;
- laser power or hatch recipe optimization;
- process-parameter invention;
- melt-pool AI;
- reinforcement learning;
- full finite-element thermal simulation;
- automatic qualification;
- production binary patching based on guessed offsets.

The v1 problem is narrowly defined:

> Which existing validated vectors should L1 and L2 execute, in what order, while all hard constraints remain satisfied?

---

## 18. Project rules for `AGENTS.md`

```markdown
# Project rules

This project schedules LPBF exposure vectors between two lasers.

Do not invent Materialise BP SDK APIs. Implement Materialise adapter methods only when supported by supplied official SDK documentation or examples.

Never generate production machine files from guessed binary fields.

Keep the vendor-neutral scheduler independent from Materialise and ALT.

Hard constraints must never be converted into soft penalties.

All scheduler results must pass validator checks before export.

Keep material/process parameters opaque. Reference validated parameter sets; do not invent laser power, hatch spacing or scan-speed recipes.

Every algorithm requires deterministic fixtures and benchmark tests.

Record every new machine-specific assumption in docs/unknowns.md.

Fail closed for production export when mandatory machine or safety values remain unknown.
```

---

## 19. First Codex implementation prompt

> Read the complete project plan and create Milestone M0 only. Build a vendor-neutral Python dual-laser LPBF scheduling simulator. Do not add Materialise- or ALT-specific implementation beyond interfaces/stubs. Implement explicit models for Layer, VectorBlock, Laser, ScanField and ScheduleEntry; configurable block partitioning; single-laser timing baseline; static split; LPT; conflict-aware LPT; directional gas-flow exclusion; banded scheduling; deterministic validator and metrics; synthetic fixtures; tests; and a layer visualization tool. Hard constraints must never be violated to improve makespan. Keep process parameters opaque and referenced rather than inventing laser settings. Document every unresolved machine/process assumption in docs/unknowns.md. Run all tests and benchmark the supplied synthetic cases before finishing. Keep M0 intentionally bounded: after it passes, the next priority is the minimal Materialise vector-ownership experiment and ALT compatibility gate, not advanced scheduling.

---

## 20. Immediate inputs requested from the team

To prepare G1/G2 while Codex builds M0:

1. one job with a cube assigned only to L1;
2. the identical cube assigned only to L2;
3. a two-object L1/L2 job;
4. the current inner/outer L1/L2 job;
5. equivalent one-laser job if available;
6. every intermediate export produced by Magics and ALT;
7. screenshots or written notes showing where optic assignment occurs;
8. genuine Materialise BP SDK documentation/examples when obtained;
9. measured/official ALFA scan fields and gas-flow direction when available.

---

## 21. Final priority statement

The project will not wait for Materialise access before producing useful engineering software. It will also not spend months optimizing a scheduler before confirming the real integration boundary.

The sequence is therefore:

```text
M0 vendor-neutral simulator
        ↓
G1 Materialise per-vector optic ownership
        ↓
G2 ALT-compatible downstream representation
        ↓
real-layer offline validation
        ↓
conservative coupons
        ↓
qualification
        ↓
optimization toward 1.6–1.8×
```

This ordering is locked unless new evidence changes the feasibility of the Materialise/ALT boundary.
