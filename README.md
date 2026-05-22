# GPCR Sonification — β2-Adrenergic Receptor

Open-source pipeline for **sonifying** the molecular dynamics (MD) of the β2-adrenergic receptor (β2AR), and for **quantitatively evaluating** whether the rendered audio preserves the underlying MD-state information through cross-modal classification and canonical correlation analysis.

Companion repository for the manuscript:

> **Auditory Signatures of β2-Adrenergic Receptor Activation: a Sonification Framework for GPCR Molecular Dynamics**

| Notebook | Open in Colab |
|---|---|
| `01_GPCRMD_feature_extraction.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/eygpcr/gpcr-sonification-beta2ar/blob/main/notebooks/01_GPCRMD_feature_extraction.ipynb) |
| `02_GPCR_sonification_MIDI_WAV.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/eygpcr/gpcr-sonification-beta2ar/blob/main/notebooks/02_GPCR_sonification_MIDI_WAV.ipynb) |
| `03_GPCR_sonification_figures_summary.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/eygpcr/gpcr-sonification-beta2ar/blob/main/notebooks/03_GPCR_sonification_figures_summary.ipynb) |
| `04_GPCR_presentation_videos.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/eygpcr/gpcr-sonification-beta2ar/blob/main/notebooks/04_GPCR_presentation_videos.ipynb) |
| `05_GPCR_sonification_classification.ipynb` | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/eygpcr/gpcr-sonification-beta2ar/blob/main/notebooks/05_GPCR_sonification_classification.ipynb) |

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.11](https://img.shields.io/badge/python-3.11-blue.svg)](https://www.python.org/)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.PLACEHOLDER.svg)](https://doi.org/10.5281/zenodo.PLACEHOLDER)

---

## Overview

Three publicly available GPCRMD β2AR trajectories — `inactive` (Dynamic ID **11**), `active` apo (**116**), and `active + agonist` (**117**) — are passed through a five-stage pipeline:

```
GPCRMD trajectories
        │
        ▼
[01] MD feature extraction          (MDAnalysis)
        │   6 activation-related geometric features per frame
        ▼
[02] Sonification mapping           (pretty_midi + FluidSynth)
        │   MD features → pitch, duration, velocity, harmony, percussion
        │   3 timbres: piano, violin, flute
        ▼
[03] Audio rendering & figures      (librosa + matplotlib)
        │   spectrograms, distributions, piano-roll, PCA
        ▼
[04] Synchronised videos & HTML     (PyMOL + MoviePy)
        │   protein-cartoon MP4 + interactive HTML player
        ▼
[05] Cross-modal validation         (librosa + scikit-learn)
            statistical tests, Random Forest classifier, LOIO, CCA
```

## Key results (β2AR, this work)

|                                   | Activation pair (inactive vs active) | Ligand pair (active vs active + ligand) |
|---|---|---|
| MD-feature MWU q (BH)             | all < 1 × 10⁻⁴⁶                      | all < 1 × 10⁻²⁰                          |
| Audio RF balanced accuracy (pooled) | **0.995 ± 0.003**                   | **1.000 ± 0.000**                        |
| MD-feature baseline balanced acc.  | 0.989 ± 0.006                        | 1.000 ± 0.000                            |
| Information-retention ratio        | **1.006**                            | **1.000**                                |
| Canonical correlation r₁           | **0.926**                            | **0.995**                                |
| LOIO mean balanced accuracy        | 0.74                                 | 0.89                                     |

## Quickstart — Google Colab (recommended)

The notebooks are designed to run end-to-end in Google Colab with Google Drive mounted. Required Drive layout:

```
MyDrive/GPCR_Sonification/
└── data/raw/
    ├── inactive/{topology.pdb, trajectory.xtc}
    ├── active/{topology.pdb, trajectory.xtc}
    └── active_ligand_bound/{topology.pdb, trajectory.xtc}
```

Download the three GPCRMD trajectories (Dynamic IDs 11, 116, 117) from [GPCRMD](https://gpcrmd.org) and place them as above. Then click the Colab badge for notebook 01 and run cells in order. Each notebook auto-installs its own dependencies in the first cell.

## Quickstart — local

```bash
git clone https://github.com/eygpcr/gpcr-sonification-beta2ar.git
cd gpcr-sonification-beta2ar
python -m venv .venv
source .venv/bin/activate           # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter lab notebooks/
```

Local execution additionally requires:

- **FluidSynth** (system binary) for WAV synthesis from MIDI:
  - macOS: `brew install fluid-synth fluid-soundfont-gm`
  - Ubuntu: `sudo apt install fluidsynth fluid-soundfont-gm`
- **PyMOL** for the protein-cartoon video pipeline in notebook 04 (`pymol-open-source` is in `requirements.txt`; on macOS you may also need `conda install -c conda-forge pymol-open-source`).
- **FFmpeg** (system binary):
  - macOS: `brew install ffmpeg`
  - Ubuntu: `sudo apt install ffmpeg`

Update the `PROJECT_DIR` variable at the top of each notebook to point to your local data directory.

## What each notebook produces

| Notebook | Outputs |
|---|---|
| **01** Feature extraction | `data/processed/all_features.csv`; per-state CSV; QC plots |
| **02** Sonification | `outputs/audio/` — 9 single-state WAV/MID, 6 pair WAV/MID, 3 sequence WAV; per-state mapping CSV |
| **03** Figures & summary | `outputs/figures/` — features-timeseries, distributions, spectrograms, piano roll, PCA, composite; `outputs/tables/` — MD + MIDI summary statistics, methods table |
| **04** Videos & HTML player | `outputs/video/` — silent MP4 per state, audio MP4 per (state × instrument), side-by-side pair MP4; `outputs/presentation_bundle/` — self-contained HTML + assets |
| **05** Cross-modal validation | `outputs/tables/` — MD/audio MWU tests, binary + 3-class classifier results, LOIO, MD baseline, information-retention ratio, canonical correlation; `outputs/figures/` — confusion matrices, LOIO grid, CCA bars, audio PCA |

## Reproducing the manuscript figures and tables

Run notebooks 01 → 05 in order, then check `outputs/figures/` and `outputs/tables/` for files referenced in the manuscript. Random seeds are fixed (`random_state=42`) so classification numbers reproduce exactly.

| Manuscript artefact | Source file (Drive) |
|---|---|
| Table 2 | `outputs/tables/feature_summary_statistics.csv` |
| Table 3 | `outputs/tables/md_statistical_tests.csv` |
| Table 4 Panel A | `outputs/tables/binary_classification_results.csv` |
| Table 4 Panel B | `outputs/tables/loio_binary_classification_results.csv` |
| Table 4 Panel C | `outputs/tables/cca_md_audio.csv` |
| Figure 2 | `outputs/figures/fig_pair_*_features_distributions.{png,pdf,svg}` |
| Figure 3 | `outputs/figures/fig_pair_*_spectrograms_piano.{png,pdf,svg}` |
| Figure 4 | `outputs/figures/fig_pair_activation_cross_instrument_grid.{png,pdf,svg}` |
| Figure 5 | `outputs/figures/fig_classification_confusion_per_pair.{png,pdf,svg}` |
| Figure 6 | `outputs/figures/fig_cca_md_audio.{png,pdf,svg}` |
| Figure 7 | `outputs/figures/fig_classification_loio_per_pair.{png,pdf,svg}` |

## Data availability

- **Raw MD trajectories**: publicly available at [GPCRMD](https://gpcrmd.org) under Dynamic IDs 11, 116, 117. **Not redistributed in this repository** — please download directly from GPCRMD.
- **Processed data, audio renders, videos, figures, and tables**: archived on Zenodo at [DOI placeholder]. Mirror of this repository at each tagged release.

## Citation

If you use this pipeline or any of its outputs, please cite both the manuscript and the Zenodo deposit. A `CITATION.cff` is provided; GitHub will render a "Cite this repository" widget.

## License

[MIT](LICENSE) — free to use, modify, and distribute. Attribution to the manuscript is appreciated.

## Contact

Open a GitHub issue for questions or bug reports.

---

### Acknowledgments

This work used MD trajectory data from the [GPCRMD repository](https://gpcrmd.org) (Rodríguez-Espigares et al., *Nat. Methods* 2020). Sonification rendering uses [pretty_midi](https://github.com/craffel/pretty-midi), [FluidSynth](https://www.fluidsynth.org/), and the FluidR3 GM SoundFont; audio analysis uses [librosa](https://librosa.org/); MD analysis uses [MDAnalysis](https://www.mdanalysis.org/); protein visualisation uses [PyMOL Open Source](https://github.com/schrodinger/pymol-open-source).
