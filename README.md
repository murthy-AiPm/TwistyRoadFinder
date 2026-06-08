# Sharp Turns — Curvy Road Trip Planner

A single-file web app that finds the **curviest driving route** between two or more places, instead of the fastest one. Built for motorcyclists, sports-car drivers, and anyone who'd rather take the scenic way.

Google Maps optimizes for time. Sharp Turns optimizes for fun roads, within a detour budget you control.

![Sharp Turns](src/assets/hero.png)

## Run it

No build step. Open the file in any modern browser:

```bash
# clone, then just open the HTML
open SharpTurns.html      # macOS
start SharpTurns.html     # Windows
xdg-open SharpTurns.html  # Linux
```

Or host it as a static page anywhere (GitHub Pages, Netlify, S3 — it's one HTML file).

## What it does

1. **Enter stops** — origin, destination, and up to 6 intermediate waypoints
2. **Pick how curvy you want it** — `Curviness` slider (10–100%) and a linked `Detour Budget` slider that caps extra travel time (+5% to +100% of baseline)
3. **Hit "Find Sharp Turns"** — the app computes a baseline route, scans nearby roads for curvature, and re-routes you through the twistiest options that still fit your time budget
4. **Get a result** — interactive map with baseline vs. curvy route, the top curvy segments ranked by score, a Google Maps deep link, and a GPX download for your GPS unit

Units toggle between miles and kilometers.

## How it works

| Step | Service |
|------|---------|
| Geocoding stop names → coordinates | [Nominatim](https://nominatim.openstreetmap.org/) (OpenStreetMap) |
| Baseline driving route | [OSRM](https://project-osrm.org/) public demo server |
| Road geometry around the corridor | [Overpass API](https://overpass-api.de/) (queries OSM `secondary` / `tertiary` / `unclassified` ways) |
| Map rendering | [Leaflet](https://leafletjs.com/) + CARTO dark basemap |

**Curvature scoring:** each road segment is scored by computing the circumradius of every consecutive triplet of points along the way. Tighter radii get higher weights (a 30 m radius hairpin counts for far more than a 500 m sweeper), and the score is normalized per kilometer so a short twisty road can out-rank a long straight one.

**Detour budget:** waypoints are only inserted if the new total trip time stays within `(1 + detour%) × baseline`. The two sliders are linked by default so cranking up curviness automatically opens up the budget, but you can unlink them.

## Stack

- Vanilla JS, no build tooling, no framework
- [Tailwind CSS](https://tailwindcss.com/) via CDN
- [Leaflet 1.9](https://leafletjs.com/) via CDN
- OSM-based open data only — no API keys required

## Limitations

- Uses **public, free** OSRM / Overpass / Nominatim endpoints. They are rate-limited and occasionally busy; the app tries 3 Overpass mirrors before giving up
- Routing is driving-only
- Curvature scoring only considers `secondary`, `tertiary`, and `unclassified` OSM roads — interstates and residential streets are excluded by design
- Max 8 stops per trip

## License

MIT
