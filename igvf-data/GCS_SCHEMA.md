# igvf_sc-islet_10X-Multiome — GCS Bucket Schema (v1 / v2 / v3)

**GCP project:** beta-cell-networks (`vocal-lead-490915-t9`)
**Bucket:** `gs://igvf-data/igvf_sc-islet_10X-Multiome/`
**Account:** `aklie@ucsd.edu`

## Layout pattern

Follows the `gs://gaius_data/public-data/` convention (Ramirez2022 / González-Blas
references): per-dataset subtree with **flat per-data-type folders** and
canonical filenames of the form `<group>.<modality>.<file-type>.<ext>`. The
hierarchy of pseudobulk groupings is encoded *in the filename*, not in directory
nesting.

For our dataset, three analysis versions exist as parallel, fully self-contained
subtrees under the bucket root.

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

Round 1 = initial Harmony integration. Round 2 = re-clustering of filtered
endocrine cell types only; canonical for downstream pseudobulk, differential
analysis, and peak calling.

## Versioning

| Version | Pipeline | Samples | Notes |
|---|---|---|---|
| v1 | CellRanger-ARC + YAP | 44 | prototype; partial DM060 batch missing |
| v2 | CellRanger-ARC + YAP | 54 | production v2 (DM060 batch added) |
| v3 | IGVF uniform pipeline (kallisto-bustools + chromap) | 54 | current production |

Each version's bucket subtree is self-contained: `ref/`, `sample_manifest.tsv`,
`h5ad/`, `bigWig/`, `fragments/`, `tagAlign/`, `data_hubs/`. No cross-version
path lookups, no implicit shared state. Most reference files (GTF, blacklist,
motif DB, gene lists, palettes) overlap across versions but are copied per
version. Pipeline-specific files (ARC barcode whitelist + templates for v1/v2;
kallisto-bustools whitelist for v3) only ship under the versions that use them.

**Naming convention.** Filenames use `-` between *covariate names* in the
grouping label and `_` between *covariate values* inside the group token.
Example for a `cell_type-condition` grouping: file is
`SC.beta_control.ATAC.fpm.bw`. v1 outputs that use `+` in the HPC scratch
tree are renamed to `_` in value tokens at upload time. The bucket never
contains `+`.

**SoupX status.** All three versions apply SoupX as part of per-sample QC
(v1/v2 via custom QC; v3 via cellcommander `qc_RNA_uniform.sh`). Filenames
keep the `soupx_umi_counts` token across versions.

**Out of scope (deferred — not yet finalized in any version):**
- Peak BED files (`narrowPeak`, `consensus_peak.bed`, `peak_sources.bed`).
- Differential expression / accessibility results (DESeq2 outputs).

