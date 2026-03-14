# DashCMCoimbra — Project Reference

## Project Overview

A single-page D3.js v7 dashboard about **Município de Coimbra** (Portugal), built entirely from open public data. Delivered as one self-contained HTML file with all data embedded — no build step, no server required. Open `index.html` directly in a browser.

---

## File Structure

```
DashCMCoimbra/
└── index.html      # Entire dashboard: HTML + CSS + embedded JS data + D3 charts (~130 KB)
```

---

## Current Dashboard Sections

### Section 1 — Statistical Data
| Chart | Source | Notes |
|-------|--------|-------|
| Population Over Time (line+area) | INE census + Wikidata SPARQL (live) | 1864–2021 |
| Age Structure (horizontal bars) | INE Census 2021 | 6 age groups |
| Employment by Sector (donut) | INE 2021 | Services/Industry/Agriculture |
| Top Parishes by Population (bars) | INE Census 2021 | Top 10 of 31 parishes |
| Climate — Temp & Precipitation (combo) | Open-Meteo Historical API (live) | Falls back to IPMA 30-yr normals |

### Section 2 — Urban Infrastructure (dados.gov.pt)
| Chart / Component | Source | Notes |
|---|---|---|
| Interactive SVG Map | dados.gov.pt (3 datasets) | Zoom/pan, 7 toggleable layers |
| Kiosk Status chart (bars) | Edificações em Espaço Público | Active/Inactive, Municipal/Private |
| Urban Infrastructure Overview (log bars) | All datasets combined | Count by category |
| Public Buildings grid (cards) | Edificações em Espaço Público | 10 facilities, Parque Verde |

---

## Data Sources

### Live APIs (fetched at runtime, CORS-friendly, no auth)
| API | URL | Data |
|-----|-----|------|
| Open-Meteo Historical | `https://archive-api.open-meteo.com/v1/archive` | Daily climate 2020–2022, aggregated to monthly |
| Wikidata SPARQL | `https://query.wikidata.org/sparql` | Population history enrichment (Q45412) |

### Embedded Data (extracted from dados.gov.pt ZIPs, Oct 2024)
| Dataset | URL | Contents | Format |
|---------|-----|----------|--------|
| Paragens Autocarros | https://dados.gov.pt/pt/datasets/paragens-autocarros/ | 1,511 SMTUC bus stops (lat/lon/name) | ZIP → TXT/CSV |
| Edificações em Espaço Público | https://dados.gov.pt/pt/datasets/edificacoes-em-espaco-publico/ | 10 buildings, 13 toilets, 24 kiosks | ZIP → CSV + XLSX |
| Mobilidade Suave | https://dados.gov.pt/pt/datasets/mobilidade-suave/ | 86 bike spots, 112 scooter hotspots, 4 zone polygons | ZIP → KML |

### Embedded Statistical Data (INE / PORDATA)
- Population census 1864–2021 (16 data points)
- Age groups 2021, Employment by sector 2021, Top 10 parishes 2021
- IPMA 30-year climate normals (fallback)

---

## Map Details

- **Library:** Leaflet 1.9.4 + OpenStreetMap tiles (`https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png`)
- **Initial view:** center `[40.207, -8.445]`, zoom `13`
- **Map instance:** stored at `window._map`; `drawMap()` destroys and recreates on re-call
- **Performance:** bus stops use `L.canvas()` renderer (1,511 SVG-less markers); all others use default SVG renderer
- **Coordinate conventions in JS arrays:**
  - `busStops`: `[lat, lon, name]` → Leaflet `[lat, lon]` ✅ direct
  - `bikeSpots`, `scooterHotspots`: `[lat, lon]` → Leaflet `[lat, lon]` ✅ direct
  - `mobilityZones`: `{name: [[[lon, lat], ...]]}` → **must reverse** to `[lat, lon]` for Leaflet: `pts.map(([lon,lat])=>[lat,lon])`
  - `buildings`, `toilets`, `kiosks`: objects `.lat`, `.lon` → `[d.lat, d.lon]` ✅
