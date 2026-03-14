# Município de Coimbra — Open Data Dashboard

Interactive D3.js + Leaflet dashboard for the **Município de Coimbra**, Portugal, built entirely from open public data.

🗺️ **[Live Dashboard](https://indradashdemo.github.io/MunicipioCoimbra/)**

---

## Features

- **Population history** (1864–2021) — INE census + Wikidata SPARQL live enrichment
- **Age structure & Employment** — INE Census 2021
- **Climate** — Monthly temperature & precipitation via Open-Meteo Historical API
- **Interactive OSM Map** — OpenStreetMap tiles with 7 toggleable layers:
  - 1,511 SMTUC bus stops
  - 86 bicycle parking stations
  - 112 scooter hotspots
  - 4 soft-mobility zones (Verde / Azul / Vermelha / Preta)
  - 24 kiosks, 13 public toilets, 10 public buildings

## Data Sources

| Source | Data |
|--------|------|
| [INE — Statistics Portugal](https://www.ine.pt) | Census, demographics, employment |
| [Open-Meteo](https://open-meteo.com) | Historical climate data |
| [Wikidata](https://www.wikidata.org/wiki/Q45412) | Population history |
| [dados.gov.pt — Paragens Autocarros](https://dados.gov.pt/pt/datasets/paragens-autocarros/) | SMTUC bus stops |
| [dados.gov.pt — Edificações em Espaço Público](https://dados.gov.pt/pt/datasets/edificacoes-em-espaco-publico/) | Buildings, toilets, kiosks |
| [dados.gov.pt — Mobilidade Suave](https://dados.gov.pt/pt/datasets/mobilidade-suave/) | Bike spots, scooter hotspots, zones |
| [OpenStreetMap](https://www.openstreetmap.org) | Map tiles |

## Tech Stack

- [D3.js v7](https://d3js.org) — statistical charts
- [Leaflet 1.9](https://leafletjs.com) — interactive map
- Single self-contained HTML file — no build step, no server required

## License

Data: open data under respective source licenses.
Dashboard code: MIT.
