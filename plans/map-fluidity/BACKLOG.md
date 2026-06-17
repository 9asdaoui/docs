# Command Center Map — Backlog

Phases 1–5 are **shipped** (viewport bundle, camera, modes, LOD/clustering, live ops, MapEngine split). This file tracks **remaining** work only.

## Recently shipped (rendering)

- Imperative `MapBoundaryLayer` — polygons not remounted on unrelated React state
- Disabled viewport boundary refetch that morphed geometry on `zoomend` (`useMapBoundaries` off in Global Map)
- Solid region/sector strokes in view mode (no dashed SVG during zoom)
- Hover-only boundary labels (no sticky tooltips); labels hidden below zoom 9
- Dark `#0c0c0e` map canvas background to reduce tile seam flash

---

## 1. Leaflet rendering polish

| Item | Status | Notes |
|------|--------|--------|
| Cluster animation + zoom-12 crossfade | Pending | `leaflet.markercluster` `animate: true`; soften server ↔ client cluster swap |
| Freeze polygons during pan/zoom | Partial | `MapBoundaryLayer` defers sync until `moveend` |
| Safe boundary LOD | Pending | Re-enable simplify-by-zoom **without** replacing geometry mid-session (full geom in memory, simplify render only) |
| Tile seam / blur mitigation | Partial | `edgeBufferTiles`, dark bg; optional `detectRetina`, more buffer tuning |

---

## 2. Scale (former Phase 6)

See also [phase-06-scale-optional.md](./phase-06-scale-optional.md).

### Backend

- [ ] `GET /tiles/{z}/{x}/{y}.mvt?layer=clients` (vector tiles)
- [ ] Entity change SSE: `{ type, id, geometry?, fields? }`
- [ ] `GET /logistics/map/bounds?layers=` — server-computed fit bounds

### Frontend

- [ ] 2k+ visible clients without main-thread jank (or document MVT path)
- [ ] Multi-user map patches without `refreshMapData` storms
- [ ] Worker offload for bbox filter / cluster index

**Trigger:** DOM marker count or bbox p95 latency exceeds Phase 4 targets.

---

## 3. MapLibre path (if Leaflet ceiling hit)

**Triggers:** boundaries still jank after polish, >2k DOM markers, or raster basemap blur unacceptable.

**Approach (hybrid spike, ~1–2 weeks):**

1. MapLibre GL + dark vector basemap in Command Center
2. Boundaries as GeoJSON / vector layers (smooth zoom transforms)
3. Points via symbol layers or hybrid with existing viewport API
4. Keep Leaflet + `RegionBoundaryDrawer` for edit/create until draw parity

**Reuse:** viewport bundle, SSE positions, mission overlay APIs — renderer swap only.

Detail: [maplibre-spike.md](./maplibre-spike.md)

---

## Rules (unchanged)

- Additive APIs; optional query params on shared routes
- Auth: `view_logistics_tab` or `manage_logistics`
- Do not break `MOBILE_API_CONTRACT_V2.md`
