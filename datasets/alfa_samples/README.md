# ALFA printer job samples

Upload original exported jobs to [incoming](incoming/). This folder supports the first read-only investigation of the Materialise → ALT → ALFA workflow.

## What to upload

Start with one existing job that the printer accepts, preferably a working dual-laser job. The controlled comparison set below is useful when available; you do not need to print new builds for this analysis.

| Sample | Contents | Purpose |
| --- | --- | --- |
| A_cube_L1.zip | One small cube assigned to L1 | Baseline |
| B_cube_L2.zip | Exactly the same cube assigned to L2 | Isolate laser assignment |
| C_two_cubes_L1_L2.zip | Two cubes, one assigned to each laser | Observe both laser identities in one job |
| D_inner_outer_L1_L2.zip | Existing working inner/outer dual-laser job | Compare the current workaround |
| E_equivalent_single_laser.zip | Single-laser equivalent of D, if available | Compare existing workflows |
| A2_cube_L1_repeat.zip | A exported again with identical settings, if convenient | Identify changing timestamps, IDs or other export noise |

For A and B, keep geometry, coordinates, orientation, layer settings and process parameters identical. Change only the laser assignment and record any settings the software changes automatically.

ZIP the **complete export folder**, including companion files, retaining original filenames and directory structure inside the archive. Use the names above for the outer ZIP only. If the export is genuinely one file, upload that original file instead; retain its extension. Preserve available intermediate exports and the corresponding Magics project separately, with their workflow stage recorded.

Copy [metadata-template.md](metadata-template.md) next to each archive in `incoming/`, naming it after the sample, for example `A_cube_L1.md`. Enter `UNKNOWN` for unavailable information.

This is a public repository: use generic sample geometry and files you intend to share publicly. Keep proprietary production geometry, credentials and licensed SDK packages out of these uploads.

## Upload through GitHub

1. Open [incoming](incoming/).
2. Choose **Add file → Upload files**.
3. Upload the job archive and its metadata Markdown file.
4. Commit the upload to `main` with a descriptive message.

If GitHub rejects an archive because of its size, keep the complete original intact and record its filename, size and hash in the metadata. Arrange a suitable transfer separately rather than removing required companion files.

## Instructions for Codex after upload

Read [AGENTS.md](../../AGENTS.md) and [ALFA integration](../../docs/alfa-integration.md). This task is artifact inspection and documentation; it does not authorize printer execution or machine-file generation.

### 1. Inventory and preserve

- Treat files in `incoming/` as immutable evidence. Do not overwrite or normalize the originals.
- Inventory paths, byte sizes and SHA-256 hashes. Record the repository commit and associated metadata for provenance.
- Detect format/container signatures from the bytes; do not infer the format solely from the extension.
- Extract archives only into the ignored `datasets/alfa_samples/work/` directory. Reject escaping paths and unsafe links, bound extraction size, and never execute bundled programs, scripts or macros.
- Keep extracted copies and generated bulk output out of Git. Commit only reviewed analysis reports and intentionally selected small fixtures.

### 2. Inspect and compare

- Identify readable headers, strings, manifests and documented structures. Inspect container members before interpreting compressed binary differences.
- Compare A with A2 first when present, then A with B. Separate volatile export metadata from repeatable laser-related changes.
- Use C and D to investigate multiple laser identities within a single job. Record the objects, layers or vector blocks each candidate field appears to describe.
- For every candidate interpretation, cite the sample hash, member/path and byte offset or parsed field. Label findings `CONFIRMED`, `LIKELY` or `UNKNOWN`.
- Do not treat a changed byte as a confirmed laser ID without independent or controlled supporting evidence. Keep unknown units, coordinate transforms and timing semantics explicit.
- When a parser is justified, prefer documented structures; validate bounds, record lengths and counts. Add focused tests using shareable fixtures for any implemented parser.

### 3. Evaluate the actual integration boundary

Report separately whether evidence establishes:

| Question | Evidence needed |
| --- | --- |
| What enters ALT? | Actual export stage and accepted representation |
| Are exposures already generated? | Distinguish contours/slices from hatch/exposure vectors |
| Where is laser ownership stored? | Mesh, region, block or individual vector scope |
| Is geometry preserved? | Coverage, gaps, duplicate exposure and additional internal contours |
| Are parameter references preserved? | Associations with existing process settings |
| Is order preserved within each laser? | Explicit ordering and downstream interpretation |
| Can cross-laser dependencies survive? | Supported waits, barriers or other synchronization semantics |
| Does ALT regenerate or reorder exposures? | Documented behavior, supported preview or execution evidence |

A working two-mesh job establishes neither single-mesh vector ownership nor preservation of a proposed concurrent schedule. Simulation timestamps alone do not establish printer timing. Read-only file inspection may leave execution semantics unresolved.

### 4. Deliver findings and the next experiment

Create `docs/alfa-format-observations.md` containing:

- the sample inventory and provenance;
- reproducible inspection steps and tool versions;
- controlled differences and evidence references;
- confirmed findings, likely interpretations and unknowns;
- an assessment of whether further investigation of an existing importer or documented thin converter is justified;
- the smallest next sample or documentation request needed to resolve each blocker.

Update `docs/unknowns.md` and `docs/alfa-integration.md` when evidence changes them. Record consequential architecture decisions in `docs/decision-log.md`.

Do not patch machine jobs from guessed offsets, invent Materialise APIs, connect to the printer or claim process qualification. Keep ALT authoritative for hardware execution.

## Ready-to-use Codex task

> Read datasets/alfa_samples/README.md and the project rules. Inventory the uploaded files in datasets/alfa_samples/incoming, preserve their originals and SHA-256 hashes, inspect containers safely in the ignored work directory, and perform the available controlled comparisons. Write docs/alfa-format-observations.md with evidence-backed CONFIRMED / LIKELY / UNKNOWN findings about geometry, exposure vectors, process references, laser ownership, ordering and synchronization. Update relevant unknowns and integration documentation. Do not generate or modify printer jobs. If evidence is insufficient, identify the smallest next artifact or documentation needed.
