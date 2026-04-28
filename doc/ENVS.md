# Environments

Inventory of the Python venvs and conda envs used across this project, with the scripts that expect each one. All conda envs live under `/cellar/users/aklie/opt/miniconda3/envs/`.

When running a one-off script, invoke the env's interpreter directly (e.g. `/cellar/users/aklie/opt/miniconda3/envs/scverse-lite-py39/bin/python ...`) — `conda activate` from a non-interactive shell often fails silently and falls back to `/usr/bin/python`, which has none of the project packages.

## Quick picker

| Task | Env |
|---|---|
| Ad-hoc plotting, pandas, matplotlib, quick notebooks | `scverse-lite-py39` (default), `scverse-lite-py311` |
| Multiome sample QC (RNA, AMULET, merge_RNA, joint-integrate) | `cellcommander` |
| Multiome sample QC (ATAC in v2/v3) | `scverse-lite-py39` (v2), `scverse-lite-py311` (v3) |
| ATAC merge across samples | `scverse-lite-py311` |
| DESeq2 / pseudobulk differential analysis (R) | `seqtools-R443` |
| ChromBPNet model training, bias, predictions, contributions | `chrombpnet` |
| ChromBPNet averaging, TF-MoDISco motif discovery, averaged bigwigs | `eugene_tools` |
| FiNeMo motif-hit calling | `finemo_gpu` |
| Seurat / Harmony integration in R | `seurat_v5` |
| IGVF data portal interactions | `igvf_data` |
| Nextflow pipeline orchestration | `nextflow` |
| snm3C (schicluster) | `schicluster` / `schicluster_dev` |

## Conda envs (primary)

### `cellcommander`
- **Used by:** all `pipelines/multiome-sample-qc/src/scripts/*.sh` (RNA QC, AMULET, joint-integrate), `pipelines/multiome-cell-annotation/src/scripts/merge_RNA.sh`.
- **Includes:** `cellcommander` as an editable install (`pip install -e tools/CellCommander/`). If the editable install breaks after a project move, re-run the `pip install -e` from this env.
- **Stack:** scanpy + anndata + snapatac2 + rpy2 (for SCTransform bridge).

### `scverse-lite-py39`
- **Used by:** `pipelines/multiome-sample-qc/src/scratch/qc_ATAC.sh` (v2 path), default for ad-hoc analysis scripts under `4_comparison/`.
- **Stack:** pandas 2.1, matplotlib 3.9, scanpy, snapatac2. Works for the Jaccard/overlap plots and most comparison figures.

### `scverse-lite-py311`
- **Used by:** `pipelines/multiome-sample-qc/src/scripts/qc_ATAC.sh` (v3 path), `pipelines/multiome-cell-annotation/src/scripts/merge_ATAC.sh`.
- **Stack:** newer scanpy / snapatac2; pin is scipy 1.12 so snapatac2 2.8's scrublet path doesn't hit the removed `Series.nonzero()` in scipy 1.15.

### `chrombpnet`
- **Used by:** every script under `igvf-data/.../bin/8_chrombpnet/scripts/{bias_pipeline,chrombpnet_pipeline,negatives,predictions,contributions}/*.sh` that trains or runs raw ChromBPNet.
- **Stack:** chrombpnet package + tensorflow + pyBigWig. Training uses GPU; ensure `CUDA_VISIBLE_DEVICES` is set from the SLURM template.

### `eugene_tools`
- **Used by:** ChromBPNet **averaged** predictions/contributions, and motif discovery (`scripts/motifs/cell_type-condition_motif_discovery.sh`).
- **Stack:** modiscolite + utility scripts for averaging `.h5` contribution stacks across folds.

### `seqtools-R443`
- **Used by:** `igvf-data/.../bin/6_differential_analysis/scripts/deseq_*.sh`.
- **Stack:** R 4.4.3 + DESeq2 + pseudobulk utilities.

### `finemo_gpu`
- **Used by:** `manuscripts/ChromBetaNet/bin/7_motif2cRE_linking/3_call_hits.sh` (also sets `LD_LIBRARY_PATH` to include this env's `lib/`).

### `seurat_v5`
- **Used by:** R integration / Harmony comparisons in `manuscripts/beta_cell_networks/scratch/`.

### `igvf_data`
- **Used by:** data portal submission/download utilities; see `igvf-data/PORTAL.md` and `SUBMISSION.md`.

## Python venvs (uv-managed)

- **`gene-networks/`** — managed with `uv`. Installs `gsa` (from `tools/gene-set-analysis`) as a local editable dependency. Use `uv sync` then `uv run ...` from inside `gene-networks/`.

## Gotchas

- **`source activate` in non-interactive shells** often doesn't swap `$PATH`; `which python` stays `/usr/bin/python`. Prefer the explicit env-python path for one-off scripts and subprocess invocations.
- **pandas ModuleNotFoundError from a `bare python` call** is almost always the symptom above — retry with the env's interpreter.
- **scipy 1.15 breaks snapatac2 2.8 scrublet** (`Series.nonzero()` removed). `scverse-lite-py311` pins scipy 1.12, which is compatible with both snapatac2 2.8 and macs3 3.0.
- **cellcommander editable install** can break when the repo is relocated; re-run `pip install -e tools/CellCommander/` inside the `cellcommander` env to restore.

Update this file when a new env becomes load-bearing or an existing env changes role.
