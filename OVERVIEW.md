# Beta Cell Networks — Project Overview

## Goal

Characterize the transcriptomic and epigenomic responses of stem-cell-derived
islet (SC-islet) cell types to metabolic and inflammatory stressors using
10X Multiome (RNA + ATAC) and snm3C-seq (methylation + chromatin conformation).

## Analysis versions

### v1 — Prototype (44 samples)

Multiome processed with CellRanger-ARC
snm3C-seq processed with YAP. 
Served as the prototype for all downstream analysis steps (pseudobulk, 
differential expression, peak calling, sequence modeling). 
Missing DM060 batch and some dex/Ex-4_HG samples.

- **Cluster:** `igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_01_30` (sample QC, integration, peak analysis, ChromBPNet)
- **Cluster:** `igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_02_02` (refined peak calling, differential analysis, ChromBPNet, upload)
- **GCP:** [gs://2025_01_06](https://console.cloud.google.com/storage/browser/2025_01_06) (project: igvf-browser-tracks)

### v2 — Refined (54 samples, current as of 03/21/2026)

Added 10 additional samples (DM060 batch, complete dex and Ex-4_HG coverage)
and refined many analysis steps. Still uses CellRanger-ARC and YAP for
preprocessing. Improved pseudobulk DESeq2 methodology, updated SnapATAC2
peak calling, and restructured upload pipeline for new GCP project.

- **Cluster:** `igvf-data/igvf_sc-islet_10X-Multiome/bin/` (analysis code)
- **Cluster:** `igvf-data/igvf_sc-islet_10X-Multiome/results/` (outputs)
- **GCP:** `gs://igvf-data/igvf_sc-islet_10X-Multiome/` (project: beta-cell-networks / `vocal-lead-490915-t9`)

### v3 — IGVF uniform pipeline (planned)

Use IGVF single-cell uniform processed data as input and repeat downstream
analysis steps. Replaces CellRanger-ARC + YAP with the standardized IGVF
preprocessing pipeline.
