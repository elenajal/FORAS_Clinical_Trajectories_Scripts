# Moderator Analysis of Prevalences of Clinical PTSD Symptom Trajectories

R Markdown workflow to run **moderator / meta‑regression analyses** for the FORAS meta‑analysis of clinical PTSD symptom trajectories. 

## Functions
- Loads cleaned study‑level data (events, totals, study descriptors)
- Defines moderators (design/sample features, follow‑up, quality, etc.)
- Fits random‑effects meta‑regressions (and subgroup analyses when relevant)
- Reports effect sizes, CIs, heterogeneity (τ², I²), model fit
- Produces forest/coef plots and summary tables to `moderators/output/`

## Requirements
- R (≥ 4.2)
- Suggested packages: `rmarkdown`, `tidyverse`, `metafor`, `meta`, `broom`, `ggplot2`

Install essentials:
```r
install.packages(c("rmarkdown", "tidyverse", "metafor", "meta", "broom", "ggplot2"))
```

## Inputs
- The extracted data of all analysis-relevant variables (can be downloaded on DataVerseNL).

## How to run
From RStudio: open `moderators/Moderator_analysis.Rmd` → **Knit**.  
From R:
```r
rmarkdown::render("moderators/Moderator_analysis.Rmd")
```
Outputs (HTML by default) and figures/tables are written under `moderators/`.

## License / Citation
- License: MIT (see repository root).  
- Please cite the FORAS meta‑analysis project/preprint when using these materials.
