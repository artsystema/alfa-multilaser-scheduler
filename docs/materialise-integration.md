# Materialise integration

## Gate G1

Prove the smallest useful case:

```text
one mesh → one layer → multiple vector blocks → at least one L1 block + one L2 block
```

Mesh topology must remain unchanged.

## Questions requiring genuine SDK evidence

1. At what stage are generated hatch/vector blocks accessible?
2. Can one part contain blocks assigned to different optics?
3. Can contours remain on one optic while core vectors are load-balanced?
4. Is Load Balancer callable/configurable from a customer BP?
5. Can external scheduling augment or replace built-in assignment?
6. How are scan fields, optic IDs, fog zones and bands represented?
7. What job representation leaves the BP?
8. Is an OEM-specific writer mandatory?

## Candidate paths

1. Built-in Materialise Load Balancer configured for ALFA.
2. Built-in Load Balancer plus custom ALFA constraints.
3. Vendor-neutral scheduler integrated through a custom BP.

Do not invent method names or adapter behavior before official SDK material is available.

