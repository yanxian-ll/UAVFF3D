# UAVFF3D: A Geometry-Aware Benchmark for Feed-Forward UAV 3D Reconstruction

> **UAVFF3D** is a geometry-aware real-synthetic benchmark for evaluating and adapting feed-forward 3D reconstruction models under UAV photogrammetric acquisition. It focuses on camera-geometry robustness, including oblique-view degradation and horizontal field-of-view (hFOV)-height ambiguity.

[![Dataset](https://img.shields.io/badge/Dataset-Coming%20Soon-orange)](#dataset-release)
[![Data Pipeline](https://img.shields.io/badge/Data%20Pipeline-UAVFF3D--Pipeline-blue)](https://github.com/yanxian-ll/UAVFF3D-Pipeline)
[![Fine-tuning & Evaluation](https://img.shields.io/badge/Fine--tuning%20%26%20Evaluation-uavff3d__evaluate__forward__models-blue)](https://github.com/yanxian-ll/uavff3d_evaluate_forward_models)
[![License](https://img.shields.io/badge/License-Apache--2.0-green)](LICENSE)

<p align="center">
  <img src="assets/uavff3d-overview.png" alt="UAVFF3D unified multi-source data construction pipeline and benchmark components" width="100%">
</p>

<p align="center">
  <em>UAVFF3D unified data construction pipeline and benchmark components. </a>.</em>
</p>

---

## Overview

Recent feed-forward 3D reconstruction models can directly predict camera parameters, depth, rays, point maps, or dense 3D geometry from images. However, UAV imagery introduces camera geometries that are underrepresented in many general-purpose reconstruction datasets:

- high-altitude nadir views;
- oblique and multi-camera UAV acquisition;
- large hFOV variation;
- coupled hFOV-height changes and similar-image-footprint ambiguity;
- metric-scale, ray-direction, and camera-scene consistency challenges.

UAVFF3D is designed for three goals:

1. **UAV-domain adaptation** - provide real and synthetic UAV-style data for fine-tuning feed-forward reconstruction models.
2. **Metric real-world evaluation** - evaluate reconstruction quality on real UAV scenes with LiDAR-grounded or reconstruction-based reference geometry.
3. **Controlled camera-geometry diagnosis** - analyze failure modes caused by oblique acquisition and hFOV-height ambiguity.

---

---

## Dataset Components

UAVFF3D contains three complementary components.

| Component | Description | Main use |
| --- | --- | --- |
| **UAVFF3D-Real** | Real UAV imagery with reconstruction supervision and LiDAR-supported test scenes. | Real-world evaluation and UAV-domain adaptation. |
| **UAVFF3D-Syn** | Controllable synthetic UAV data rendered from textured 3D assets with diverse flight heights, hFOVs, nadir views, oblique views, and irregular trajectories. | Camera-geometry coverage and synthetic UAV-domain adaptation. |
| **UAVFF3D-FA** | Controlled hFOV-height diagnostic split where hFOV and flight height are changed while the image footprint remains approximately comparable. | Projection-geometry and metric-scale robustness diagnosis. |

The benchmark is intended for feed-forward 3D reconstruction models that predict depth, point clouds, camera poses, camera rays, or dense scene geometry from multi-view UAV images.

---

## Dataset Release

The public dataset release will be added after the paper artifacts are finalized.

| Resource | Status |
| --- | --- |
| UAVFF3D full dataset | Coming soon |
| UAVFF3D-Real | Coming soon |
| UAVFF3D-Syn | Coming soon |
| UAVFF3D-FA | Coming soon |
| Preprocessed metadata and splits | Coming soon |
| Fine-tuned checkpoints | Coming soon |

Suggested local layout after downloading:

```text
UAVFF3D_DATA_ROOT/
  UAVFF3D-Real/
    train/
    test/
  UAVFF3D-Syn/
  UAVFF3D-FA/
  metadata/
  splits/
```

The companion code repositories use this naming convention. Keeping these directory names avoids changing Hydra configs, data-loader defaults, and bash entry points.

---

## Companion Code Repositories

UAVFF3D is split into two companion repositories so that the dataset construction code and model evaluation code can evolve independently.

### 1. Data Construction and Processing Pipeline

Repository: <https://github.com/yanxian-ll/UAVFF3D-Pipeline>

This repository contains the data construction pipeline used to build UAVFF3D, including:

- synthetic rendering for UAVFF3D-Syn and UAVFF3D-FA;
- UAV trajectory simulation and view selection;
- LiDAR-SfM alignment for LiDAR-supported real UAV scenes;
- per-view depth rendering, valid-mask generation, and depth fusion;
- conversion of heterogeneous UAV/MVS data into a unified scene format;
- scene metadata, covisibility graph, and train/test split generation.

### 2. Model Fine-tuning and Evaluation

Repository: <https://github.com/yanxian-ll/uavff3d_evaluate_forward_models>

This repository contains the fine-tuning and benchmark evaluation framework. It is based on the **MapAnything** training and evaluation framework and adds UAVFF3D-specific data loaders, configs, scripts, and model wrappers.

It includes:

- UAV-domain fine-tuning scripts under `bash_scripts/train/uav_finetuning`;
- dense N-view benchmark scripts under `bash_scripts/benchmark/uav_dense_n_view`;
- shared-alignment evaluation for coupled camera-scene consistency;
- wrappers for representative feed-forward reconstruction models;
- prior-aware evaluation with camera intrinsics, camera poses, or both;
- retained third-party integrations required by the paper experiments, including HunyuanWorld-Mirror and Depth Anything 3.

---

## Unified Scene Format

Processed scenes follow a common data format across real UAV data, LiDAR-supported data, and synthetic scenes.

```text
scene/
  images/
    000000.png
    000001.png
    ...
  cams/
    000000.txt
    000001.txt
    ...
  depth/
    000000.exr
    000001.exr
    ...
  mask/
    000000.png
    000001.png
    ...
  scene_meta.json
```

Optional outputs may include:

```text
scene/
  depth_complete/
  depth_lidar/
  depth_sfm/
  semantic_mask/
  covisibility/
```

Each camera file stores camera intrinsics and world-to-camera extrinsics in the OpenCV camera convention: **x right, y down, z forward**.

---

## Evaluation Protocol

UAVFF3D evaluates the predicted cameras and dense geometry as a coupled reconstruction result. Instead of aligning camera poses and point clouds independently, the benchmark estimates one scene-level similarity transform and applies it consistently to both the predicted dense geometry and the predicted cameras.

The main metrics include:

| Metric | Target |
| --- | --- |
| **AbsRel Depth** | Image-aligned metric depth accuracy |
| **Ray Error** | Camera-ray and projection-geometry consistency |
| **Pose ATE** | Camera-center accuracy after shared scene-level alignment |
| **Rotation MAE** | Camera-orientation accuracy |
| **Chamfer-L1** | Dense 3D geometry accuracy |

This shared-alignment protocol is designed to expose camera-scene inconsistency that can be hidden by separate camera and geometry alignments.

---

## Fine-tuning and Evaluation Settings

The evaluation framework supports image-only and prior-aware model inputs.

| Setting | Input |
| --- | --- |
| **RGB** | Images only |
| **C** | Images + camera intrinsics |
| **P** | Images + camera poses |
| **CP** | Images + camera intrinsics + camera poses |

Representative feed-forward reconstruction models evaluated by the project include VGGT, Pi3, MapAnything, Pi3X, and Depth Anything 3. The fine-tuning code uses a MapAnything-based training interface so that data sampling, multi-view preprocessing, and metric reporting remain consistent across models.

---

## Quick Start

### Data construction pipeline

```bash
git clone https://github.com/yanxian-ll/UAVFF3D-Pipeline.git
cd UAVFF3D-Pipeline
pip install -r requirements.txt
```

Refer to the pipeline repository for synthetic rendering, LiDAR-SfM alignment, unified-format conversion, and metadata generation.

### Fine-tuning and evaluation

```bash
git clone https://github.com/yanxian-ll/uavff3d_evaluate_forward_models.git
cd uavff3d_evaluate_forward_models

conda create -n uavff3d python=3.12 -y
conda activate uavff3d
pip install -e .
```

Before running training or evaluation, update dataset roots, checkpoint paths, and machine-specific paths in the corresponding config files.

---

## Compatibility Notes

The main project, the data pipeline repository, and the fine-tuning/evaluation repository use the same naming convention:

```text
UAVFF3D
UAVFF3D-Real
UAVFF3D-Syn
UAVFF3D-FA
uavff3d
```

Use the UAVFF3D naming convention consistently in all new configs, scripts, and documentation.

---

## Citation

If you use UAVFF3D, the data construction pipeline, or the fine-tuning/evaluation framework, please cite the paper. The final BibTeX entry will be updated after publication.

```bibtex
@misc{uavff3d2026,
  title  = {UAVFF3D: A Geometry-Aware Benchmark for Feed-Forward UAV 3D Reconstruction},
  author = {Yang, Xiang and Wang, Yongli and Li, Haifeng and Zhang, Yunsheng},
  year   = {2026},
  note   = {Dataset, pipeline, and benchmark framework}
}
```

---

## License

The documentation and project-page files in this repository are released under the Apache License 2.0. See [LICENSE](LICENSE).

The dataset, 3D assets, pretrained checkpoints, fine-tuned checkpoints, and third-party model code may be released under different licenses. Please check the dataset release notes and the `THIRD_PARTY.md` / `NOTICE` files in the companion repositories before redistribution or commercial use.

---

## Acknowledgements

UAVFF3D builds on prior work in photogrammetric reconstruction, UAV mapping, synthetic data generation, and feed-forward 3D reconstruction. The fine-tuning/evaluation repository is based on the MapAnything framework and integrates several third-party feed-forward reconstruction models for benchmark comparison.
