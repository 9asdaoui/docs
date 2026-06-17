# Phase 6 — Scale (Optional)

**Goal:** Future-proof for 10k+ map entities and multi-user ops.

## Backend

- `GET /tiles/{z}/{x}/{y}.mvt?layer=clients` (vector tiles)
- Entity change SSE: `{ type, id, geometry?, fields? }`
- `GET /logistics/map/bounds?layers=` — server-computed fit bounds

## Frontend

- Evaluate MapLibre GL for point layers (keep Leaflet for polygons initially)
- Multi-user map patches without `refreshMapData` storms
- Worker offload for bbox filter / cluster index

## Acceptance

- [ ] 2k+ visible clients without main-thread jank (or tile path documented)
- [ ] Second user sees depot move within seconds

## Trigger

Implement when DOM marker count or bbox p95 latency exceeds Phase 4 targets.
