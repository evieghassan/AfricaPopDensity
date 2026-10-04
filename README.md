# Population Change in Ethiopia, Kenya & Uganda (2015–2025)

Quarto report for **Geographic Data Science** assignment (student ID 201730962).  
Analyses gridded population data to map density and decadal change across three East African countries.

## Research Questions

1. What are the spatial patterns of population density in Ethiopia, Kenya, and Uganda in 2025?
2. How has population changed between 2015 and 2025 at the 1 km² scale?
3. What geographic and urban factors explain the observed patterns?

## Data Sources

| Dataset | Resolution | Source | Notes |
|---------|------------|--------|-------|
| Global Population 2015 | 1 km (≈30 arc-sec) | WorldPop / Global Human Settlement Layer | `global_pop_2015_CN_1km_R2025A_UA_v1.tif` |
| Global Population 2025 | 1 km | WorldPop (constrained, UN-adjusted) | `global_pop_2025_CN_1km_R2025A_UA_v1.tif` |
| Admin 0 boundaries | Vector | HDX / OCHA | National boundaries |
| Admin 1 boundaries | Vector | HDX / OCHA | First-level subnational units |

> **Data not included in repo** — download the two WorldPop GeoTIFFs and boundary shapefiles from [WorldPop](https://www.worldpop.org/) and [HDX](https://data.humdata.org/), then place in the project root (or update paths in the `.qmd`).

## Methodology

```
1. Load rasters (terra::rast) and boundaries (sf::read_sf)
2. Filter Admin 0/1 to Ethiopia, Kenya, Uganda
3. Crop & mask both rasters to Admin 1 extent
4. Resample 2015 raster to 2025 grid (bilinear) for pixel-wise comparability
5. Population change = pop2025 − pop2015_resampled
6. Zonal statistics: mean 2025 population per Admin 1 unit (exactextractr)
7. Classify change: No Change (−5 to 5), Growth (5 to 100), High Growth (>100)
8. Visualise with tmap (quantile for density; categorical for change)
```

**CRS**: WGS84 (EPSG:4326) — native CRS of both raster and vector sources; globally standardised (True, 2004).

## Key Findings

- **Density** — Highest mean densities align with capital cities (Addis Ababa, Nairobi, Kampala) and fertile highlands; arid lowlands (e.g., NE Kenya, Somali Region Ethiopia) remain sparse.
- **Drivers** — Urban economic pull + agricultural suitability explain the skew; quantile breaks used because distribution is heavily right-skewed.
- **Change 2015–2025** — No grid cells show net decline; categories reduced to three (Neutral / Growth / High Growth) after Decline class returned zero cells.
- **High Growth** clusters in peri-urban zones and along transport corridors — consistent with rapid urbanisation and internal migration literature (Selod & Shilpi, 2021).

## Outputs

| Figure | Description |
|--------|-------------|
| **Map 1** | Mean population density (people/km²) per Admin 1 unit, 2025 — quantile (6 classes), RdYlGn palette |
| **Map 2** | Categorical population change 2015–2025 — Neutral (orange), Growth (light green), High Growth (dark green); Admin 0 borders overlaid |

## Reproducibility

### Requirements

- R ≥ 4.2
- Quarto ≥ 1.4
- Packages:
  ```r
  install.packages(c("terra", "sf", "tmap", "dplyr", "exactextractr", "raster"))
  ```

### Render

```bash
quarto render AfricaPop.qmd
```

Produces `AfricaPop.html` (self-contained, with embedded maps).

### Paths

The script uses absolute Windows paths. For portability, either:
- Keep data in the same folder and use relative paths (`"global_pop_2015_..."`), or
- Set an RStudio project and use `here::here("data/...")`.

## Project Structure

```
GDS Assignment/
├── AfricaPop.qmd                      # Main Quarto document
├── global_pop_2015_CN_1km_R2025A_UA_v1.tif   # (add manually)
├── global_pop_2025_CN_1km_R2025A_UA_v1.tif   # (add manually)
├── result/
│   └── 1f88dd58-b415-4f97-a3ec-69872f9cbad8  # Admin 0 boundaries (add manually)
├── Boundaries_admin_level_1/          # Admin 1 boundaries (add manually)
├── README.md                          # This file
└── _freeze/                           # Auto-generated on render
```

## References

- Selod, H. & Shilpi, F. (2021) Rural-urban migration in developing countries. *Regional Science and Urban Economics*, 91, 103713.
- True, S.A. (2004) Planning the future of WGS84. *PLANS 2004 Symposium*, IEEE, pp. 639–648.
- WorldPop / Global Human Settlement Layer — constrained UN-adjusted population rasters.

## License

Academic coursework. Code © 2024 Evie Hassan (201730962).  
Data: WorldPop (CC-BY 4.0), HDX/OCHA boundaries (open).

## Contact

Evie Hassan — BA Geography, University of Liverpool  
[LinkedIn](https://www.linkedin.com/in/evie-hassan-4bab432a1/)