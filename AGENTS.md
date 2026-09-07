# Project rules

Read `docs/project-plan.md`, `docs/codex-m0.md`, and `docs/unknowns.md` before implementation.

This project schedules LPBF exposure vectors between multiple lasers, initially two optics on the ALFA-280.

## Scope and safety

- Split exposure workload, not source CAD geometry.
- Keep the vendor-neutral scheduler independent from Materialise and ALT.
- ALT remains authoritative for scanner execution, laser timing, safety interlocks and firmware.
- Do not implement direct printer control, raw galvo commands or safety-interlock logic.
- Never generate or patch production machine files from guessed binary fields.
- Keep material/process parameters opaque. Reference validated parameter sets; do not invent laser power, scan speed, hatch spacing or metallurgy recipes.
- Fail closed for production export when mandatory machine or safety values remain unknown or unqualified.

## External integrations

- Do not invent Materialise BP SDK APIs.
- Implement Materialise adapter methods only when supported by supplied official documentation or examples.
- Treat ALT as a black box until real artifacts establish a documented boundary.
- Classify integration findings as `CONFIRMED`, `LIKELY` or `UNKNOWN`.

## Scheduler correctness

- Hard constraints must never be converted into soft penalties.
- A laser may idle when no safe concurrent assignment exists.
- Every accepted schedule must pass validator checks before export.
- Every algorithm requires deterministic fixtures, unit tests and benchmark coverage.
- Identical input and configuration must produce identical output.

## Documentation discipline

- Record each new machine-specific assumption in `docs/unknowns.md`.
- Record consequential architecture choices in `docs/decision-log.md`.
- Update the relevant integration document when evidence changes a boundary.
- Never silently convert an unknown into a default or assumption.

## Current milestone

Implement M0 only, as defined in `docs/codex-m0.md`.

After M0 passes, prioritize the minimal Materialise vector-ownership experiment and ALT compatibility gates over advanced scheduling, reinforcement learning or detailed thermal modeling.