These will be added once their methodology settles (issues #4, #5).

---

## Bucket structure

```
gs://igvf-data/igvf_sc-islet_10X-Multiome/
├── v1/                                 # CellRanger-ARC + YAP, 44 samples
│   ├── ref/
│   ├── sample_manifest.tsv
│   ├── h5ad/                           # cell-by-X matrices
│   ├── bigWig/                         # *.ATAC.counts.bw, *.ATAC.fpm.bw
│   ├── fragments/                      # *.ATAC.fragments.bed.gz, *.ATAC.scale_factor.txt
│   ├── tagAlign/                       # *.ATAC.tagAlign.sort.gz (+ .tbi)
│   └── data_hubs/                      # JSON manifests for browser tracks
├── v2/                                 # CellRanger-ARC + YAP, 54 samples
│   └── (same layout as v1)
└── v3/                                 # IGVF uniform pipeline, 54 samples
    ├── ref/
    ├── sample_manifest.tsv
    ├── h5ad/
    ├── bigWig/
    ├── fragments/
    ├── tagAlign/
    ├── pseudobulk_rna/                 # new in v3
    └── data_hubs/
```

---

## v{N}/ref/ — Reference data (per version)

### Common across versions
- `SC.islet.marker_genes.csv` — SC-islet gene markers
- `blacklist.bed.gz` — ENCODE blacklist
- `IGVFFI9573KOZR.gtf.gz` — gene annotation
- `motifs.meme` — Vierstra v2.0beta motif clustering catalog
- `cellid_colors.tsv` — cell type palette (canonical: `igvf_sc-islet_10X-Multiome/config/cell_type_metadata.tsv`)
- `condition_colors.tsv` — condition palette (canonical: `config/condition_metadata.tsv`)
- `sc_islet_genelist.txt`, `regev_lab_cell_cycle_genes.txt`, `GRCh38_TCF7L2_ZFAND3.tsv`
- `GSE145643_MIN6_MPRA_Baseline_FinalMatrix.out.txt.gz`, `IEP_annotation_gnomadV3_snps.bed.gz`

### v1 / v2 only (CellRanger-ARC)
- `737K-arc-v1.txt.gz` — 10X Multiome ARC barcode whitelist
- `10x_multiome_ATAC.yaml.j2`, `10x_multiome_RNA.yaml.j2` — ARC config templates

### v3 only (IGVF uniform pipeline)
- kallisto-bustools / chromap whitelist + index files actually used by v3 (TBD; ships only under `v3/ref/`).

---

## v{N}/sample_manifest.tsv

Canonical sample-level metadata for the version. Per-version because sample
sets and identifiers differ.

**Common columns**
- `sample_id` — internal sample identifier (e.g. `dm11a`, `45-1`)
- `batch` — differentiation batch (e.g. `JE002`, `DM060`)
- `condition` — `["control", "3-cyt", "IFNg", "Ex-4_HG", "palmitate", "dex"]`
- `timepoint` — `[0, 6, 24, 48, 72]` (hours)
- `rep` — `[1, 2, 3]`
- `cell_line` — `["H1", "A2"]`
- `multiome_qc_status`
- `notes`

**v3 only**
- `igvfds_id` — IGVF data submission accession (e.g. `IGVFDS1093WHDR`)
- `analysis_set_accession` — IGVF analysis set accession

**Row counts:** v1 = 44, v2 = 54, v3 = 54.

---

## v{N}/h5ad/ — Single-cell matrices

### Cell × gene UMI count matrices

**v1** (per-celltype + endocrine combined)
- `cell_x_gene.soupx_umi_counts.endocrine_celltypes.h5ad` — round 2 endocrine combined (canonical)
- `cell_x_gene.soupx_umi_counts.SC.alpha.h5ad`
- `cell_x_gene.soupx_umi_counts.SC.beta.h5ad`
- `cell_x_gene.soupx_umi_counts.SC.delta.h5ad`
- `cell_x_gene.soupx_umi_counts.SC.EC.h5ad`

**v2 / v3** (round 2 endocrine — slim / full / annotated trio)
- `cell_x_gene.soupx_umi_counts.endocrine_celltypes.annotated.h5ad`
- `cell_x_gene.soupx_umi_counts.endocrine_celltypes.slim.h5ad`
- `cell_x_gene.soupx_umi_counts.endocrine_celltypes.full.h5ad`

**Data Matrix**
- `.X`: sparse — SoupX-corrected UMI counts (all versions)

**Observations (.obs)**
- `barcode` (index): str `{sample}#{10x_gex_barcode}` — unique cell ID; trailing `-1` retained
- `gex_barcode` / `atac_barcode`
- `sample` / `batch`
- `condition` / `timepoint` / `rep` / `cell_line`
- `cluster`, `celltype` ∈ `{SC.alpha, SC.beta, SC.delta, SC.EC}`
- v3 only: `multiome_stage`

**Variables (.var)**
- index: HUGO gene symbol

**Observation-wise multidimensional arrays (.obsm)**
- `X_pca`, `X_harmony`, `X_umap`

### Cell × peak Tn5 insertion count matrices

**v1**
- `cell_x_peak.Tn5_insertion_counts.endocrine_celltypes.h5ad`

**v2**
- `cell_x_peak.Tn5_insertion_counts.endocrine_celltypes.h5ad`

**v3** — _TODO_: not yet ported; ships once stage-5 peak calling completes.

Schema mirrors cell × gene; `.var.peak` index = `{chr}:{start}-{end}`.

---

## v{N}/bigWig/ — Per-pseudobulk genome-wide ATAC signal

Two flavors per group, mirroring Ramirez2022:
- `<group>.ATAC.counts.bw` — unnormalized Tn5 insertion counts
- `<group>.ATAC.fpm.bw` — FPM-normalized (`scale_factor = 1e6 / fragment_count`)

Plus a path-index TSV:
- `bigWig_file_paths.tsv` — relative paths + group → `(counts.bw, fpm.bw)` lookup

### Group token encoding (filename prefix `<group>`)

The grouping hierarchy is encoded **in the group token using `_` between
covariate values** (cell type names contain `.`, never `_`, so the parsing
is unambiguous):

| Grouping | Group token format | Example |
|---|---|---|
| `cell_type` | `<celltype>` | `SC.beta` |
| `cell_type-condition` | `<celltype>_<condition>` | `SC.beta_control` |
| `cell_type-condition-timepoint` | `<celltype>_<condition>_<timepoint>` | `SC.beta_control_72` |

### Groupings present per version

| Version | Groupings shipped under `bigWig/` (and `fragments/`, `tagAlign/`) |
|---|---|
| v1 | `cell_type`, `cell_type-condition`, `cell_type-condition-timepoint` |
| v2 | `cell_type`, `cell_type-condition` |
| v3 | `cell_type-condition` (more groupings TBD per #1's multi-resolution plan) |

**Provenance**
- counts: https://github.com/adamklie/single_cell_utilities/blob/dev/fragments/frag_to_count_bw.sh
- fpm:    https://github.com/adamklie/single_cell_utilities/blob/dev/fragments/frag_to_norm_bigWig.sh

---

## v{N}/fragments/ — Per-pseudobulk Tn5 fragment files

**Files**
- `<group>.ATAC.fragments.bed.gz` — sorted fragments per group
- `<group>.ATAC.scale_factor.txt` — `1e6 / fragment_count` scale factor used for the corresponding `.fpm.bw`
- `frag_counts.tsv` — fragment count summary across groups (single TSV)
- `fragment_file_paths.tsv` — path-index TSV

**Specification**
https://www.10xgenomics.com/support/software/cell-ranger-arc/latest/analysis/outputs/fragments-file

**Provenance**
- SnapATAC2 export fragments — https://kzhang.org/SnapATAC2/api/_autosummary/snapatac2.ex.export_fragments.html
- DecoupleR-based pseudobulking helper — https://github.com/adamklie/single_cell_utilities/blob/dev/functional_analysis/pseudobulk.py

---

## v{N}/tagAlign/ — Per-pseudobulk tagAlign files

**Files**
- `<group>.ATAC.tagAlign.sort.gz` — sorted tagAlign per group (one Tn5 insertion per line)
- `<group>.ATAC.tagAlign.sort.gz.tbi` — tabix index
- `tagAlign_file_paths.tsv` — path-index TSV

**Specification**
tagAlign files are special BED files where each Tn5 insertion "tag" is its own
entry. tagAligns store the "left" fragment end on the `+` strand, and the
"right" fragment end on the `-` strand. The `name` field is `N` and `score` is
`1000` for all lines.

- chr / start / end / name / score / strand

**Provenance**
- https://github.com/adamklie/single_cell_utilities/blob/dev/fragments/frag_to_tagAlign.sh

---

## v{N}/data_hubs/ — Browser-track JSON manifests

Per-modality JSON manifests pointing browser tracks at the corresponding
`bigWig/` files. One JSON per modality × version.

**Files**
- `igvf_sc-islet_10X-Multiome_v{N}_ATAC_bigwigs.json`

**JSON schema** (mirrors gaius `data_hubs/`):

```json
[
  {
    "type": "bigwig",
    "url": "https://storage.googleapis.com/igvf-data/igvf_sc-islet_10X-Multiome/v{N}/bigWig/<group>.ATAC.fpm.bw",
    "name": "<group> ATAC",
    "options": { "color": "#...", "yMin": 0, "yMax": 3, "yScale": "fixed" },
    "metadata": { "sample": "<group>", "assay": "ATAC" },
    "showOnHubLoad": true
  }
]
```

`color` is taken from `ref/cellid_colors.tsv` (i.e. project canonical palette).

---

## v3/pseudobulk_rna/ — RNA pseudobulk gene-count matrices (v3 only currently)

Stage-4 RNA pseudobulks landed in v3 (HPC: `results/4_pseudobulking/rna/<grouping>/`).
Out of scope for v1 / v2 in this spec — those versions' DE work used HPC-side
per-cell-type pseudobulks pre-dating this canonical layout.

**Files (per `<grouping>`)**
- `<grouping>.pseudobulk_no_filter.h5ad` — AnnData; rows = pseudobulks, cols = genes
- `<grouping>.pseudobulk_no_filter.tsv` — gene × pseudobulk count matrix (TSV mirror)
- `<grouping>.pseudobulk_no_filter_metadata.csv` — per-pseudobulk metadata
- `<grouping>.pseudobulk_filter.{h5ad,tsv,csv}` — same layout, post-filter

### Groupings present

| Version | Groupings |
|---|---|
| v3 | `sample_id-cell_type` (more groupings TBD per multi-resolution plan) |

**Data Matrix (`pseudobulk_no_filter.h5ad`)**
- `.X`: dense int — summed SoupX-corrected UMI counts
- shape: `(n_pseudobulks, n_genes)`

**Observations (.obs)**
- `pseudobulk` (index): str — e.g. `{sample_id}_{cell_type}` for `sample_id-cell_type`
- `sample_id`, `cell_type`, `batch`/`differentiation_batch`
- `condition`, `timepoint`, `rep`
- `psbulk_cells`, `psbulk_counts`

**Variables (.var)**
- index: HUGO gene symbol

**Provenance**
- https://github.com/adamklie/single_cell_utilities/blob/dev/functional_analysis/pseudobulk.py

---

## HPC source paths

Per-version source paths on the HPC. Upload tooling reads from these and
writes to the corresponding bucket subtree.

```
/cellar/users/aklie/data/datasets/igvf_sc-islet_10X-Multiome/

# v1 (CellRanger-ARC + YAP, 44 samples)
├── archive/v1/ref/  (or shared ref/ snapshot at v1's freeze)              → v1/ref/
├── archive/v1/results/9_upload/cell-by-X/                                  → v1/h5ad/
├── archive/v1/results/4_integration/atac/pseudobulk/<grouping>/{fragments, count_bws, norm_bws, tagAlign}/  → flatten to v1/{fragments, bigWig, tagAlign}/
└── archive/v1/results/3_sample_qc/sample_metadata.tsv                      → v1/sample_manifest.tsv  (44 rows)

# v2 (CellRanger-ARC + YAP, 54 samples)
├── archive/v2/ref/                                                         → v2/ref/
├── archive/v2/results/4_integration/rna/integrate/round_2/{slim,full,annotation}.h5ad   → v2/h5ad/
├── archive/v2/results/4_integration/atac/pseudobulk/<grouping>/{fragments, count_bws, norm_bws, tagAlign}/  → flatten to v2/{fragments, bigWig, tagAlign}/
└── archive/v2/results/1_get_data/sample_metadata.tsv                       → v2/sample_manifest.tsv  (54 rows)

# v3 (IGVF uniform pipeline, 54 samples)
├── ref/                                                                    → v3/ref/
├── results/3_cell_annotation/rna/integrate/round_2/{slim,full,merge_annotated}.h5ad     → v3/h5ad/
├── results/4_pseudobulking/atac/<grouping>/{fragments, count_bws, norm_bws, tagAlign}/  → flatten to v3/{fragments, bigWig, tagAlign}/
├── results/4_pseudobulking/rna/<grouping>/                                 → v3/pseudobulk_rna/
└── bin/1_get_data/metadata/igvfds_to_sample_id.tsv                         → v3/sample_manifest.tsv  (54 rows + IGVFDS_id columns)
```

The "flatten" step renames HPC paths like
`pseudobulk/cell_type-condition/count_bws/SC.beta_control_unstranded.bw`
→ `v{N}/bigWig/SC.beta_control.ATAC.counts.bw`, mirroring gaius's
`<group>.<modality>.<file-type>.<ext>` filename pattern. Upload tooling
(per-version notebook under `archive/v{1,2}/bin/9_upload/` or
`bin/10_submission/` for v3) implements the rename.
