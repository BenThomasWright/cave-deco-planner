# Cave Deco Planner

A browser-based cave diving decompression planner built as a self-contained HTML file (no build step, no server needed — open `index.html` in any browser).

## What it is

A dive planning tool implementing Bühlmann ZHL-16C with Gradient Factors. Intended for planning purposes only — not a substitute for certified training or approved deco software.

## Tech stack

- **Single file**: `index.html` — all logic, styles, and markup in one file
- **React 18** via CDN (cdnjs), **Babel standalone** for JSX — no build tooling
- **Pure CSS** with custom properties for theming (dark-first, light mode supported)
- **Canvas API** for the interactive profile drawing
- All decompression math is plain JS — no external deco library

## Architecture

The file is structured in clear sections (search for the `═══` comment dividers):

1. **Bühlmann ZHL-16C constants** — N2/He half-times, a and b coefficients (ZHL-16C values)
2. **Physics helpers** — `ambBar()`, `barToM()`, `alveolarPP()` for OC and CCR
3. **Tissue math** — `initTissues()`, `stepTissues()`, `ceilingBar()`, GF interpolation (`gfAt()`)
4. **CNS tracking** — NOAA O2 exposure table, `cnsRatePerMin()`
5. **Simulation engine** — `runSimulation()` converts the dive profile to tissue loadings using 10-second steps
6. **Deco calculator** — `buildDecoSchedule()` iterates 3m stops until the GF-modified ceiling clears
7. **React components** — `ProfileCanvas`, `OCPanel`, `CCRPanel`, `WaypointsTable`, `ResultsPanel`, `App`

## Key design decisions

- **10-second time steps** for the bottom phase (fast enough for a browser, accurate enough for planning)
- **GF line**: GF Low applied at the deepest ceiling (found using `gfLow` alone first), GF High at surface, linearly interpolated at intermediate depths
- **CCR inert gas**: `ppO2 = min(setpoint, fO2_diluent * (P_amb - P_H2O))` — setpoint maintained unless too shallow; inert PP split by diluent N2:He ratio
- **Cave profile mode**: waypoints are (distance, depth) with per-segment swim/scooter speed; optional mirror-return toggle (default on)
- **Time profile mode**: waypoints are (time, depth) drawn directly — full profile, no mirroring
- **Gas drops**: deco gases (OC) and bailout (CCR) each have a `dropDist` — distance from entrance where the bottle is left; picked up on the return leg

## Artifact

Published at: https://claude.ai/artifact/Bm9yvvpXPBUKmLWykXaVCQ

## Known gaps / next steps

- [ ] Gas consumption (RMV × time × P_amb → surface litres per cylinder)
- [ ] Per-compartment tissue loading bar chart at TD
- [ ] CCR bailout worst-case: recalculate deco as OC from the bailout drop point
- [ ] Dive profile results chart (depth vs time with deco stops visualised)
- [ ] OC ppO₂ warning if descent gas exceeds MOD
- [ ] Persist settings to localStorage between sessions
- [ ] Export/share dive plan as text or PDF

## Running locally

```bash
open index.html        # macOS
# or just double-click index.html in Finder
```

No server, no npm, no dependencies to install.
