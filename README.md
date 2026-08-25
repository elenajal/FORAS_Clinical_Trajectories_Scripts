# Overview

This repository contains the analytical scripts and descriptive code for the paper **"A Meta-analysis: Clinical PTSD Symptom Trajectories in Relation to Evidence-based Trauma-focused Treatment"** by Jalsovec et al., (in prep.).
It is part of the **Hunt for the Last Relevant Paper: blending the best of humans and AI** project (full paper: https://doi.org/10.1080/20008066.2025.2546214). 
The primary goal of this repository is to ensure a fully transparent and reproducible computational pipeline. All scripts regarding descriptive and inferential analyses are hosted here, while the corresponding datasets, models, and codebooks are archived on DataverseNL.

## Repository Content
Within this repository, you will find the R scripts utilized for:
*   **Descriptive Analyses:** Code used to generate sample descriptives, heatmaps (e.g., sample locations), and timepoint assessment visualizations.
*   **Inferential Analyses:** Scripts detailing the Bayesian random-intercept Generalised Linear Mixed Models (GLMM) used for pooling trajectory prevalence. These models were fitted using the `brms` package via Stan, employing Hamiltonian Monte Carlo sampling.
*   **Moderator Analyses:** Bayesian univariate moderation models assessing the impact of various predictors on the relative prevalence of each trajectory.
*   **Sensitivity Analyses:** Code for robustness checks, including leave-one-out (LOO) cross-validation and conservative lower-bound estimates.

## Data Overview
The data feeding into these analyses stems from a systematic review comprising 13 studies, yielding 14 distinct samples and a total of 19,131 observations. 

The extracted dataset includes the following categorized variables used in our moderator analyses:
*   **Background / Sociodemographic:** Mean age, percentage of women, percentage with a partner, minority status percentage, and study location (US vs. non-US).
*   **Trauma-Related:** Trauma type (combat, assault, or other), trauma discreteness (discrete vs. non-discrete), and occupational trauma exposure.
*   **Methodological:** Number of trajectories, latent modelling approach (LCGA vs. LGMM), functional form (linear vs. quadratic), diagnostic criteria (DSM-IV vs. DSM-5), PTSD assessment method (interview vs. self-reported), time elapsed between assessments, total timepoints, entropy, sample size, RCT design (yes/no), and study quality/GRoLTS score.
*   **Treatment-Related:** Investigated treatment type (exposure, CBT, or mixed) and treatment length in weeks.

### Trajectory Reclassification Data
To standardize outcomes across disparate studies, the data includes reclassified trajectory labels aligned with a standardized framework grounded in clinically meaningful change. The data maps original study classes into four prototypical symptom trajectories: Decreasing, Partially Decreasing, Persistent, and Relapsing.

## Data Access and Reproducibility
To run the scripts in this repository, you must first download the raw data file. All datasets, full model outputs, and detailed codebooks are hosted externally on DataverseNL. 

# Funding 
The research is supported by the Dutch Research Council under grant number 406.22.GO.048

# Contact
For questions contact Elena Jalsovec (e.s.jalsovec@uu.nl) 
