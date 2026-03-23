# Data

## Multiome

### v1 (44 samples)

First analysis version. Prototype of all downstream steps.

- **Samples:** 44 total
- **Conditions:** control (no DM060), 3-cyt, IFNg, palmitate, dex (3 samples), Ex-4_HG
- **Preprocessing:** CellRanger-ARC + YAP
- **Spec:** [IGVF Beta Cell Networks File Specs](https://docs.google.com/document/d/16geh0GFWYjXUECTY1wqGKrw0f4f1BDdsAsMwJWZSU5U/edit?tab=t.0#heading=h.rguznvyoqgpb)
- **Cluster (code + results):**
  - `igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_01_30` — sample QC, integration, peak analysis, joint analysis, ChromBPNet
  - `igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_02_02` — refined peak calling, differential analysis, ChromBPNet, upload prep
- **GCP:** [gs://2025_01_06](https://console.cloud.google.com/storage/browser/2025_01_06) (project: igvf-browser-tracks)

### v2 (54 samples)

Added 10 samples and refined analysis steps. Current as of 03/21/2026.

- **Samples:** 54 total
- **Conditions:** control (DM060 included), 3-cyt, IFNg, palmitate, dex (all samples), Ex-4_HG
- **Preprocessing:** CellRanger-ARC + YAP
- **Cluster (code):** `igvf-data/igvf_sc-islet_10X-Multiome/bin/`
- **Cluster (results):** `igvf-data/igvf_sc-islet_10X-Multiome/results/`
- **GCP:** `gs://igvf-data/igvf_sc-islet_10X-Multiome/` (project: beta-cell-networks / `vocal-lead-490915-t9`)
- **Schema:** [SCHEMA.md](igvf-data/igvf_sc-islet_10X-Multiome/SCHEMA.md)

### v3 (planned)

Reprocess with IGVF single-cell uniform pipeline and repeat downstream analysis.

## snm3C-seq

_Coming soon_
