# igvf_sc-islet_10X-Multiome — GCS Bucket Schema (v1 / v2 / v3)

**GCP project:** beta-cell-networks (`vocal-lead-490915-t9`)
**Bucket:** `gs://igvf-data/igvf_sc-islet_10X-Multiome/`
**Account:** `aklie@ucsd.edu`

## Dataset overview

10X Multiome (RNA + ATAC) from stem-cell-derived islets (SC-islets) treated
with metabolic and inflammatory stressors.

- **Cell lines:** H1, A2
- **Conditions:** control, 3-cyt, IFNg, palmitate, dex, Ex-4_HG
- **Timepoints:** 0, 6, 24, 48, 72 hours
- **Replicates:** up to 3
- **Provenance:** https://github.com/IGVF-UCSD/igvf_sc-islet_10X-Multiome

### Cell types

**Round 1 (all cell types):** SC.alpha, SC.beta, SC.delta, SC.EC, proliferating, progenitor, stressed-endocrine

**Round 2 (filtered endocrine):** SC.alpha, SC.beta, SC.delta, SC.EC

Round 1 includes all cells from the initial Harmony integration. Round 2 is a
re-clustering of filtered endocrine cell types only, used for downstream
pseudobulk, differential analysis, and peak calling.

---

## Versioning

Three analysis versions are uploaded as parallel subtrees. Reference data
(`0_ref/`) is shared. Pipeline-derived outputs (cell-by-X matrices, pseudobulks)
are version-specific because they depend on the upstream processing.

| Version | Pipeline | Samples | Notes |
|---|---|---|---|
| v1 | CellRanger-ARC + YAP | 44 | prototype; partial DM060 batch missing |
| v2 | CellRanger-ARC + YAP | 54 | production v2 (DM060 batch added) |
| v3 | IGVF uniform pipeline (kallisto-bustools + chromap) | 54 | current production |

**Naming convention.** File names use `-` (not `+`) between covariates in
compound groupings (e.g. `cell_type-condition`, `cell_type-condition-timepoint`,
`sample_id-cell_type`). v1 outputs that use `+` in the HPC scratch tree are
renamed to `-` at upload time — the bucket never contains `+`.

**Out of scope (deferred — not yet formally finalized in any version):**
- Peak BED files (`narrowPeak`, `consensus_peak.bed`, `peak_sources.bed`).
- Differential expression / accessibility results (DESeq2 outputs).

