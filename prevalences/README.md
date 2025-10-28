# Prevalence Estimates for the Meta-analysis of PTSD Symptom Trajectories

R Markdown workflow to compute and visualise pooled **prevalence** estimates for the FORAS project on clinical PTSD symptom trajectories after traumatic events. 

## Purpose

- Computes pooled prevalence (random-effects), with common transformations for proportions
- Quantifies heterogeneity (τ², I²) and influence diagnostics
- Produces forest and funnel-type plots
- Writes summaries/plots to `prevalences/output/`


## Requirements

- R (≥ 4.2)
- R packages: `rmarkdown`, `tidyverse`, `metafor`, `meta`

Install essentials:

```r
install.packages(c("rmarkdown", "tidyverse", "metafor", "meta"))
```

---

## Inputs
- The dataset containing all extracted data (can be downloaded on DataVerseNL).
---

## How to run

From RStudio: open `prevalences/Prevalences_analysis.Rmd` and **Knit**.

From R:

```r
rmarkdown::render("prevalences/Prevalences_analysis.Rmd")
```

Outputs (HTML by default) and figures are saved under `prevalences/` (see the Rmd for exact paths).

---
