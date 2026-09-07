# ALFA / ALT integration

Treat ALT as a black box until supported artifacts establish the interface boundary.

## Gate G2

Pass if Materialise multi-optic vector ownership can reach the current workflow through:

1. an existing ALT importer/postprocessor; or
2. a small documented compatibility converter.

Pause and reassess if ALT accepts optic ownership only at mesh/object level or Materialise requires an inaccessible OEM writer.

## Minimal differential jobs

- **A:** one cube assigned to L1.
- **B:** identical cube assigned to L2.
- **C:** two cubes, one assigned to each laser.
- **D:** current inner/outer split assigned to L1/L2.
- **E, if available:** single-laser equivalent of D.

Preserve every intermediate file and the exact software/settings used.

Upload samples to [datasets/alfa_samples/incoming](../datasets/alfa_samples/incoming/). Follow the [sample preparation and Codex handling instructions](../datasets/alfa_samples/README.md) and attach the [metadata template](../datasets/alfa_samples/metadata-template.md) for each sample.

Inspect laser ownership, exposure coverage, parameter associations, per-laser order and cross-laser synchronization separately. Laser IDs surviving an export alone do not establish that ALT preserves a proposed concurrent schedule.

## Permitted analysis

Comparison tooling may report:

- size and cryptographic hashes;
- headers and readable strings;
- changed byte ranges;
- discoverable object/layer/block structures;
- confirmed or likely optic identifiers.

It must not patch production files based on guessed offsets. Findings must be labeled `CONFIRMED`, `LIKELY` or `UNKNOWN`.

