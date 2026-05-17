# UAVFF3D Release Checklist

Use this checklist before publishing the dataset, code, checkpoints, or project page.

## Main Project Page

- [ ] README uses `UAVFF3D` consistently.
- [ ] Overview figure renders correctly on GitHub.
- [ ] Dataset links point to the final release location.
- [ ] Citation metadata is updated after paper publication.
- [ ] License and third-party notices are complete.

## Data Pipeline Repository

- [ ] Public scripts and documentation use the UAVFF3D naming convention consistently.
- [ ] Example configs use `UAVFF3D-Real`, `UAVFF3D-Syn`, and `UAVFF3D-FA`.
- [ ] Private absolute paths are removed.
- [ ] Dataset conversion examples are reproducible.
- [ ] Generated metadata and split files match the public release.

## Fine-tuning and Evaluation Repository

- [ ] Paper-related bash scripts are preserved under `bash_scripts/train/uav_finetuning` and `bash_scripts/benchmark/uav_dense_n_view`.
- [ ] Unrelated demo/profiling scripts are removed or clearly separated.
- [ ] Required third-party integrations are retained and documented.
- [ ] Model wrappers and configs use the same dataset naming convention.
- [ ] Evaluation outputs report AbsRel, Ray Error, Pose ATE, Rotation MAE, and Chamfer-L1 consistently.

## Dataset and Checkpoints

- [ ] Dataset license is specified.
- [ ] Third-party assets and model licenses are listed.
- [ ] Checksums are provided for large archives.
- [ ] Checkpoint download instructions are tested.
- [ ] Data splits are fixed and documented.