- **Layers (7 toggleable):** Mobility Zones (polygons), Bus Stops, Bike Parking, Scooter Hotspots, Public Toilets, Kiosks, Public Buildings
- **Layer toggle:** custom buttons above map, `map.removeLayer()` / `layerGroup.addTo(map)`
- **Reset view:** button below map → `map.setView([40.207,-8.445],13)`
- **Popups:** `bindPopup()` on all markers, styled with light-theme CSS overrides

---

## Theme: Current (Light) Color Palette

| Token | Value | Use |
|-------|-------|-----|
| `--bg-primary` | `#f1f5f9` | Page background |
| `--bg-card` | `#ffffff` | Card backgrounds |
| `--bg-card-alt` | `#f8fafc` | Building cards |
| `--accent` | `#b45309` | KPI values, header title (amber-700) |
| `--accent-chart` | `#d97706` | Population line, chart accents |
| `--text-primary` | `#0f172a` | Headings |
| `--text-secondary` | `#64748b` | Body/axis text |
| `--text-muted` | `#94a3b8` | Labels, subtitles |
| `--border` | `#e2e8f0` | Card borders |
| `--grid` | `#f1f5f9` | D3 chart grid lines (const GRID) |
| `--chart-indigo` | `#6366f1` / `#818cf8` | Bus stops, precipitation bars |
| `--chart-green` | `#15803d` / `#22c55e` | Bike spots, active status |
| `--chart-amber` | `#b45309` / `#f59e0b` | Scooter spots, buildings |
| `--chart-blue` | `#0369a1` / `#38bdf8` | Public toilets |
| `--chart-pink` | `#be185d` / `#f472b6` | Kiosks (active) |
| `--chart-orange` | `#ea580c` | Temperature line in climate chart |
| `--chart-gray` | `#6b7280` / `#d1d5db` | Inactive items |

---

## Data Extraction Process (for re-extraction)

If dados.gov.pt updates the data, re-extract with:

```bash
# 1. Download ZIPs
python3 -c "
import urllib.request, zipfile, io, os
datasets = {
    'paragens':     'https://dados.gov.pt/s/resources/paragens-1/20241022-155252/paragens.zip',
    'edificacoes':  'https://dados.gov.pt/s/resources/edificacoes-em-espaco-publico/20241022-154257/edificacoes-csv.zip',
    'sanitarias':   'https://dados.gov.pt/s/resources/edificacoes-em-espaco-publico/20241022-154523/instalacoes-sanitarias-publicas-csv.zip',
    'quiosques':    'https://dados.gov.pt/s/resources/edificacoes-em-espaco-publico/20241022-154922/quiosques-csv.zip',
    'mobilidade':   'https://dados.gov.pt/s/resources/mobilidade-suave/20241022-151633/kml.zip',
}
# ... unzip each to /tmp/coimbra_data/<name>/
"

# 2. Run the extraction script (outputs /tmp/coimbra_jsdata.js)
# See session history for full Python extraction script
```

CSV files use **semicolon delimiter** and **comma as decimal separator** for coordinates (Portuguese locale). The quiosques file is an XLSX, not CSV despite the folder name.

---

## Key Facts About Coimbra

- **Population:** 140,796 (2021 Census, INE)
- **Area:** 319.4 km²
- **Density:** 441 residents/km²
- **Parishes:** 31 (after 2013 reform)
- **University of Coimbra:** founded 1290, ~24,000 students
- **Wikidata entity:** Q45412
- **GPS center:** 40.2033°N, 8.4103°W
- **SMTUC:** Serviços Municipalizados de Transportes Urbanos de Coimbra (bus operator)
- **Municipality division (DICOFRE):** 060334 (historic center), 060318, 060336 (Parque Verde)

---

## Architecture Notes

- **Single HTML file** — everything embedded, no external assets except D3.js CDN
- **Render strategy:** Draw with embedded data immediately on load, then silently upgrade with live API data via `Promise.allSettled()`
- **No build tooling** — plain HTML/CSS/JS, edit directly
- **dados.gov.pt API:** Uses the `api/1/datasets/<slug>/` endpoint for resource listing (not the CKAN v3 API which returns 404)
- **CORS status:** Open-Meteo ✅, Wikidata SPARQL ✅, dados.gov.pt direct ZIP downloads ✅ (for browser fetch + JSZip if needed in future)
