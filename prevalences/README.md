# Prevalence Estimates for the Meta-analysis of Clinical PTSD Symptom Trajectories

`Prevalences_analyses_clinical.Rmd` is an R Markdown analysis pipeline that estimates the pooled prevalence of each PTSD symptom trajectory (Decreasing, Partially Decreasing, Persistent, Relapsing) across studies, using Bayesian random-intercept generalized linear mixed models fit with `brms`, together with a battery of sensitivity analyses.

## What this report does

1. **Loads and cleans** a study-level extraction spreadsheet, forcing character columns to UTF-8 and selecting the columns needed for prevalence estimation and study descriptives.
2. **Describes the data**, reporting the number of studies/cohorts, total sample size, and a per-study overview table (sample size, mean age, % women, trauma type, number of assessment time points, treatment type/length, number of trajectories found).
3. **Fits a Bayesian random-intercept binomial GLMM** (`fit_bglmm_prev()`) for the pooled prevalence of each trajectory:
   - Model: `n_events | trials(n_total) ~ 1 + (1 | Study)`, binomial family with logit link
   - Weakly informative priors: `normal(0, 1.5)` on the intercept, `student_t(3, 0, 1)` on the between-study SD
   - Returns the pooled prevalence (posterior mean of `plogis(intercept)`), 95% credible interval, logit-scale estimate, between-study SD (τ), and max R-hat
4. **Runs four sensitivity analyses per trajectory**, alongside the main analysis:
   - **Sensitivity 1:** exclude studies reporting ≥90% prevalence for that trajectory
   - **Sensitivity 2:** restrict to studies with sample size > 999
   - **Sensitivity 3:** exclude "influential" studies identified via Bayesian Leave-One-Out cross-validation (`loo`), where a Pareto *k* diagnostic > 0.7 flags a study as overly influential on the pooled estimate
   - **Sensitivity 4 (conservative estimate):** rebuild the dataset so that *every* study with a usable sample size is retained, coding studies that did not identify the given trajectory as contributing **zero events** rather than being excluded — this yields a lower-bound prevalence estimate to complement the main analysis's upper-bound estimate
5. **Builds two summary tables for publication**:
   - A **main prevalence summary table**, showing *k* (number of studies), N (%), the "relative" (upper-bound, main-analysis) prevalence and CrI, the "conservative" (lower-bound, zero-imputed) prevalence and CrI, logit estimate, τ, and max R-hat per trajectory — exported as PNG and RTF.
   - A **sensitivity analyses summary table**, consolidating the main analysis and all four sensitivity variants across all trajectories into one table (grouped by trajectory), with footnotes listing which studies were excluded as influential in each Pareto-*k* sensitivity step — also exported as PNG and RTF.

## Requirements

### R packages
```r
install.packages(c("readxl", "readr", "dplyr", "gt", "purrr", "tidyr",
                    "loo", "posterior", "bayesplot", "knitr", "kableExtra"))
```
`brms` additionally requires a working Stan backend (e.g. `rstan` or `cmdstanr`):
```r
install.packages("brms")
# if using cmdstanr:
install.packages("cmdstanr", repos = c("https://mc-stan.org/r-packages/", getOption("repos")))
cmdstanr::install_cmdstan()
```

### Data
The report expects a data extraction file at:
```
../data.xlsx
```
relative to the location of the `.Rmd` file — i.e., the working directory is set to the document's own folder

## Sampling settings

The default GLMM sampling settings (`fit_bglmm_prev()` defaults) are 4 chains, 4,000 iterations (1,000 warmup), `adapt_delta = 0.95`, and a fixed seed (`seed = 123`) for reproducibility. A minimum of 3 usable studies (`min_k = 3`) is required to attempt the main model; sensitivity analyses require at least 5 (`min_k = 5`). Analyses based on 5 or fewer studies are also dropped from the final sensitivity summary table, since random-effects estimates are unstable with very few clusters.

## How to run

Open the `.Rmd` file in RStudio and knit it, or run from the command line:
```r
rmarkdown::render("prevalences_analyses.Rmd")
```
Supported output formats (configured in the YAML header): HTML (with floating table of contents and code folding), Word, and PDF.

Knitting creates a `Prevalences Tables/` output folder (relative to the working directory) containing:
- `prevalences-summary-table.png` / `.rtf`
- `prevalences-sensitivity-table.png` / `.rtf`

## Output

For each trajectory (Decreasing, Partially Decreasing, Persistent, Relapsing), the knitted report includes:
- Descriptive counts (total individuals in the trajectory, number of unique contributing studies)
- The main pooled prevalence estimate (posterior mean, 95% CrI, logit estimate, τ, max R-hat)
- Four sensitivity analyses (extreme-prevalence exclusion, large-sample-only, influential-study exclusion via Pareto *k*, and the zero-imputed conservative estimate)

The report closes with two publication-ready `gt` tables: the cross-trajectory prevalence summary (upper- vs. lower-bound estimates) and the full sensitivity-analysis summary with footnoted exclusions.

## Notes / caveats

- This file estimates **unconditional pooled prevalences** only; it does not model moderators of trajectory prevalence (see the companion moderator-analysis `.Rmd` for that).
- Max R-hat is reported per model as a convergence check; values ≤ 1.01 are generally taken to indicate good chain mixing.
- The Pareto-*k* threshold of 0.7 for flagging influential studies follows common `loo` package guidance, not a universally fixed cutoff.
