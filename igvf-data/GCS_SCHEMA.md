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

**Immutability + latest pointer.** Each version subtree is **immutable once
published**. Updates ship as a new version, never by overwrite. "Latest = v3"
is documented in the top-level `README.md`, *not* via path aliasing or a
root-level mirror — `data_hubs/` URLs in published manifests stay stable
across releases.

**Public read.** The bucket is public-read. `data_hubs/` JSON URLs use
`https://storage.googleapis.com/...` directly (no signed URLs); paste them
into the WashU Epigenome Browser, IGV, or any compatible viewer without auth.

**Self-describing bucket.** Bucket-level docs ride along with the data so a
fresh visitor with zero repo access can identify what's there, what each
file means, how to load it, and how to pivot back to the HPC source.
Anchors: `README.md` at the bucket root + `schemas/` (which holds
`CONVENTIONS.md`, `DATA_MODEL.md`, and per-artifact JSON Schemas), plus a
per-version `README.md` and `source_manifest.tsv` inside each `v{N}/`.

**Out of scope (deferred — not yet finalized in any version):**
- Peak BED files (`narrowPeak`, `consensus_peak.bed`, `peak_sources.bed`).
- Differential expression / accessibility results (DESeq2 outputs).
- RNA pseudobulks at varying groupings (per #1's multi-resolution plan).

These will be added once their methodology settles (issues #1, #4, #5).

---

## Bucket structure

```
gs://igvf-data/igvf_sc-islet_10X-Multiome/
├── README.md                           # bucket-level: version matrix, "how to use" recipes
├── schemas/                            # CONVENTIONS.md, DATA_MODEL.md, per-artifact JSON Schemas
├── v1/                                 # CellRanger-ARC + YAP, 44 samples (partial subset)
│   ├── README.md                       # v1-specific pipeline notes, inventory, examples
│   ├── ref/
│   ├── sample_manifest.tsv
│   ├── source_manifest.tsv             # bucket_path ↔ hpc_path mapping for nrnb-access pivot
│   ├── h5ad/                           # endocrine combined + 4 per-celltype splits
│   ├── bigWig/                         # *.ATAC.fpm.bw only (no counts in v1)
│   └── data_hubs/                      # JSON manifests for browser tracks
├── v2/                                 # CellRanger-ARC + YAP, 54 samples
│   ├── README.md
│   ├── ref/
│   ├── sample_manifest.tsv
│   ├── source_manifest.tsv
│   ├── h5ad/                           # endocrine combined + cell_x_peak
│   ├── bigWig/                         # *.ATAC.{counts,fpm}.bw
│   └── data_hubs/
└── v3/                                 # IGVF uniform pipeline, 54 samples (canonical)
    ├── README.md
    ├── ref/
    ├── sample_manifest.tsv
    ├── source_manifest.tsv
    ├── h5ad/
    ├── bigWig/
    ├── fragments/                      # v3 only — full set
    ├── tagAlign/                       # v3 only — full set
    └── data_hubs/
```

`fragments/` and `tagAlign/` are **v3 only** by design — see "Subset shipped per version" below. v1 / v2 fragments and tagAlign live on the HPC archive at `archive/v{1,2}/results/4_integration/atac/pseudobulk/` and are accessible to nrnb-access collaborators via `source_manifest.tsv`.

---

## Subset shipped per version

Common to every version: `ref/`, `sample_manifest.tsv`, `source_manifest.tsv`, `data_hubs/`, `README.md`.

| Object class | v1 | v2 | v3 | Rationale |
|---|---|---|---|---|
| `README.md` (per version) | ✓ | ✓ | ✓ | Self-description: pipeline, sample count, what shipped, what's omitted (and why), worked examples. |
| `ref/` | ✓ | ✓ | ✓ | Per-version snapshot. Pipeline-specific files (ARC whitelists v1/v2; kallisto-bustools whitelist v3) only under their version. |
| `sample_manifest.tsv` | ✓ (44) | ✓ (54) | ✓ (54 + IGVFDS_id) | Sample-level metadata. |
| `source_manifest.tsv` | ✓ | ✓ | ✓ | `bucket_path ↔ hpc_path` for every shipped object — lets nrnb-access collaborators pivot to local. |
| `h5ad/cell_x_gene.soupx_umi_counts.endocrine_celltypes.h5ad` (slim) | ✓ | ✓ | ✓ | Canonical RNA matrix. Single slim flavor produced by the slim transform (see h5ad section below). |
| `h5ad/cell_x_gene.soupx_umi_counts.SC.{alpha,beta,delta,EC}.h5ad` (per-cell splits, slim) | ✓ | — | — | v1-only convention. |
| `h5ad/cell_x_peak.Tn5_insertion_counts.endocrine_celltypes.h5ad` (slim) | ✓ | — | _TODO_ post-#5 | v1 only currently — v2 never produced a canonical cell × peak slim h5ad; v3 will once stage-5 peak calling lands. |
| `bigWig/<group>.ATAC.fpm.bw` | ✓ | ✓ | ✓ | Canonical browser-track signal. |
| `bigWig/<group>.ATAC.counts.bw` | — | ✓ | ✓ | Useful for ChromBPNet; v1 skipped to slim it down. |
| `fragments/<group>.ATAC.fragments.bed.gz` (+ `.scale_factor.txt`) | — | — | ✓ | Bulky, niche; v1/v2 stay on HPC by request. |
| `tagAlign/<group>.ATAC.tagAlign.sort.gz` (+ `.tbi`) | — | — | ✓ | Same logic as fragments. |
| `data_hubs/igvf_sc-islet_10X-Multiome_v{N}_ATAC_fpm_bigWigs.json` | ✓ | ✓ | ✓ | One-click browser-track manifest. |

Out-of-scope items (RNA pseudobulks, peak BEDs, DESeq2 outputs) are NOT in any version yet — see the deferred list above.

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

A **single slim h5ad per matrix type per grouping** ships per version. No
slim/full/annotated trio — the bucket flavor is produced by the project's
`slim_h5ad()` transform applied to the HPC `slim.h5ad`: `.X = layers["counts"]`,
layers cleared, `.obs` renamed to the canonical bucket schema, `cell_type`
ensured (joined from the annotation TSV if missing).

Parseable contract: [`schemas/cell_x_gene_h5ad.json`](igvf_sc-islet_10X-Multiome/schemas/cell_x_gene_h5ad.json), [`schemas/cell_x_peak_h5ad.json`](igvf_sc-islet_10X-Multiome/schemas/cell_x_peak_h5ad.json).

### Cell × gene UMI count matrices

**v1** (per-celltype + endocrine combined)
- `cell_x_gene.soupx_umi_counts.endocrine_celltypes.h5ad` — round 2 endocrine combined (canonical)
- `cell_x_gene.soupx_umi_counts.SC.alpha.h5ad`
- `cell_x_gene.soupx_umi_counts.SC.beta.h5ad`
- `cell_x_gene.soupx_umi_counts.SC.delta.h5ad`
- `cell_x_gene.soupx_umi_counts.SC.EC.h5ad`

**v2 / v3** (round 2 endocrine combined; single slim flavor)
- `cell_x_gene.soupx_umi_counts.endocrine_celltypes.h5ad`

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

**v2** — does not have a canonical cell × peak slim h5ad. v2 produced peak count matrices at the pseudobulk level only (`results/5_peak_calling/<peak_set>/matrix/peak_mat.h5ad` on the HPC) — those are pseudobulk-by-peak, not cell-by-peak, so they don't fit the slim cell × X schema. nrnb-access users can read them locally.

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

## v{N}/source_manifest.tsv — Source-mapping manifest

Per-version TSV mapping every shipped bucket object back to its HPC source
path. Lets collaborators with nrnb (HPC) access skip the GCS download and
read files locally without re-downloading.

Parseable contract: [`schemas/source_manifest_tsv.json`](igvf_sc-islet_10X-Multiome/schemas/source_manifest_tsv.json).

Columns: `bucket_path`, `hpc_path`, `data_class`, `size_bytes`, `md5`, `version`, `notes`.

For h5ads where the bucket file isn't byte-identical to the HPC source (the
`slim_h5ad()` transform changed the `.obs` schema), `hpc_path` points at the
*original* HPC `slim.h5ad` and `notes` flags the transform — an HPC user can
re-derive the bucket file by running the same transform locally.

The manifest is emitted by the per-version upload notebook as a side-effect
of every `gcp_cp(src, dst)` call.

---

## Schemas

`igvf_sc-islet_10X-Multiome/schemas/` holds the parseable contract for every
bespoke artifact shipped to the bucket. Mirrors the GaiusTx/gstudio
[schemas/](https://github.com/GaiusTx/gstudio/tree/main/schemas) pattern.

The directory ships to the bucket root (`gs://.../schemas/`) so a fresh
visitor with no repo access has the full contract available alongside the
data.

| File | Purpose |
|---|---|
| `CONVENTIONS.md` | Filename grammar, separator policy, group-token rules. Shared rules across all schemas. |
| `DATA_MODEL.md` | Cross-version semantic contract per data class. Human-readable companion to the JSON Schemas. |
| `cell_x_gene_h5ad.json` | JSON Schema — slim cell × gene h5ad. |
| `cell_x_peak_h5ad.json` | JSON Schema — slim cell × peak h5ad. |
| `sample_manifest_tsv.json` | Column schema for `sample_manifest.tsv` (per-version columns flagged). |
| `source_manifest_tsv.json` | Column schema for `source_manifest.tsv`. |
| `data_hubs_bigwigs_json.json` | JSON Schema for the WashU data_hub JSON. |
| `bigwig_filename.json` | Filename grammar — `<group>.<modality>.{counts,fpm}.bw`. |
| `fragments_filename.json` | Filename grammar — fragments + scale_factor + tagAlign. |

Each JSON Schema has a project-local `igvf` metadata block (`schema_version`,
`last_modified`, `applies_to_versions`, `produced_by`, `consumed_by`,
`changelog`) — see `CONVENTIONS.md` for the authoring contract.

---

## Upload tooling

Per-version Jupyter notebooks following the
[GaiusTx/Muto_mouse-kidney_10X-Multiome `bin/7_upload/1_upload_to_gcp.ipynb`](https://github.com/GaiusTx/Muto_mouse-kidney_10X-Multiome/blob/main/bin/7_upload/1_upload_to_gcp.ipynb)
pattern: single sectioned notebook per version, inline `gcp_cp` +
`local_exists` helpers, top-of-notebook `DRY_RUN` / `OVERWRITE` flags,
per-section missing-file report, end-of-notebook `gsutil ls -r` verification
+ per-class sanity diff vs expected, plus a config-regeneration step that
emits `config/gcp/...yaml` mappings from the cell-type / grouping list.

| Version | Notebook path |
|---|---|
| v1 | `archive/v1/bin/9_upload/1_upload_to_gcp.ipynb` |
| v2 | `archive/v2/bin/9_upload/1_upload_to_gcp.ipynb` |
| v3 | `bin/10_submission/1_upload_to_gcp.ipynb` |

A light shared helper at `tools/single_cell_utilities/gcs_upload.py` exposes
the genuinely complex bits (`slim_h5ad`, `generate_data_hub_json`,
`compute_md5`); everything else (`gcp_cp`, `local_exists`, dry-run summary,
manifest collection, sanity diffs) lives inline per-notebook (matches the
Muto pattern).

**Dry-run-first contract.** Two-phase upload mandatory: Phase 1 prints the
manifest as a table (src → dst, size, exists-remote, action) plus total GB
and total file count for human review; Phase 2 runs `gcloud storage cp` only
after Phase 1 passes review.

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
├── archive/v2/results/4_integration/rna/integrate/round_2/slim.h5ad        → slim_h5ad() → v2/h5ad/cell_x_gene.soupx_umi_counts.endocrine_celltypes.h5ad
├── archive/v2/results/4_integration/atac/pseudobulk/<grouping>/{count_bws, norm_bws}/   → flatten + rename → v2/bigWig/<group>.ATAC.{counts,fpm}.bw
└── archive/v2/results/1_get_data/sample_metadata.tsv                       → v2/sample_manifest.tsv  (54 rows)

# v3 (IGVF uniform pipeline, 54 samples)
├── ref/                                                                    → v3/ref/
├── results/3_cell_annotation/rna/integrate/round_2/slim.h5ad               → slim_h5ad() → v3/h5ad/cell_x_gene.soupx_umi_counts.endocrine_celltypes.h5ad
├── results/4_pseudobulking/atac/<grouping>/{fragments, count_bws, norm_bws, tagAlign}/  → flatten + rename → v3/{fragments, bigWig, tagAlign}/
└── bin/1_get_data/metadata/igvfds_to_sample_id.tsv                         → v3/sample_manifest.tsv  (54 rows + IGVFDS_id columns)
```

(`pseudobulk_rna/` is **deferred** for the initial upload — out of scope per #1's multi-resolution plan.)

The "flatten" step renames HPC paths like
`pseudobulk/cell_type-condition/count_bws/SC.beta_control_unstranded.bw`
→ `v{N}/bigWig/SC.beta_control.ATAC.counts.bw`, mirroring gaius's
`<group>.<modality>.<file-type>.<ext>` filename pattern. Upload tooling
(per-version notebook under `archive/v{1,2}/bin/9_upload/` or
`bin/10_submission/` for v3) implements the rename.
