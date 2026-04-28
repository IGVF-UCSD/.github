# Data

## Multiome

### v3 (current — IGVF uniform pipeline, 54 samples)

Promoted from scratch to canonical `bin/`/`results/` on 2026-04-28.

- **Samples:** 54 total
- **Conditions:** control (DM060 included), 3-cyt, IFNg, palmitate, dex (all samples), Ex-4_HG
- **Preprocessing:** IGVF single-cell uniform pipeline (kallisto-bustools + chromap)
- **Cluster (code):** `igvf-data/igvf_sc-islet_10X-Multiome/bin/` — see [bin/README.md](../igvf-data/igvf_sc-islet_10X-Multiome/bin/README.md) for stage status
- **Cluster (results):** `igvf-data/igvf_sc-islet_10X-Multiome/results/` (448 GB)
- **Stages migrated:** 1_get_data, 2_sample_qc, 3_cell_annotation (RNA side; `4_cell_annotation.ipynb` finish pending)
- **Stages NOT yet ported from v2:** 4 (ATAC integration), 5 (peak calling), 6 (DESeq2), 8 (ChromBPNet), 9 (upload), 10 (submission). v2 reference at `archive/v2/bin/`.
- **Cross-version comparison:** v2-vs-v3 plots and notebooks at `igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_04_09/v3_uniform_pipeline/4_comparison/` (left in scratch as archival)
- **GCP:** _TODO_ — bucket layout for v3 ties to issue #6

### v2 (archived — CellRanger-ARC + YAP, 54 samples)

Frozen 2026-04-28. Was the production pipeline 2026-03-21 → 2026-04-16.

- **Samples:** 54 total
- **Conditions:** control (DM060 included), 3-cyt, IFNg, palmitate, dex (all samples), Ex-4_HG
- **Preprocessing:** CellRanger-ARC + YAP
- **Cluster (code):** `igvf-data/igvf_sc-islet_10X-Multiome/archive/v2/bin/` (git history preserved)
- **Cluster (results):** `igvf-data/igvf_sc-islet_10X-Multiome/archive/v2/results/` (1.9 TB, gitignored)
- **Manifest:** [archive/v2/MANIFEST.md](../igvf-data/igvf_sc-islet_10X-Multiome/archive/v2/MANIFEST.md)
- **GCP:** `gs://igvf-data/igvf_sc-islet_10X-Multiome/` (project: beta-cell-networks / `vocal-lead-490915-t9`). Bucket reflects pre-archive layout.
- **v2-era exploratory scratch:** `scratch/2026_02_28/` (92 GB analysis-set snapshot), `scratch/2026_03_*/`, `scratch/2026_04_06/` — left in place

### v1 (archived — CellRanger-ARC + YAP, 44 samples)

Prototype. Lives in scratch (3.0 TB), not physically moved.

- **Samples:** 44 total
- **Conditions:** control (no DM060), 3-cyt, IFNg, palmitate, dex (3 samples), Ex-4_HG
- **Preprocessing:** CellRanger-ARC + YAP
- **Manifest with path map:** [archive/v1/MANIFEST.md](../igvf-data/igvf_sc-islet_10X-Multiome/archive/v1/MANIFEST.md)
- **Spec:** [IGVF Beta Cell Networks File Specs](https://docs.google.com/document/d/16geh0GFWYjXUECTY1wqGKrw0f4f1BDdsAsMwJWZSU5U/edit?tab=t.0#heading=h.rguznvyoqgpb)
- **Canonical workspace:** `igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_01_30/` (sample QC → joint analysis → upload prep)
- **Supplementary:** `igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_02_02/` (refined peak calling, DESeq2 notebooks)
- **GCP:** [gs://2025_01_06](https://console.cloud.google.com/storage/browser/2025_01_06) (project: igvf-browser-tracks)

## snm3C-seq

_Coming soon_
