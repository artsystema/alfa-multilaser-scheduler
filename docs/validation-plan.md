# Validation plan

## Offline M0 validation

- deterministic unit and integration tests;
- independent schedule validator;
- full/partial scan-field fixtures;
- symmetric and directional conflict fixtures;
- infeasible concurrent fixture that requires idling;
- golden JSON/metrics outputs;
- visual review of assignment and concurrent execution;
- timing and speedup reports separated from recoating/whole-machine time.

## Real-layer offline validation

After G1/G2:

- import representative ALFA layers;
- validate scan reachability and configured interaction rules;
- calibrate timing against known jobs;
- compare static, LPT, conflict-aware and banded schedules;
- compare with Materialise Load Balancer output if available.

## Physical progression

1. established single-laser control;
2. existing two-mesh inner/outer control;
3. new block scheduler with conservative separation;
4. band optimization;
5. distance/plume optimization;
6. layer-to-layer boundary-shift qualification.

Keep qualified material parameters constant initially. Change scheduling/ownership only.

## Coupon concept

Include L1-dominant, mixed/handoff and L2-dominant regions alongside single-laser controls. Apply internal requirements for density/porosity, polished cross-sections, handoff regions, dimensions, surface quality, hardness and mechanical testing where appropriate.

No valuable production part is used before conservative coupons pass the agreed criteria.

