# A3D-Bench: A Geometry-Aware Benchmark for Feed-Forward UAV 3D Reconstruction

> **A3D-Bench** is a geometry-aware real--synthetic benchmark for evaluating and adapting feed-forward 3D reconstruction models under UAV photogrammetric acquisition.  
> It is designed to study how camera geometry, especially oblique acquisition and hFOV--height ambiguity, affects feed-forward reconstruction quality.

[![Dataset](https://img.shields.io/badge/Dataset-Coming%20Soon-orange)](#dataset-download)
[![Pipeline](https://img.shields.io/badge/Data%20Pipeline-GitHub-blue)](https://github.com/yanxian-ll/a3dbench_pepeline)
[![Evaluation](https://img.shields.io/badge/Fine--tuning%20%26%20Evaluation-GitHub-blue)](https://github.com/yanxian-ll/a3dbench_evaluate_forward_models)

---

## Overview

Recent feed-forward 3D reconstruction models have shown strong performance on general multi-view images, but they can still degrade under UAV-specific camera geometry. UAV imagery introduces acquisition patterns that are uncommon in many general-purpose training datasets, including:

- high-altitude nadir views,
- five-view oblique imaging,
- large changes in horizontal field of view (hFOV),
- varying flight height and ground footprint,
- strong metric-scale and camera-ray ambiguity.

A3D-Bench is built to support three goals:

1. **UAV-domain adaptation**: provide real and synthetic UAV-style data for fine-tuning feed-forward reconstruction models.
2. **Metric real-world evaluation**: evaluate reconstruction quality on UAV scenes with LiDAR-grounded reference geometry.
3. **Controlled camera-geometry diagnosis**: analyze failure modes caused by oblique views and hFOV--height ambiguity.

---

## Dataset Components

A3D-Bench contains three complementary components.

| Component | Description | Main Use |
| --- | --- | --- |
| **A3D-Real** | Real UAV imagery with reconstructed scenes and LiDAR-grounded test scenes. | Real-world evaluation and UAV-domain adaptation. |
| **A3D-Syn** | Controllable synthetic UAV data rendered from textured 3D assets with diverse flight heights, hFOVs, nadir views, oblique views, and irregular trajectories. | Camera-geometry coverage and synthetic UAV-domain adaptation. |
| **A3D-FA** | A controlled hFOV--height diagnostic split where hFOV and flight height are changed while the observed image footprint is approximately preserved. | Testing whether models can recover projection geometry and metric scale from visually similar images. |

The benchmark is intended for feed-forward 3D reconstruction models that predict depth, point clouds, camera poses, camera rays, or dense scene geometry from a set of posed or unposed images.

---

## Dataset Download

The public dataset release will be added here after the paper artifacts are ready.

| Resource | Link |
| --- | --- |
| Full A3D-Bench dataset | **Coming soon** |
| A3D-Real | **Coming soon** |
| A3D-Syn | **Coming soon** |
| A3D-FA | **Coming soon** |
| Preprocessed metadata / splits | **Coming soon** |
| Fine-tuned checkpoints | **Coming soon** |

Suggested local layout after downloading:

```text
A3D-Bench/
  A3D-Real/
  A3D-Syn-L/
  A3D-Syn-S/
  A3D-FA/
  splits/
  metadata/
```

---

## Code Repositories

This project uses two companion repositories.

### 1. Dataset Construction and Processing Pipeline

Repository:  
https://github.com/yanxian-ll/a3dbench_pepeline

This repository contains the data construction pipeline used to build A3D-Bench, including:

- synthetic data rendering for A3D-Syn-L, A3D-Syn-S, and A3D-FA;
- multi-OBJ tile processing and UAV flight-path rendering;
- interactive viewpoint and trajectory selection for irregular synthetic scenes;
- LiDAR--SfM alignment for LiDAR-grounded real UAV scenes;
- per-view depth rendering and depth fusion;
- conversion of heterogeneous UAV/MVS datasets into a unified scene format;
- scene metadata, covisibility graph, and train/test split generation.

### 2. Model Fine-tuning and Evaluation

Repository:  
https://github.com/yanxian-ll/a3dbench_evaluate_forward_models

This repository contains the model adaptation and benchmark evaluation framework, including:

- fine-tuning scripts for representative feed-forward reconstruction models;
- UAV-domain adaptation with A3D training data;
- zero-shot and fine-tuned evaluation protocols;
- dense multi-view benchmark scripts;
- depth, camera-ray, pose, rotation, and point-cloud metrics;
- prior-aware evaluation with camera intrinsics, camera poses, or both.

---

## Dataset Construction Pipeline

A3D-Bench converts real UAV data, UAV--LiDAR acquisitions, external aerial datasets, and rendered synthetic data into a common representation.

### 1. Real UAV and LiDAR-grounded acquisition

A3D-Real includes UAV image blocks captured under UAV photogrammetric settings. LiDAR-grounded test scenes are processed by:

1. reconstructing image blocks with a photogrammetric SfM/MVS pipeline;
2. aligning LiDAR point clouds to the SfM coordinate system;
3. rendering LiDAR depth and SfM/MVS depth from the same camera parameters;
4. fusing LiDAR and reconstruction depth to obtain dense image-aligned supervision.

### 2. Synthetic UAV generation

A3D-Syn is rendered from textured 3D scenes using planned UAV camera programs. The synthetic pipeline supports:

- nadir and oblique camera rigs;
- structured large-area lawnmower-style flight paths;
- irregular local trajectories;
- controllable hFOV, focal length, flight height, overlap, and viewing direction;
- RGB image, depth map, camera intrinsic, camera extrinsic, and metadata export.

### 3. Controlled hFOV--height diagnostic data

A3D-FA is designed to isolate hFOV--height ambiguity. For each scene, multiple acquisition groups are rendered with different hFOV values while adjusting camera height so that the image footprint remains approximately comparable. This split is used only for controlled evaluation and is excluded from fine-tuning.

### 4. Unified scene format

Processed scenes follow a common layout:

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
  scene_meta.json
```

Optional data may include:

```text
scene/
  mask/
  depth_complete/
  depth_lidar/
  depth_sfm/
  semantic_mask/
  covisibility/
```

Each `cams/*.txt` file stores camera intrinsics and world-to-camera extrinsics in the OpenCV camera convention: **x right, y down, z forward**.

---

## Fine-tuning and Evaluation Protocol

A3D-Bench evaluates feed-forward reconstruction models under a unified dense multi-view protocol.

### Model settings

The benchmark supports both image-only and prior-aware settings:

| Setting | Input |
| --- | --- |
| **RGB** | Images only |
| **C** | Images + camera intrinsics |
| **P** | Images + camera poses |
| **CP** | Images + camera intrinsics + camera poses |

### Evaluation targets

The evaluation is geometry-aware and measures multiple aspects of reconstruction quality:

- depth accuracy,
- camera-ray consistency,
- camera pose accuracy,
- rotation accuracy,
- dense point-cloud / geometry accuracy,
- scene-level camera--geometry consistency.

### Fine-tuning

The fine-tuning framework supports UAV-domain adaptation using A3D training data and related UAV/MVS sources. The training pipeline is based on a unified multi-view data interface so that different feed-forward models can be evaluated under comparable sampling and preprocessing settings.

---

## Quick Start

### Dataset pipeline

```bash
git clone https://github.com/yanxian-ll/a3dbench_pepeline.git
cd a3dbench_pepeline
pip install -r requirements.txt
```

Please refer to the repository documentation for synthetic rendering, LiDAR--SfM alignment, and unified-format conversion.

### Fine-tuning and evaluation

```bash
git clone https://github.com/yanxian-ll/a3dbench_evaluate_forward_models.git
cd a3dbench_evaluate_forward_models

conda create -n a3dbench python=3.12 -y
conda activate a3dbench

pip install -e .
```

Before running evaluation or training, update local dataset paths and machine-specific paths in the corresponding config files.

---

## Citation

If you use A3D-Bench, the construction pipeline, or the evaluation framework, please cite the paper. The final BibTeX entry will be added after publication.

```bibtex
@misc{a3dbench2026,
  title  = {A3D-Bench: A Geometry-Aware Benchmark for Feed-Forward UAV 3D Reconstruction},
  author = {A3D-Bench Authors},
  year   = {2026},
  note   = {Dataset, pipeline, and benchmark framework}
}
```

---

## License

The dataset, code, third-party assets, and model checkpoints may be released under different licenses.

- The dataset license will be added with the public dataset release.
- The data construction pipeline follows the license of its repository.
- The model fine-tuning and evaluation framework follows the license of its repository.
- Third-party datasets, pretrained models, and textured 3D assets remain subject to their original licenses.

Please check the corresponding license files before redistribution or commercial use.

---

## Acknowledgements

A3D-Bench builds on prior work in photogrammetric reconstruction, UAV mapping, synthetic data generation, and feed-forward 3D reconstruction. We thank the authors and maintainers of the datasets, models, and open-source tools used in this benchmark.