These will be added once their methodology is settled (issues #4, #5).

---

## Bucket structure

```
gs://igvf-data/igvf_sc-islet_10X-Multiome/
├── 0_ref/                              # shared reference data (all versions)
├── v1/                                 # CellRanger-ARC + YAP, 44 samples
│   ├── sample_manifest.tsv
│   ├── 1_cell-by-X/
│   └── 2_pseudobulk/atac/
├── v2/                                 # CellRanger-ARC + YAP, 54 samples
│   ├── sample_manifest.tsv
│   ├── 1_cell-by-X/
│   └── 2_pseudobulk/atac/
└── v3/                                 # IGVF uniform pipeline, 54 samples
    ├── sample_manifest.tsv
    ├── 1_cell-by-X/
    ├── 2_pseudobulk/atac/
    └── 2_pseudobulk/rna/               # new in v3
```

---

## 0_ref/ — Reference data (shared across versions)

Files used across analyses. Sourced from `ref/` and `results/1_get_data/` on
the HPC.

### SC-islet gene markers

**Files**
- `SC.islet.marker_genes.csv`

### ENCODE blacklist

**Files**
- `blacklist.bed.gz`

### Gene annotation

**Files**
- `IGVFFI9573KOZR.gtf.gz`

### Motif database

**Files**
- `motifs.meme` — Vierstra v2.0beta motif clustering catalog.

### Color palettes

**Files**
- `cellid_colors.tsv` — Cell type color palette (canonical: `igvf_sc-islet_10X-Multiome/config/cell_type_metadata.tsv`).
- `condition_colors.tsv` — Condition color palette (canonical: `config/condition_metadata.tsv`).

### Barcode whitelists (v1 / v2 only — CellRanger-ARC)

**Files**
- `737K-arc-v1.txt.gz` — 10X barcode whitelist (Multiome ARC).

### CellRanger ARC config templates (v1 / v2 only)

**Files**
- `10x_multiome_ATAC.yaml.j2`
- `10x_multiome_RNA.yaml.j2`

### Other reference files

**Files**
- `sc_islet_genelist.txt` — curated SC-islet gene list.
- `regev_lab_cell_cycle_genes.txt` — cell-cycle gene list (S and G2M phase).
- `GRCh38_TCF7L2_ZFAND3.tsv` — TCF7L2 / ZFAND3 locus annotation.
- `GSE145643_MIN6_MPRA_Baseline_FinalMatrix.out.txt.gz` — MIN6 MPRA baseline (Chiou et al.).
- `IEP_annotation_gnomadV3_snps.bed.gz` — Islet enhancer primed SNP annotations (gnomAD v3).

---

## sample_manifest.tsv (per version, lives at `v{N}/sample_manifest.tsv`)

The canonical sample-level metadata for the version. Per-version because
sample sets and identifiers differ across pipelines.

**Specification (common columns)**
- sample_id: str — internal sample identifier (e.g. `dm11a`, `45-1`)
- batch: str — differentiation batch identifier (e.g. `JE002`, `DM060`)
- condition: str ["control", "3-cyt", "IFNg", "Ex-4_HG", "palmitate", "dex"]
- timepoint: int [0, 6, 24, 48, 72] — hours
- rep: int [1, 2, 3]
- cell_line: str ["H1", "A2"]
- multiome_qc_status: str — sample-level QC status
- notes: str — freeform

**Version-specific additions**
- v3 only — `igvfds_id`: str — IGVF data submission accession (e.g. `IGVFDS1093WHDR`).
- v3 only — `analysis_set_accession`: str — IGVF analysis set accession.

**Row counts**
- v1: 44 rows
- v2: 54 rows
- v3: 54 rows

---

## 1_cell-by-X/ — Single-cell matrices (per version)

### Cell × gene UMI count matrices (h5ad)

**Files**

v1 (per-celltype + endocrine combined):
- `cell_x_gene.soupx_umi_counts.endocrine_celltypes.h5ad` — round 2 endocrine combined (the canonical object)
- `cell_x_gene.soupx_umi_counts.SC.alpha.h5ad` — SC.alpha only
- `cell_x_gene.soupx_umi_counts.SC.beta.h5ad` — SC.beta only
- `cell_x_gene.soupx_umi_counts.SC.delta.h5ad` — SC.delta only
- `cell_x_gene.soupx_umi_counts.SC.EC.h5ad` — SC.EC only

v2 (round 2 endocrine, three flavors):
- `cell_x_gene.soupx_umi_counts.endocrine_celltypes.annotated.h5ad` — final annotated (round 2)
- `cell_x_gene.soupx_umi_counts.endocrine_celltypes.slim.h5ad` — counts + metadata only (no intermediate layers)
- `cell_x_gene.soupx_umi_counts.endocrine_celltypes.full.h5ad` — full object with all computed layers

v3 (kallisto-bustools UMI counts; round 2 endocrine):
- `cell_x_gene.umi_counts.endocrine_celltypes.annotated.h5ad`
- `cell_x_gene.umi_counts.endocrine_celltypes.slim.h5ad`
- `cell_x_gene.umi_counts.endocrine_celltypes.full.h5ad`

(v3 drops `soupx_` from the filename — kallisto-bustools doesn't apply SoupX.)

**Data Matrix**
- .X: sparse matrix — UMI counts (SoupX-corrected for v1/v2; uncorrected kallisto-bustools for v3)

**Observations (.obs)**

Metadata for cells (rows in .X):
- barcode (index): str `{sample}#{10x_gex_barcode}` — unique cell ID combining sample and 10x GEX barcode (includes trailing `-1`)
- gex_barcode: str — 10x GEX barcode
- atac_barcode: str — 10x ATAC barcode
- sample: str — sample identifier
- batch: str — differentiation batch identifier
- condition: str ["control", "3-cyt", "IFNg", "Ex-4_HG", "palmitate", "dex"]
- timepoint: int [0, 6, 24, 48, 72]
- rep: int [1, 2, 3]
- cluster: int — cluster ID defining cell type
- celltype: str ["SC.alpha", "SC.beta", "SC.delta", "SC.EC"] — round 2 endocrine
- multiome_stage: str (v3 only)

**Variables (.var)**
- index: str — HUGO gene symbol

**Observation-wise multidimensional arrays (.obsm)**
- X_pca: array — PCA coordinates
- X_harmony: array — Harmony-corrected PCA coordinates
- X_umap: array — UMAP coordinates

### Cell × peak Tn5 insertion count matrices (h5ad)

**Files**

v1:
- `cell_x_peak.Tn5_insertion_counts.endocrine_celltypes.h5ad`

v2:
- `cell_x_peak.Tn5_insertion_counts.endocrine_celltypes.h5ad`

v3 — _TODO_: not yet ported. Will land once stage-5 peak calling completes.

**Data Matrix**
- .X: sparse matrix — Tn5 insertion counts

**Observations (.obs)** — same schema as cell × gene above.

**Variables (.var)**
- peak (index): str `{chr}:{start}-{end}` — peak identifier

**Observation-wise multidimensional arrays (.obsm)**
- X_pca, X_harmony, X_umap

---

## 2_pseudobulk/atac/ — Pseudobulk ATAC (per version, per grouping)

Pseudobulk aggregations of ATAC fragments at one or more grouping levels.
Generated via SnapATAC2 fragment export and downstream tagAlign / bigwig
post-processing.

**Provenance**
- SnapATAC2 export fragments — https://kzhang.org/SnapATAC2/api/_autosummary/snapatac2.ex.export_fragments.html
- DecoupleR-based pseudobulking helper — https://github.com/adamklie/single_cell_utilities/blob/dev/functional_analysis/pseudobulk.py
- tagAlign generation — https://github.com/adamklie/single_cell_utilities/blob/dev/fragments/frag_to_tagAlign.sh
- Unnormalized bigwig — https://github.com/adamklie/single_cell_utilities/blob/dev/fragments/frag_to_count_bw.sh
- FPM-normalized bigwig — https://github.com/adamklie/single_cell_utilities/blob/dev/fragments/frag_to_norm_bigWig.sh

### Groupings present per version

| Version | Groupings under `2_pseudobulk/atac/` |
|---|---|
| v1 | `cell_type/`, `cell_type-condition/`, `cell_type-condition-timepoint/` |
| v2 | `cell_type/`, `cell_type-condition/` |
| v3 | `cell_type-condition/` (additional groupings to be added per #1's multi-resolution plan) |

### Per-grouping layout

Each grouping subdirectory has the same four sub-folders. Within each sub-folder
files are named after the pseudobulk group: `{name}.bed.gz`, `{name}.tagAlign.sort.gz`,
`{name}_unstranded.bw`, `{name}.fpm.bw`. For `cell_type/`, `{name}` is e.g. `SC.beta`;
for `cell_type-condition/`, e.g. `SC.beta_control`; for `cell_type-condition-timepoint/`,
e.g. `SC.beta_control_72`.

#### fragments/

**Files**
- `fragments/{name}.bed.gz` — sorted fragments per group
- `fragments/frag_counts.tsv` — fragment count summary per group
- `fragments/fragment_file_paths.tsv` — file path index

**Specification**
https://www.10xgenomics.com/support/software/cell-ranger-arc/latest/analysis/outputs/fragments-file

#### tagAlign/

**Files**
- `tagAlign/{name}.tagAlign.sort.gz` — sorted tagAlign per group
- `tagAlign/{name}.tagAlign.sort.gz.tbi` — tabix index
- `tagAlign/tagAlign_file_paths.tsv` — file path index

**Specification**
tagAlign files are special versions of BED files where each Tn5 insertion "tag"
is its own entry (line). tagAligns store the "left" fragment end on the +
strand, and the "right" fragment end on the - strand. The name field is left as
"N" and the score field is 1000 for all lines.

- chr
- start
- end
- name
- score
- strand

#### count_bws/

**Files**
- `count_bws/{name}_unstranded.bw` — unnormalized Tn5 insertion count bigwig
- `count_bws/count_bw_file_paths.tsv` — file path index

#### norm_bws/

**Files**
- `norm_bws/{name}.fpm.bw` — FPM-normalized bigwig
- `norm_bws/{name}.scale_factor.txt` — `1e6 / fragment_count` scale factor used
- `norm_bws/norm_bw_file_paths.tsv` — file path index

---

## 2_pseudobulk/rna/ — Pseudobulk RNA (v3 only currently)

RNA pseudobulk gene-count matrices grouped by sample × cell type. Used for
downstream DESeq2 / pattern analysis (issue #4). Out of scope for v1 / v2 in
this spec — those versions' DE work used HPC-side per-cell-type pseudobulks
that pre-date this canonical layout.

**Provenance**
- DecoupleR-based pseudobulking — https://github.com/adamklie/single_cell_utilities/blob/dev/functional_analysis/pseudobulk.py

### Groupings present

| Version | Groupings under `2_pseudobulk/rna/` |
|---|---|
| v3 | `sample_id-cell_type/` (more groupings TBD per multi-resolution plan) |

### Per-grouping layout

Each grouping subdirectory holds an `h5ad` AnnData object plus a flat-file
TSV mirror that's easier for R / shell consumers.

**Files**
- `{grouping}/pseudobulk_no_filter.h5ad` — AnnData; rows = pseudobulks, cols = genes
- `{grouping}/pseudobulk_no_filter.tsv` — gene × pseudobulk count matrix (TSV)
- `{grouping}/pseudobulk_no_filter_metadata.csv` — per-pseudobulk metadata
- `{grouping}/pseudobulk_filter.h5ad` — same layout, post-filter (low-count pseudobulks dropped)
- `{grouping}/pseudobulk_filter.tsv`
- `{grouping}/pseudobulk_filter_metadata.csv`

**Data Matrix (`pseudobulk_no_filter.h5ad`)**
- .X: dense int matrix — summed UMI counts (SoupX-corrected for v1/v2 when those land here; uncorrected kallisto-bustools for v3)
- shape: (n_pseudobulks, n_genes)

**Observations (.obs) — pseudobulk metadata**
- pseudobulk (index): str `{sample_id}_{cell_type}` — unique pseudobulk identifier
- sample_id: str
- cell_type: str
- batch / differentiation_batch: str
- condition: str
- timepoint: int
- rep: int
- psbulk_cells: int — number of cells contributing
- psbulk_counts: int — total counts contributing

**Variables (.var)**
- index: str — HUGO gene symbol

---

## HPC source paths

Per-version source paths on the HPC. Upload tooling (`bin/{N}_upload/`) reads
from these and writes to the corresponding bucket subtree.

```
/cellar/users/aklie/data/datasets/igvf_sc-islet_10X-Multiome/

# shared
├── ref/                                                             → 0_ref/

# v1 (CellRanger-ARC + YAP, 44 samples)
├── archive/v1/results/9_upload/cell-by-X/                           → v1/1_cell-by-X/
├── archive/v1/results/4_integration/atac/pseudobulk/                → v1/2_pseudobulk/atac/
└── archive/v1/results/3_sample_qc/sample_metadata.tsv               → v1/sample_manifest.tsv  (44 rows)

# v2 (CellRanger-ARC + YAP, 54 samples)
├── archive/v2/results/4_integration/rna/integrate/round_2/{slim,full,annotation}.h5ad   → v2/1_cell-by-X/
├── archive/v2/results/4_integration/atac/pseudobulk/{cell_type,cell_type-condition}/    → v2/2_pseudobulk/atac/
└── archive/v2/results/1_get_data/sample_metadata.tsv                → v2/sample_manifest.tsv  (54 rows)

# v3 (IGVF uniform pipeline, 54 samples)
├── results/3_cell_annotation/rna/integrate/round_2/{slim,full,merge_annotated}.h5ad     → v3/1_cell-by-X/
├── results/4_pseudobulking/atac/cell_type-condition/                → v3/2_pseudobulk/atac/cell_type-condition/
├── results/4_pseudobulking/rna/sample_id-cell_type/                 → v3/2_pseudobulk/rna/sample_id-cell_type/
└── bin/1_get_data/metadata/igvfds_to_sample_id.tsv                  → v3/sample_manifest.tsv  (54 rows + IGVFDS_id columns)
```

Upload tooling: `archive/v{1,2}/bin/9_upload/` (ad-hoc notebooks); `bin/10_submission/`
once the v3 upload notebook is written.
