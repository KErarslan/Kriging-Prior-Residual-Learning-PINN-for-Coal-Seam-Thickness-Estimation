# Kriging-Prior Residual-Learning PINN for Coal Seam Thickness Estimation

Reproducible pipeline accompanying the manuscript:

> Özdemir, M., Erarslan, K. *Physics-Informed Neural Networks with Kriging-Prior Residual
> Learning: A Validated Benchmark for Coal Seam Thickness Estimation.* Submitted to the
> *Arabian Journal for Science and Engineering* (2026).

This repository contains the complete, leakage-free, seed-controlled benchmarking pipeline
comparing Universal Kriging, a standalone physics-informed neural network (PINN), and a
Kriging-prior residual-learning hybrid, for spatial estimation of coal seam thickness at an
undisclosed lignite deposit in Kütahya Province, Türkiye (n = 191 boreholes).

## What this pipeline does

- **Leakage-free preprocessing**: every cross-validation fold refits its own scaling
  statistics from that fold's training data only.
- **Staged Kriging optimization**: an automatic, sequential search over variogram model,
  trend, and anisotropy (Sections 5 and 23 of the notebook).
- **PINN capacity ablation and hyperparameter decomposition**: small/medium/large
  architectures, each evaluated over multiple independent seeds under full leave-one-out
  cross-validation (LOOCV), followed by controlled, paired tests isolating the effect of
  learning rate, epoch budget, and scheduler settings (Sections 7-18).
- **Kriging-prior residual-learning hybrid**: a network trained to correct the Kriging
  prediction rather than replace it, with a nested-LOOCV residual target and a kriging-
  variance-weighted loss (Sections 19-21).
- **Uncertainty calibration**: comparison of Kriging variance, PINN ensemble spread, and
  hybrid uncertainty sources against coverage and rank-correlation criteria (Sections 10,
  24, 25).
- **Illustrative volumetric cross-check**: a roof/floor surface integration used only for
  cross-method comparison, explicitly not a classified mineral resource or reserve estimate
  (Section 22).

All checkpointing, seed control, and the data/configuration fingerprint guard (Section 4)
are designed so that long runs can be safely resumed across multiple sessions without
mixing results from different configurations.

## Repository contents

```
kriging_vs_pinn_github_pipeline.ipynb   # main pipeline notebook (English)
requirements.txt                            # Python dependencies
LICENSE
README.md
```

The dataset itself is **not included** in this repository (see *Data availability* below).

## Running the pipeline

The notebook was developed and run on Google Colab; the `large` PINN architecture takes
roughly 110-120 minutes per seed under full LOOCV, so a complete run of every section is a
multi-hour undertaking best split across several sessions using the built-in checkpointing
(see the notebook's "Practical note for long runs"). To run:

1. Open the notebook in Google Colab (or a local Jupyter environment with a GPU/CPU of your
   choice).
2. Provide your own copy of the borehole dataset (columns: `drill_hole`, `X`, `Y`, `Z`,
   `thickness`, `calorific_value`, `coal_top`) and point `CONFIG['data_path']` to it.
3. Install dependencies: `pip install -r requirements.txt`.
4. Run cells sequentially from the top. Intermediate results are cached to
   `RESULTS_DIR` (Google Drive when run in Colab, a local `results/` folder otherwise) and
   are safe to resume.

For a quick functional check without the full multi-hour run, set `CONFIG['n_folds']` to a
small integer (e.g. 15-20) rather than `None`; Section 4's stratified subsampling keeps the
reduced run representative of the full-data thickness distribution. **Do not compare R²
values from a reduced run to the manuscript's reported figures** — they use different,
much smaller evaluation sets and are not meant to reproduce the paper's numbers, only to
verify the code runs correctly end to end.

## Data availability

The borehole dataset is confidential and cannot be publicly shared, per the terms under
which it was obtained from the site operator. The site itself is not named in the
manuscript at the request of its post-privatization ownership. Researchers with a
legitimate interest in verifying specific results may contact the corresponding author.

## Before committing changes

**Always clear all cell outputs before committing an updated notebook to this repository.**
Running the pipeline on the real dataset embeds its results — summary statistics, printed
metrics, coordinates, plots — directly in the `.ipynb` file's saved output cells, even
though the data file itself is never uploaded. Committing a notebook that was run and saved
with its outputs intact would leak exactly the confidential information described above. In
Colab: **Edit > Clear all outputs** (or, from the command line, `jupyter nbconvert
--clear-output --inplace <notebook>.ipynb`) before every commit.

## Requirements

See `requirements.txt`. Developed against Python 3.10+ (Google Colab's standard runtime).

## Citation

A full citation will be added once the manuscript is accepted. In the meantime, please cite
this repository directly if you build on the pipeline.

## License

Code in this repository is released under the MIT License (see `LICENSE`). The dataset is
not covered by this license and is not distributed here.

## Contact

Kaan Erarslan (corresponding author) — kaan.erarslan@dpu.edu.tr
Department of Mining Engineering, Kütahya Dumlupınar University, Kütahya, Türkiye
