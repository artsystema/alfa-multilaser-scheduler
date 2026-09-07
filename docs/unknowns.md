# Knowns, unknowns and unqualified values

Never silently promote an item from `UNKNOWN` to `KNOWN`. Add evidence and date the change.

## Known

- The target ALFA workflow has at least two optics.
- The current supported workflow can assign separate meshes to separate lasers.
- Laser identity therefore survives at least the existing mesh-level workflow.
- The current inner/outer split is a useful benchmark but introduces artificial geometric/process boundaries.
- The scheduler must split exposure workload rather than source CAD geometry.
- ALT remains responsible for actual machine-side execution.

## Unknown — blocks integration

- Exact Materialise BP SDK language, API surface and runtime/licensing contract.
- The stage at which a customer BP can access generated hatch/vector blocks.
- Whether blocks from one part can receive different optic IDs.
- Whether built-in Load Balancer is callable/configurable from a customer BP.
- Whether a generic multi-optic output can remain upstream of the existing ALT postprocessor/importer.
- Whether a custom machine writer is mandatory.
- Exact ALT optic-ID granularity: mesh, region, layer, vector block or vector.
- Exact ALT-compatible input/job representation.

## Unknown — machine/process

- Validated ALFA L1/L2 scan fields and overlap geometry.
- Qualified simultaneous-exposure separation.
- Gas-flow direction at the build plane.
- Qualified plume/fog-zone geometry.
- Scanner jump speed and settle/transition overhead.
- Qualified block size and band width.
- Qualified contour ownership policy beyond the conservative v1 rule.
- Qualified layer-to-layer ownership-boundary shift.

## Conservative software defaults

Defaults used for synthetic simulation are not production values. Mandatory production values remain `null` until evidence or qualification exists, and production export must refuse to proceed.

## Evidence needed

- Materialise BP SDK documentation, examples and licensing/runtime notes.
- Official or measured scan-field geometry.
- Confirmed gas-flow direction and process guidance.
- Minimal Magics/ALT artifacts listed in `docs/alfa-integration.md`.

