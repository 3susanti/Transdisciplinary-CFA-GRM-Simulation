# CFA–GRM Simulation Pipeline

Analysis script and output-generation pipeline for the manuscript
**"Smarter but Not Wiser": A Transdisciplinary CFA-GRM Simulation Pipeline for Mapping Human–GenAI Interaction**

The script simulates a synthetic response dataset (N = 1,000; 36 ordinal items
across three latent domains: Cognitive, Affective, Conative), fits a
three-factor Confirmatory Factor Analysis (CFA, WLSMV estimator) and three
per-domain Graded Response Models (GRM), classifies respondents into latent
interaction-quality typologies, and exports every figure and table used in the
manuscript together with the underlying and extended data files deposited
alongside it.

An optional replication study (Section 6) repeats the whole generate–estimate
cycle 200 times at each of two sample sizes and reports parameter recovery.

## Requirements

- R >= 4.6.1
- Internet access on first run (to install `pacman`, which then installs and
  loads the remaining packages automatically)

Packages used (auto-installed via `pacman::p_load()`):
`lavaan`, `psych`, `MASS`, `dplyr`, `mirt`, `ggplot2`, `semTools`, `corrplot`,
`tidyr`, `patchwork`, `stringr`, `here`

No absolute paths are used; the script writes all output to the current working
directory.

## How to run

**Normal run — this is the default.** Takes 1–3 minutes and reproduces every
figure and table in the main article.

```r
setwd(here::here())
source("ExtendedData_8_R_Analysis_Script.R")
```

Or from a terminal:

```bash
Rscript ExtendedData_8_R_Analysis_Script.R
```

**Full run including the Monte Carlo replication study.** Section 6 is disabled
by default because computing it from scratch takes several hours. Set `RUN_MC`
in the console *before* sourcing — do not edit the script:

```r
RUN_MC <- TRUE; source("ExtendedData_8_R_Analysis_Script.R")
```

### Verifying the replication results without recomputing them

You do not need to run Section 6 to check the reported recovery results. Its
summary tables are deposited as `ExtendedData_9/10/11.csv`, and the
per-replication raw estimates as `MC_raw_results_N1000.rds` and
`MC_raw_results_N300.rds`. Place those two `.rds` files in the project root and
use the full-run command above: the checkpoint is reused and the tables are
regenerated in under a minute, with no recomputation. To recompute everything
from scratch, delete the `.rds` files first.

`MC_REPS`, `MC_N` and `MC_SEED` are stored inside the checkpoint files. If any
of them differs from the deposited values, the checkpoint cannot be reused and
the study restarts from zero.

### Determinism

The random seed is fixed (`set.seed(20260502)`), so re-running reproduces the
same synthetic dataset, model estimates and figures. In the replication study,
replication *r* of condition *k* uses seed `20260502 + 100000k + r`, so results
do not depend on the number of processor cores used.

Console output reports progress through Sections 0–7, followed by a compliance
check (figure dimensions, DPI, file sizes) and `sessionInfo()` for
reproducibility reporting.

## Output files

All files are written to the project root. Filenames indicate whether each item
belongs to the main article, its underlying data, or extended (supplementary)
data, per F1000Research's data-deposit convention. The table follows the order
in which the script writes them.

