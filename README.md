# Harvestmen assemblages reveal compositional shifts associated with riparian wetland degradation in a tropical dry forest-oil palm landscape
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.20705739.svg)](https://doi.org/10.5281/zenodo.20705739)

This repository contains the data and analysis code supporting the manuscript:

> Ahumada-C., D., Estevez-Vargas, A. M., Fajardo-Herrera, R. J., Castillo-Navarro, H.
> **Harvestmen assemblages (Arachnida: Opiliones) reveal compositional shifts associated
> with riparian wetland degradation in a tropical dry forest-oil palm landscape.**
> *Ecological Indicators* (in preparation).

The study evaluates how the replacement of riparian tropical dry forest (TDF) by
oil palm monoculture (OPM) affects harvestmen assemblages in a Caribbean
dry-forest landscape of northern Colombia (Finca Rionilo Ecolodge Tours,
Mahates, Bolívar Department). Sampling was conducted across six permanent
riparian plots (three TDF, three OPM) over four campaigns coinciding with the
2023-2024 El Niño event.

## Repository structure

```
harvestmen-TDF-OPM-Colombia/
├── README.md
├── LICENSE                  # MIT license (code)
├── CITATION.cff
├── .gitignore
├── data/
│   ├── LICENSE-DATA.md      # CC-BY 4.0 license (data)
│   └── processed/
│       └── 01_datos_opiliones.RData
├── analysis/
│   ├── opiliones_analysis.Rmd
│   └── preamble.tex          # LaTeX definitions for PDF rendering
├── figures/
│   ├── main/                 # Main-text figures (generated on knit)
│   └── supplementary/        # Supplementary figures, incl. DHARMa diagnostics
└── outputs/                   # Compiled report (PDF) and exported tables
```

Generated figures and the rendered PDF report are not tracked in version
control (see `.gitignore`); they are reproduced by knitting
`analysis/opiliones_analysis.Rmd`.

## Data

### `data/processed/01_datos_opiliones.RData`

A single R data file containing all pre-processed objects used in the
analysis. Raw field and satellite data were cleaned, merged, and structured
into the objects below prior to deposit; this is the dataset used directly
by `analysis/opiliones_analysis.Rmd`.

| Object | Dimensions | Description |
|---|---|---|
| `df_master` | 24 x 31 | Master table integrating field, biological, and satellite data; one row per plot x sampling date |
| `sp_matrix` | 24 x 10 | Community matrix (sites x OTUs), used directly with `vegan` |
| `metadata` | 24 x 20 | Explanatory variables aligned with `sp_matrix` for ordination/PERMANOVA |
| `ambiental` | 24 x 14 | Field microclimate variables |
| `satelital` | 24 x 14 | Satellite-derived variables (Google Earth Engine) |
| `bio_largo` | long format | Biological data in long (tidy) format, one row per OTU x sample |
| `bio_ancho` | 24 x 15 | Biological data in wide format (OTU abundances per sample) |
| `sp_cols` | length 10 | Full species/OTU names |
| `sp_cod_cols` | length 10 | Short OTU codes (see table below) |
| `sp_ref` | 10 x n | Reference table linking OTU codes to full taxonomic names |
| `sp_abundantes` | length 6 | OTU codes with >= 10 individuals (used for species-level GLMMs) |

### Key variables in `df_master` / `metadata`

| Variable | Description |
|---|---|
| `cobertura` | Habitat type: `BST` (tropical dry forest, TDF) or `PLM` (oil palm monoculture, OPM) |
| `mes` | Sampling campaign: `Nov2023`, `Dic2023`, `Ene2024`, `Jun2024` |
| `transecto_id` / `transecto` | Permanent plot identifier (block, 1-3 per habitat) |
| `riqueza` | Observed species richness per sampling unit (Hill q = 0) |
| `hill_q1`, `hill_q2` | Hill numbers q = 1 (Shannon) and q = 2 (Simpson) |
| `abundancia_total` | Total individuals recorded per sampling unit |
| `HR_media`, `TA_media`, `TS_media` | Mean relative humidity, air temperature, soil temperature (field microclimate) |
| `delta_T` | Thermal differential (T_air - T_soil) |
| `NDVI`, `EVI` | Vegetation indices (Sentinel-2) |
| `LST_C` | Land surface temperature, degrees C (MODIS) |
| `precip_15d_mm` | Cumulative precipitation, 15 days prior to sampling (CHIRPS) |
| `dist_agua_m` | Euclidean distance to nearest open-water pixel (Sentinel-2 NDWI) |

### OTU codes (`sp_cod_cols`)

| Code | Taxon |
|---|---|
| EUCQU | *Eucynorta quadripustulata* |
| HEVCR | *Hevelia crucis* |
| BARPI | *Barinas piragua* |
| SAMSP | Samoidae sp. |
| GAGNA | Gagrellinae sp. |
| STYSP | *Stygnomma* sp. |
| AGOSP | Agoristenidae sp. |
| COSSP | Cosmetidae sp. |
| ICASP | Icaleptidae sp. |
| ZALSP | Zalmoxidae sp. |

Variable and habitat codes retain their original short-form labels (`BST`,
`PLM`, OTU codes, etc.) as used throughout the analysis code. In the
manuscript, `BST` corresponds to **TDF** (tropical dry forest) and `PLM`
corresponds to **OPM** (oil palm monoculture).

## Analysis code

### `analysis/opiliones_analysis.Rmd`

A single, self-contained R Markdown document that reproduces all statistical
analyses, tables, and figures reported in the manuscript and its
Supplementary Material, organised into four analytical blocks:

1. **Alpha diversity and temporal variation** — Hill numbers, rarefaction/
   extrapolation (iNEXT), GLMMs (glmmTMB).
2. **Community composition and structure** — NMDS, PERMANOVA (block-restricted
   and unrestricted), PERMDISP, mvabund, IndVal, SIMPER.
3. **Environmental drivers of assemblage structure** — species-level GLMMs
   with AICc-based forward selection (MuMIn).
4. **Beta-diversity partitioning and modelling** — Jaccard turnover/nestedness
   partitioning (betapart), Beta GLMMs.

Knitting the document regenerates all main-text figures into
`figures/main/` and all supplementary figures (including DHARMa residual
diagnostics for every model) into `figures/supplementary/`, each as both
300 dpi TIFF and vector PDF.

### Requirements

- R >= 4.4.0
- Packages: `tidyverse`, `readxl`, `vegan`, `glmmTMB`, `DHARMa`, `iNEXT`,
  `betapart`, `mvabund`, `indicspecies`, `MuMIn`, `ggeffects`, `mgcv`,
  `emmeans`, `patchwork`, `knitr`, `kableExtra`
- A LaTeX distribution (e.g., TinyTeX) for PDF rendering via `xelatex`,
  including the `tcolorbox` package (used for the in-text methods summary
  boxes). If using TinyTeX: `tinytex::tlmgr_install("tcolorbox")`.

```r
install.packages(c(
  "tidyverse", "readxl", "vegan", "glmmTMB", "DHARMa", "iNEXT",
  "betapart", "mvabund", "indicspecies", "MuMIn", "ggeffects", "mgcv",
  "emmeans", "patchwork", "knitr", "kableExtra"
))
```

### Code conventions

All reader-facing text (figure captions, table headers, axis labels, legends,
and code comments) is in English. Internal data columns and a small number of
helper-function names retain their original short identifiers (e.g.,
`cobertura`, `mes`, `transecto_id`, `riqueza`, `hill_q1`, `glmm_especie`,
`ajustar_glmm`), matching the variable names documented in the data dictionary
above. These identifiers are not shown to readers of the rendered report; they
are preserved as-is to avoid introducing inconsistencies between the code and
the underlying `.RData` object during translation. `cobertura` takes values
`"BST"` (TDF) and `"PLM"` (OPM); `mes` takes values `"Nov2023"`, `"Dic2023"`
(Dec 2023), `"Ene2024"` (Jan 2024), and `"Jun2024"`.

### Reproducing the analysis

```r
# From the repository root
rmarkdown::render("analysis/opiliones_analysis.Rmd")
```

All random processes (bootstrap resampling, permutations, NMDS starts) use
`set.seed(42)` for reproducibility. The R session info, including exact
package versions used to generate the reported results, is printed at the
end of the rendered document.

## Correspondence to manuscript figures and tables

| Repository output | Manuscript element |
|---|---|
| `Fig1_rarefaccion` | Figure 2 |
| `Fig3_GLMM_riqueza_emmeans` | Figure 3 |
| `Fig4_NMDS` | Figure 4 |
| `Fig6B_relaciones_funcionales` | Figure 5 |
| `Fig7_beta_violines` | Figure 6 |
| `Fig2_perfil_Hill` | Supplementary Figure S9 |
| `Fig5_betadisper` | Supplementary Figure S11 |
| `Fig6_efectos_marginales` | Supplementary figure (species-level marginal means) |
| `Fig8_emmeans_beta` | Supplementary Figure S12 |
| `S1_DHARMa_*`, `S_DHARMa_*` | Supplementary Figures S1-S8 (DHARMa diagnostics) |

## Citation

If you use this code or data, please cite both the software/data record and
the associated article. See [`CITATION.cff`](CITATION.cff) for full citation
metadata, and [`data/LICENSE-DATA.md`](data/LICENSE-DATA.md) for the data
citation and license.

## License

- **Code** (`analysis/`): [MIT License](LICENSE)
- **Data** (`data/processed/`): [CC-BY 4.0](data/LICENSE-DATA.md)

## Acknowledgements

We thank the owners of Finca Rionilo Ecolodge Tours for granting permission to
conduct this research, and all field collaborators acknowledged in the
associated manuscript.
