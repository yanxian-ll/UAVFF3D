# UAVFF3D Dataset Guide

This document summarizes the dataset components and the local directory layout expected by the companion code repositories.

## Download

All UAVFF3D dataset components are provided through one Baidu Netdisk share:
[UAVFF3D](https://pan.baidu.com/s/1dtJSMww0NXRS1Wv2qOFhMA?pwd=1234), extraction code: `1234`.

## Components

### UAVFF3D-Real

UAVFF3D-Real contains real UAV image blocks and real-world evaluation scenes. It includes reconstruction-based supervision and LiDAR-supported test scenes where LiDAR point clouds are aligned to the SfM coordinate system and rendered into image-aligned reference depth maps.

### UAVFF3D-Syn

UAVFF3D-Syn contains controllable synthetic UAV data rendered from textured 3D assets. It is used to expand UAV camera-geometry coverage during domain adaptation, including hFOV variation, altitude variation, nadir trajectories, oblique trajectories, and irregular local trajectories.

### UAVFF3D-FA

UAVFF3D-FA is a controlled hFOV-height ambiguity benchmark. It varies hFOV and flight height while keeping the observed image footprint approximately comparable. This split is reserved for evaluation only and should not be used for fine-tuning.

## Recommended Local Layout

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

The same directory names are used by the data processing pipeline and the model fine-tuning/evaluation repository.

## Common Scene Format

```text
scene/
  images/
  cams/
  depth/
  mask/
  scene_meta.json
```

Optional directories may include `depth_lidar`, `depth_sfm`, `depth_complete`, `semantic_mask`, and `covisibility`.

## Camera Convention

Camera files use the OpenCV camera convention: x right, y down, z forward. World-to-camera extrinsics and intrinsics should be exported consistently across real, LiDAR-supported, and synthetic scenes.
