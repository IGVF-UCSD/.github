# Beta Cell Networks — Project Overview

## Goal

Characterize the transcriptomic and epigenomic responses of stem-cell-derived
islet (SC-islet) cell types to metabolic and inflammatory stressors using
10X Multiome (RNA + ATAC) and snm3C-seq (methylation + chromatin conformation).

## Analysis versions

### v3 — IGVF uniform pipeline (current as of 2026-04-28, 54 samples)

Use IGVF single-cell uniform processed data as input and repeat downstream
analysis steps. Replaces CellRanger-ARC + YAP with the standardized IGVF
preprocessing pipeline (kallisto-bustools + chromap). Promoted from scratch
to canonical `bin/`/`results/` on 2026-04-28.

- **Cluster (code):** `igvf-data/igvf_sc-islet_10X-Multiome/bin/` — see `bin/README.md` for stage-by-stage status
- **Cluster (results):** `igvf-data/igvf_sc-islet_10X-Multiome/results/` (448 GB)
- **Stages migrated:** 1_get_data, 2_sample_qc, 3_cell_annotation (RNA side; `4_cell_annotation.ipynb` finish pending)
- **Stages NOT yet ported from v2:** 4 (ATAC integration), 5 (peak calling), 6 (DESeq2), 8 (ChromBPNet), 9 (upload), 10 (submission)
- **Cross-version comparison:** `scratch/2026_04_09/v3_uniform_pipeline/4_comparison/` (archival, tied to 2026-04-16 v2→v3 prez)

### v2 — Archived (CellRanger-ARC + YAP, 54 samples, frozen 2026-04-28)

Production pipeline 2026-03-21 → 2026-04-16. Improved pseudobulk DESeq2 methodology, updated SnapATAC2 peak calling, restructured upload pipeline for new GCP project. Frozen ahead of v3 promotion.

- **Cluster (code):** `igvf-data/igvf_sc-islet_10X-Multiome/archive/v2/bin/` (git history preserved)
- **Cluster (results):** `igvf-data/igvf_sc-islet_10X-Multiome/archive/v2/results/` (1.9 TB)
- **Manifest:** `igvf-data/igvf_sc-islet_10X-Multiome/archive/v2/MANIFEST.md`
- **GCP:** `gs://igvf-data/igvf_sc-islet_10X-Multiome/` (project: beta-cell-networks / `vocal-lead-490915-t9`)

### v1 — Prototype (CellRanger-ARC + YAP, 44 samples, archived in place)

Prototype for all downstream steps (pseudobulk, differential expression, peak
calling, sequence modeling). Missing DM060 batch and some dex/Ex-4_HG samples.
Lives in scratch (3.0 TB), not physically moved — `archive/v1/MANIFEST.md` is a
pointer-only index.

- **Manifest:** `igvf-data/igvf_sc-islet_10X-Multiome/archive/v1/MANIFEST.md`
- **Canonical workspace:** `igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_01_30/` (sample QC, integration, peak analysis, joint analysis, ChromBPNet, upload prep)
- **Supplementary:** `igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_02_02/` (refined peak calling, differential analysis notebooks)
- **GCP:** [gs://2025_01_06](https://console.cloud.google.com/storage/browser/2025_01_06) (project: igvf-browser-tracks)
