# Phase-Level Cricket AQA — Scientific Reports revision code

Reproducibility materials for the controlled revision experiments supporting:

**Phase-Level Video Assessment of Cricket Batting Technique Using RGB and Pose-Based Movement Representations**

This repository contains the additional controlled experiments and reviewer-facing analyses introduced during the Scientific Reports major revision. The underlying dataset remains **UJ-AQA-CricketVision**.

## Repository contents

- `notebooks/00_prepare_revision_data_and_features.ipynb` — prepares the retained samples, grouped split, pose arrays, and I3D feature caches.
- `notebooks/01_rgb12_grouped.ipynb` — RGB-12 controlled model.
- `notebooks/02_rgb29_grouped.ipynb` — RGB-29 controlled model.
- `notebooks/03_pose29_grouped.ipynb` — Pose-29 controlled model.
- `notebooks/04_rgbpose29_grouped.ipynb` — RGB+Pose-29 controlled model.
- `notebooks/05_revision_analysis.ipynb` — repeated-run summaries, bootstrap comparisons, sensitivity analysis, pose quality, subgroup analysis, calibration, residuals, and Bland–Altman agreement.
- `results/` — reviewer-facing CSV outputs used in the revised manuscript.
- `figures/` — principal figures generated from the revision analyses.

## Data

The underlying UJ-AQA-CricketVision dataset and original code release are available at:

https://github.com/dvanderhaar/uj-aqa-cricketvision

The revision notebooks expect the processed 29-frame stroke folders used in the study, with folder names matching:

```text
processed_strokes_*_interp_29
```

This repository does **not** redistribute the source broadcast footage.

## Configuration

Before running Notebook 00, set the following environment variable:

```text
AQA_DATA_ROOT=/path/to/folder/containing/processed_strokes_*_interp_29
```

Optional:

```text
AQA_REVISION_ROOT=/path/for/cache-and-model-outputs
AQA_I3D_CODE_DIR=/path/containing/i3d_inception.py
```

If `AQA_REVISION_ROOT` is not set, runtime artefacts are written to `./artifacts/` and are ignored by Git.

The RGB notebooks use the same pretrained I3D implementation as the original study. If `i3d_inception.py` is not already importable, set `AQA_I3D_CODE_DIR` to the folder containing it.

## Run order

1. `00_prepare_revision_data_and_features.ipynb`
2. `01_rgb12_grouped.ipynb`
3. `02_rgb29_grouped.ipynb`
4. `03_pose29_grouped.ipynb`
5. `04_rgbpose29_grouped.ipynb`
6. `05_revision_analysis.ipynb`

Notebook 00 performs the expensive preprocessing/feature caching once. Notebooks 01–04 train the four matched conditions. Notebook 05 recreates the reviewer-facing analyses.

## Controlled design

All controlled conditions use the same retained strokes, source-video-grouped partition, phase structure, target matrix, optimisation protocol, MSE loss, and phase-specific regression-head design. RGB and pose necessarily use modality-specific encoders, so parameter counts are not identical.

The canonical 29-frame representation is:

- 14 buildup frames
- 7 execution frames
- 8 follow-through frames

RGB-12 is selected deterministically from the same canonical strokes as:

- 4 buildup frames
- 3 execution frames
- 5 follow-through frames

The fixed source-video-grouped split (seed 42) contains:

- training: 153 source videos / 5,806 strokes
- validation: 30 source videos / 1,378 strokes
- test: 22 source videos / 967 strokes

The split guarantees **source-video independence, not player independence**.

## Repeated runs

Controlled models are trained with fixed seeds:

```text
42, 123, 2026
```

The manuscript reports mean ± standard deviation across these runs. The principal paired bootstrap comparisons use the predefined seed-42 runs and 5,000 resamples.

## Key controlled result

| Model | SRC | Pearson | MAE | RMSE |
|---|---:|---:|---:|---:|
| RGB-12 | 0.298 ± 0.030 | 0.351 ± 0.063 | 1.407 ± 0.137 | 1.781 ± 0.077 |
| RGB-29 | 0.318 ± 0.020 | 0.347 ± 0.018 | 1.451 ± 0.065 | 1.810 ± 0.028 |
| **Pose-29** | **0.536 ± 0.018** | **0.544 ± 0.013** | **1.268 ± 0.020** | **1.563 ± 0.019** |
| RGB+Pose-29 | 0.391 ± 0.054 | 0.424 ± 0.057 | 1.339 ± 0.088 | 1.712 ± 0.073 |

## Interpretation

- Targets are **expert-derived technical assessments**, not direct three-dimensional biomechanical measurements.
- Phase boundaries and batter localisation are supplied by the dataset annotations; the framework is not end-to-end.
- Pose-29 is the strongest controlled representation under the present protocol.
- Regression toward the mean and wide individual-score limits of agreement remain important limitations.
- Detailed phase/body-part, stroke subgroup, calibration, residual, score-bin, and agreement analyses in Notebook 05 use **Pose-29**, matching the revised manuscript.

## Citation

Please cite the UJ-AQA-CricketVision dataset paper when using the underlying dataset.

Repository URL:

https://github.com/tevinmoodley/phase-level-cricket-aqa

Citation details for the Scientific Reports article can be updated after publication.
