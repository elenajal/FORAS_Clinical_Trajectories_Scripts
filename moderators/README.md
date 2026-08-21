# Moderator Analysis — Clinical Bayesian Univariate Models

`moderator_analysis.Rmd` is an R Markdown analysis pipeline that examines potential moderators of PTSD symptom trajectory prevalence (Decreasing, Partially Decreasing, Persistent, and Relapsing) using Bayesian generalized linear mixed models fit with `brms`.

## What this report does

1. **Loads and cleans** a study-level extraction spreadsheet, excluding one specified study (Schumm et al.) and building separate datasets per symptom trajectory.
2. **Describes the data**, producing summary tables of sample sizes and per-trajectory descriptive statistics.
3. **Defines a set of candidate moderators** grouped into four conceptual domains:
   - **Background:** mean age, % women, % minority, % with a partner, US-based
   - **Trauma-related:** discrete vs. non-discrete trauma, occupational trauma, trauma type
   - **Methodological:** diagnostic criteria (DSM-IV/5), entropy, LGMM vs. LCGA, number of trajectories, assessment method, GRoLTS quality score, RCT status, quadratic vs. linear modelling, sample size, assessment/follow-up timing
   - **Treatment:** treatment type, treatment length
4. **Summarizes each moderator** overall and by trajectory class in a formatted `gt` table, and flags moderators with no variation within a given trajectory subset (which cannot be estimated).
5. **Prepares data for Bayesian modelling**: standardizes continuous moderators, recodes categorical moderators to factors with defined reference levels, and drops constant variables per subset.
6. **Fits univariate Bayesian models** (via `brms`) for each moderator, one at a time, on each trajectory outcome. Each model:
   - Uses a binomial (logit) likelihood with `trials(Sample_size)`
   - Includes a random intercept for study, `(1|Study)`, to account for between-study heterogeneity
   - Handles missing continuous predictors via `brms`'s built-in `mi()` model-based imputation instead of listwise deletion
7. **Reports results** as posterior median estimates with 95% credible intervals, plus convergence diagnostics (R-hat, bulk/tail effective sample size), for every moderator × trajectory combination.

## Requirements

### R packages
```r
install.packages(c("readxl", "tidyverse", "lme4", "gt", "knitr", "rlang", "glue"))
```
`brms` additionally requires a Stan backend. This report uses `backend = "cmdstanr"`, so `cmdstanr` and CmdStan must also be installed:
```r
install.packages("brms")
install.packages("cmdstanr", repos = c("https://mc-stan.org/r-packages/", getOption("repos")))
cmdstanr::install_cmdstan()
```

### Data
The report expects an Excel data file at:
```
../data.xlsx
```
relative to the location of the `.Rmd` file — i.e., the working directory is set to the document's own folder.

## Sampling settings

Models are fit with 4 chains, 12,000 iterations (6,000 warmup), `adapt_delta = 0.999`, and a fixed seed (`seed = 1`) for reproducibility. These settings favor stable, well-converged posteriors over speed — expect fitting to be **computationally intensive**, especially across the full moderator set × 4 trajectories.

## How to run

Open the `.Rmd` file in RStudio and knit it, or run from the command line:
```r
rmarkdown::render("moderator_analysis.Rmd")
```
Supported output formats (configured in the YAML header): HTML (with floating table of contents and code folding), Word, and PDF.

## Output

The knitted report includes, per trajectory (Decreasing, Partially Decreasing, Persistent, Relapsing):
- A note on any moderators excluded for lack of variation in that subset
- A univariate model summary table (predictor, estimate, 95% credible interval, and a flag for intervals excluding zero)
- A convergence diagnostics table (R-hat, bulk ESS, tail ESS) for each model

## Notes / caveats

- This file covers **univariate** models only; it does not include adjusted or multivariable moderator models.
- Categorical moderators with no variation in the "Other" trauma-type level (or similarly uninformative levels) are filtered from display in the results tables.
- The `Relapsing` trajectory dataset drops `Percentage_minority` and `Percentage_partner` from data preparation (see `prepare_data_brms(..., exclude = ...)` in that section).
