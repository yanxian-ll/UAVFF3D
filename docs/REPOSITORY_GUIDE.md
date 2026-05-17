# Repository Compatibility Guide

UAVFF3D consists of three related repositories.

## Main Project Repository

```text
https://github.com/yanxian-ll/UAVFF3D
```

This repository is the main project page. It hosts the project overview, figure assets, citation placeholder, and release notes.

## Data Pipeline Repository

```text
https://github.com/yanxian-ll/UAVFF3D-Pipeline
```

This repository builds and converts data into the UAVFF3D common format. It should use the same dataset names as this main project page:

```text
UAVFF3D-Real
UAVFF3D-Syn
UAVFF3D-FA
```

## Fine-tuning and Evaluation Repository

```text
https://github.com/yanxian-ll/uavff3d_evaluate_forward_models
```

This repository contains the MapAnything-based training and benchmark code for the paper experiments. It should keep the following paper-related bash entry points:

```text
bash_scripts/train/uav_finetuning
bash_scripts/benchmark/uav_dense_n_view
```

It should also keep the third-party integrations that are required by the experiments, including HunyuanWorld-Mirror and Depth Anything 3.

## Naming Rules

Use these names in new code, configs, documentation, and release files:

```text
UAVFF3D
UAVFF3D-Real
UAVFF3D-Syn
UAVFF3D-FA
uavff3d
```

Use the UAVFF3D naming convention consistently in new files.