| File | Manuscript item | Description |
|---|---|---|
| `ExtendedData_1_Simulation_Specs.csv` | Extended Data 1 | Simulation design parameters (N, items, Phi/a/b ranges) |
| `Fig1_Methodology_Flowchart.tiff` | Figure 1 | Methodology flowchart (six-step pipeline) |
| `UnderlyingData_1_Dataset_Simulated_N1000.csv` | Underlying Data 1 | Full simulated item-response dataset (N = 1,000 × 36 items) |
| `UnderlyingData_2_Table1_CFA_Fit_Indices.csv` | Table 1 | CFA fit indices (robust CFI, TLI, RMSEA, SRMR, scaled chi-square, p) |
| `Fig2_Panel_Correlation_TIF.tiff` | Figure 2 | Test Information Functions per domain + latent correlation matrix |
| `UnderlyingData_3_Table2_GRM_Summary.csv` | Table 2 | GRM parameter summary by domain (loadings, discrimination, thresholds, ordinal alpha, omega) |
| `ExtendedData_2_FullGRM_Parameters.csv` | Extended Data 2 | Item-level GRM parameters for all 36 items |
| `Fig3_Panel_Threshold_ICC.tiff` | Figure 3 | Threshold distributions and item category characteristic curves |
| `Fig4_Typology_Distribution.tiff` | Figure 4 | Typology frequency distribution |
| `Fig5_Interaction_Profiles.tiff` | Figure 5 | Mean latent score profile by typology |
| `UnderlyingData_4_Table3_Latent_Typologies.csv` | Table 3 | Latent typologies and scaffolding implications |
| `ExtendedData_3_Individual_Typologies_N1000.csv` | Extended Data 3 | Per-respondent latent scores and typology assignment |
| `ExtendedData_4_FigS1_Latent_Trait_Distribution.tiff` | Extended Data 4 (Fig S1) | Latent trait score distributions by domain |
| `UnderlyingData_5_Table4_Domain_Design.csv` | Table 4 | Domain-level design implications and scaffolding strategies |
| `ExtendedData_5_FigS2_Item_Information_Functions.tiff` | Extended Data 5 (Fig S2) | Item information functions for one representative item per domain |
| `ExtendedData_6_FigS3_Omega_Reliability.tiff` | Extended Data 6 (Fig S3) | Ordinal alpha and McDonald's omega by domain |
| `ExtendedData_7_Model_Estimation_Summary.csv` | Extended Data 7 | Consolidated model fit, reliability and GRM summary |

Produced only when `RUN_MC <- TRUE`:

| File | Manuscript item | Description |
|---|---|---|
| `ExtendedData_9_MC_Recovery_Summary.csv` | Extended Data 9 | Recovery summary by parameter group and domain |
| `ExtendedData_10_MC_Item_Level_Recovery.csv` | Extended Data 10 | Item-level recovery: bias, RMSE, SE bias and coverage for all 147 parameters |
| `ExtendedData_11_MC_Convergence_Fit.csv` | Extended Data 11 | Convergence, admissibility, fit indices and Type I error rate per condition |
| `UnderlyingData_7_Table6_MC_Recovery_ByGroup.csv` | Table 6 | Recovery collapsed to parameter group — the values printed in Table 6 |
| `MC_raw_results_N1000.rds`, `MC_raw_results_N300.rds` | Extended Data 12 | Per-replication raw estimates and standard errors; allow Extended Data 9–11 to be regenerated without repeating the simulation |

`UnderlyingData_6_Table5_Transdisciplinary_Calibration_Matrix.xlsx` is **not**
produced by this script. Table 5 is the authors' conceptual synthesis, not a
computed result, and is deposited separately.

All CSV files are written with a UTF-8 byte-order mark so that special
characters render correctly when opened directly in Excel. Counts such as
"Domains Meeting Criteria" are written as plain integers rather than fractions,
because spreadsheet software silently converts values such as `3/3` into dates
on import.

Figures are exported as TIFF at 600 dpi within the 75–150 mm single/double
column width range required by F1000Research's line-art guidelines. See
`FIG_COMPRESSION` in Section 0 to switch between uncompressed output and
lossless LZW compression.

## Reproducibility notes

- Sample size for the illustrative dataset (N = 1,000) follows sample-size
  guidance for structural equation models (Wolf et al., 2013; Muthén & Muthén,
  2002), cited in the script.
- GRM calibration and reliability (ordinal alpha, omega) are computed
  separately per domain (12 items each), not pooled across all 36 items,
  consistent with the unidimensional-per-domain measurement model described in
  the manuscript's Methods section. Alpha is computed on the polychoric
  correlation matrix, so it is ordinal alpha rather than Cronbach's alpha on
  raw item scores.
- Fit indices for the categorical CFA are the robust (or, where unavailable,
  scaled) versions; the unscaled DWLS chi-square is retained in Extended Data 7
  for reference only.
- Exact package versions for the reference run are printed by `sessionInfo()`
  at the end of each run.

## Repository structure

```

|-- ExtendedData_8_R_Analysis_Script.R   # Main analysis and export script
|-- README.md                             # This file
|-- LICENSE                               # CC BY 4.0
`-- data/                                 # Deposited underlying and extended data

```

## Citation

If you use this script or its outputs, please cite the associated manuscript.
Add the full citation and the Zenodo DOI here once assigned.

## License

Released under the CC-BY 4.0 License. See (LICENSE).
