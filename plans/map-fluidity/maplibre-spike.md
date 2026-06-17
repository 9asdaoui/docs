# MapLibre Spike — Command Center

## Goal

Prove fluid pan/zoom (vector basemap + vector boundaries) without rewriting logistics APIs.

## Scope

| Layer | MapLibre | Keep on Leaflet (initially) |
|-------|----------|------------------------------|
| Basemap | Vector dark style (MapTiler / Protomaps / self-hosted) | — |
| Region/sector boundaries | GeoJSON source + line layers | — |
| Clients/vehicles/depots | Symbol layers fed by viewport bundle | — |
| Region create/edit draw | — | `RegionBoundaryDrawer` |
| Sector vertex edit | — | Existing edit flow |

## Steps

1. Add `maplibre-gl` + `react-map-gl` (or imperative MapLibre in `MapEngine` sibling)
2. Feature flag: `VITE_MAP_RENDERER=maplibre` vs `leaflet`
3. Load same DTOs from `GET /logistics/map/viewport`
4. Compare: zoom FPS, boundary stability, marker count ceiling
5. If pass → incremental migration; if fail → invest in MVT (backlog §2)

## Non-goals (spike)

- Full parity for all edit modes
- Mobile app changes
- Replacing mission route polylines day one

## Success criteria

- Region outlines stay visually locked during zoom (no blob/drift)
- Pan/zoom feels comparable to Google Maps at regional zoom
- No regression on auth, depot scope, or layer filters
