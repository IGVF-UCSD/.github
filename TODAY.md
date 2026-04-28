## Daily Task List
This document serves as a scratchpad for tracking daily tasks and progress. Upon first read of this doc in a session, review the "Workflow" section of [CLAUDE.md](CLAUDE.md) to understand how this doc fits into the overall project process. You may edit this doc as needed to track your work, but remember to also update the corresponding GitHub issue thread with progress and findings.

## 04/28/2026

### 1. Uniform documentation of `public-data/` datasets
Goal: a collaborator landing in [public-data/](public-data/) should be able to understand each dataset (what it is, where it came from, processing state, file layout, caveats) without having to ping us.
- Inventory current state across the 12 dataset dirs: `Augsornworawat2023_sc-islet_10X-Multiome`, `Chiou2021_islet_snATAC-seq`, `Dominguez2020_sc-islet_bulk`, `EndoC-bH1_treated_ATAC-seq`, `gwas_sumstats`, `HPAP`, `islet_10X-Multiome`, `Maestas2024_islet_10X-Multiome`, `mo_EndoC-bH1_ATAC-seq`, `Wang2023_islet_snATAC-seq`, `Zhu2023_sc-islet_scRNA-seq`, `Zhu2023_sc-islet_snATAC-seq`.
- Define a uniform README template (suggested fields: source/citation/accession, modality, species + genome build, n samples / n cells, raw vs processed file layout, processing pipeline used, gene-naming convention, known caveats, link to relevant GitHub issue + scratch workspace).
- Author/refresh a README per dataset; cross-link from [doc/DATA.md](doc/DATA.md) and add a top-level `public-data/README.md` index.
- Tie back to integrative-analysis issues that depend on these (#9 Augsornworawat, #10 Chiou primary islet, #11 snm3C external comparators).

### 2. v3 uniform pipeline migration + v1/v2 archive (#8 follow-up)
Outcome of the v2 → v3 presentation: we're confident enough in uniformly-processed v3 data to make it the default. Now formalize that.
- Migrate [igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_04_09/v3_uniform_pipeline/](igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_04_09/v3_uniform_pipeline/) into the production tree (likely under `igvf-data/igvf_sc-islet_10X-Multiome/bin/` and `results/` mirroring the v2 layout). Decide naming (`bin/` vs `bin_v3/`?) before moving anything.
- Design v1/v2 archive structure so old objects (h5ads, peak sets, DESeq2 outputs, ChromBPNet artifacts) remain easily discoverable — proposal: `igvf-data/igvf_sc-islet_10X-Multiome/archive/v{1,2}/` with a top-level `ARCHIVE.md` mapping old paths → new paths.
- Update [doc/OVERVIEW.md](doc/OVERVIEW.md), [doc/PIPELINES.md](doc/PIPELINES.md), [doc/DATA.md](doc/DATA.md), and [CLAUDE.md](CLAUDE.md) once paths are finalized.
- Coordinate with #6 (GCP upload) — the canonical layout decision should be the same one used for the bucket manifest.

### 3. snm3C-seq reprocess (#11)
Main goal: bring in the additional samples that have finished sequencing, while cleaning up the existing pipeline.
- Audit current snm3C pipeline state: scripts location, last-run sample list, pseudobulk `.hic` outputs (the 3 SC.alpha/beta/EC files flagged on 04/16 — Sara's normalization complaint).
- Inventory the 114 new FASTQs noted on 04/16 — confirm sample sheet, mapping to donor/condition metadata.
- Plan re-run scope: full reprocess vs incremental (just new samples), and resolve normalization (`juicer_tools post -k KR` vs alternative — needs Sara's confirmation).
- Land cleanup as part of the v3 migration (item 2) so snm3C lives in the same canonical layout.

### Progress (today)

**Task 1 — public-data/ documentation:** all 11 dataset READMEs written (1 foreground, 10 background subagents). Plan saved at `~/.claude/plans/immutable-twirling-stearns.md`. Each README follows uniform template (Source / Study design / What's here / Status / Use in this project) with `_TODO_` markers where filesystem evidence wasn't sufficient. **Still needed:** top-level `public-data/README.md` index (deferred), backfill `_TODO_`s (HPAP provenance, EndoC cell-type discrepancy, etc.), `doc/DATA.md` "Public datasets" section, two-line `CLAUDE.md` touch.

Notable findings to surface:
- **HPAP** — provenance recovered from `*-celltype-*.txt` URL manifests: PanKbase S3 (`pankbase-data-v1.s3.amazonaws.com/...`). 12 cell types enumerated, 5-fold CV models trained. User to backfill citation/donor count.
- **Wang2023** — 34 donors split 11 ND / 8 Pre-T2D / 15 T2D. A subset has 10x Multiome (RNA+ATAC), not just snATAC — slug undersells.
- **Chiou2021** — 5 SRRs locally are all non-diabetic donors (T2D arm in the paper is scRNA, not snATAC). hg38, ChromBPNet fold 0 only.
- **Zhu2023** — sub-series GSE202497 (RNA) + GSE202498 (ATAC) of SuperSeries GSE202500. **hg19** alignment, 5 timepoints, 1 H1 hESC donor. Status raw.
- **Augsornworawat2023** — sample sheet has 7 SC-Islets but `processed/` has 18 dirs (including CRISPRA, ARID1B/GFP, transplanted, human islet controls). Sample sheet is materially incomplete.
- **Dominguez2020** — 4-assay study (RNA + ATAC + ChIP + WGBS), only ChIP + WGBS downloaded locally. nf-core/methylseq run failed.
- **mo_EndoC-bH1** — tiny: 1 sample × 3 reps. Both bpnet-lite and ChromBPNet 5-fold trained.
- **EndoC-bH1_treated** — workflow how-to moved to `bin/chrombpnet/README.md`. Cell-type-list discrepancy flagged (current README lists islet cell types, not EndoC).

**Task 2 — v3 migration + v1/v2 archive:** done in nested repo at `igvf-data/igvf_sc-islet_10X-Multiome/`. Three commits:
1. `99c1a96` — WIP checkpoint of 04/06–04/16 v2 work (chrombpnet pipeline, DESeq2 joint scripts, peak_calling pseudobulk, submission renumbering)
2. Archive v2 — `bin/` → `archive/v2/bin/` (git mv, history preserved); `results/` → `archive/v2/results/` (1.9T, gitignored, instant rename); v1+v2 manifests written
3. Promote v3 — `scratch/2026_04_09/v3_uniform_pipeline/{1_get_data,2_sample_qc,3_integration}` → `bin/`; `results/` → `results/` (448G); 14 scripts refactored from hardcoded `SCRATCH=/abs/path` to `${DATASET_ROOT}` (env-var with script-relative fallback). `4_comparison/` left in scratch as v2-vs-v3 archival.

Parent project doc updates: `CLAUDE.md`, `doc/DATA.md`, `doc/OVERVIEW.md`, `doc/PIPELINES.md` all updated to reflect v3-current / v2-archived / v1-archived-in-place.

**Outstanding for task 2:**
- v3 RNA `4_cell_annotation.ipynb` produced no outputs — finish in new `bin/3_integration/` location
- Port `make_batch_info.py` helper from `archive/v2/bin/4_integration/scripts/` to v3's `bin/3_integration/scripts/`
- Port stages 4–10 (ATAC integration, peak calling, DESeq2, ChromBPNet, upload, submission) from `archive/v2/bin/` to v3 `bin/`
- `pipelines/multiome-cell-annotation/scratch/*` references to old `bin/` and `results/` paths

**Task 3 — snm3C-seq reprocess:** not started.

---

## 04/16/2026

### v2 → v3 presentation (#8)
Workspace: `igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_04_09/v3_uniform_pipeline/4_comparison/`
- **[PRESENTATION.md](igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_04_09/v3_uniform_pipeline/4_comparison/PRESENTATION.md)** rewritten to a 4-slide plan:
  1. Setup — CellRanger ARC vs IGVF uniform pipeline. Pipeline comparison table rendered as PNG + TSV: `results/pipeline_comparison_v2_vs_v3.{png,tsv}`
  2. Barcode overlap — `fig3c_overlap.png` + `atac_fig3c_overlap.png` on the left; **`results/jaccard_boxplot_by_modality.png`** on the right (RNA median 0.922, ATAC median 0.980)
  3. Cell-type UMAPs side-by-side (v2 vs v3) + `ct_composition_per_sample.png` for composition
  4. Summary — safe to continue on v3

### New comparison figures (today)
- `results/jaccard_boxplot_by_modality.png` — per-modality Jaccard boxplot (RNA vs ATAC).
- `results/per_sample_median_v2_vs_v3.png` — 4-panel per-sample v2 vs v3 median scatter for total_counts, n_genes, pct_mt, pct_ribo. Shows total_counts/n_genes/pct_ribo hug y=x while pct_mt shifts +47 % per sample uniformly.
- `results/ct_counts_and_props_per_sample_scatter.png` — 2×4 scatter of per-sample cell-type counts (top) and proportions (bottom). Counts ρ ≥ 0.90 all CTs, proportions ρ ≥ 0.98.
- `results/ct_counts_per_sample_bars.png` — per-sample grouped bars per cell type (v2 vs v3).
- `results/mt_gene_audit_reference_and_ranking.png` — 3-panel: reference membership (13 shared + 2 rRNA v3-only + 22 tRNA v3-only), per-class count share, top 15 MT genes ranked (MT-RNR2 is #1 overall).
- `results/mt_threshold_impact_45-1_v3.png` — pct_mt ECDF (all-37 vs canonical-13) + cells failing at 3/5/7/10 % thresholds.
- `results/pipeline_comparison_v2_vs_v3.{png,tsv}` — slide-ready pipeline comparison table.
- New scripts: `plot_jaccard_boxplot.py`, `plot_per_sample_median_shift.py`, `plot_per_sample_celltype_counts.py`, `plot_mt_audit.py`, `build_pipeline_comparison_table.py` (all run under `scverse-lite-py39`).

### Infra shipped today
- **New:** [doc/ENVS.md](doc/ENVS.md) — env inventory (cellcommander / scverse-lite-py{39,311} / chrombpnet / eugene_tools / seqtools-R443 / finemo_gpu / etc.), with the "`conda activate` silently falls back to /usr/bin/python in non-interactive shells" gotcha documented. Linked from CLAUDE.md.

### ChromBPNet motif discovery (#1)
- `9324509` array status: **28/48 COMPLETED, 20 CANCELLED** (tasks 20, 30–48 — stopped 04-13 for cluster capacity).
- **Resubmitted cancelled indices as `9329911`** (`--array=20,30-48%8`, 16 CPU / 90 G / `eugene_tools`). ETA ~24 h.
- Step 9 per-ctc script (`cell_type-condition_motif_clustering.sh`) needs generation — deferred until `9329911` completes.
- Steps 10–12 (marginalization → unified modisco h5 → FiNeMo hit calling) scoped against sister project's implementation.

### Project board walk-through
Went through all 16 issues on [Project #4](https://github.com/orgs/IGVF-UCSD/projects/4/views/1), posted status updates on 11, closed #3 (INS secretion score — Junxi's first pass rolls into #7), left #13/#14/#15/#16 as-is.
- **#3** closed — Junxi's first-pass INS secretion score stands; iteration moves to #7.
- **#6 (GCP upload) upgraded to high priority** — body expanded with explicit "design manifest first" blocking task. This becomes the canonical shared layout for v2 (Sara, integrative analyses) and template for v3.
- **#4 (DESeq2) reframed** — instead of another method sweep, run a focused file-by-file audit on one treatment (proposed: 3-cyt) across v1/v2/v3, modeled on today's `4_comparison/` work. Decide methodology from that.
- **#5 peak calling** — first 3 task boxes checked; comparative analysis notebook flagged as today's work item.
- **#2 (peak-to-gene)** — Sara (Gaulton lab) re-engaged; real blocker is unnormalized HiC (→ #11), not DESeq2.

### Background audits (3-step pattern: audit → plan → execute)
Piloted subagent-based audits on 3 deferred integrative issues. Each returned a structured gap analysis + next concrete action:
- **#9 Augsornworawat2023** — RNA fully processed (44k cells, annotated, hg38+GENCODE full compat); ATAC integration is the gap (fragments+peaks exist per sample, no unified h5ad).
- **#10 primary islet** (Chiou 2021) — ~18k cells, RNA+ATAC both integrated+annotated; has matched control vs 3-cyt. Gap: 5-min gene-naming / genome-build verification before integration.
- **#11 snm3C** — 3 pseudobulk `.hic` files (SC.alpha/beta/EC) generated with no explicit normalization flag; Sara's complaint confirmed. Potential ~15-min quick-win via `juicer_tools post -k KR` (deferred — needs Sara's method-choice confirmation first). 114 new FASTQs ready for full reprocessing.

### Still pending from 04/13 — resolved today
- ✅ merge_RNA (#8): already succeeded in job `9328637` before today (stale notes cleared up).
- ✅ `9324509` status checked: partial → `9329911` resubmit covers the gap.

### Strategic question to resolve before integrative work (#9, #10)
**v3 breaks the CellRanger↔CellRanger symmetry for external integrations.** v2 was CellRanger-ARC for both sides; v3 is IGVF uniform (kallisto-bustools + chromap) on our side only. Augsornworawat2023 (#9), Chiou 2021 primary islet (#10), and most future public datasets will still be CellRanger-processed. Options:
1. **Keep v2 as the integration comparator**, use v3 for internal analyses only (DESeq2, peaks, ChromBPNet within-dataset). Simplest; means maintaining two active analysis versions.
2. **Reprocess external datasets through IGVF pipeline**. Rigorous; expensive per cohort. Realistic for 1–2 highest-value datasets only.
3. **Accept the pipeline confound and treat it as a batch covariate**, same as cross-study batch. Today's v2↔v3 comparison (RNA Jaccard 0.92, composition preserved) *is* the evidence that this confound is tolerable for most analyses.
Decide before kicking off any integrative analysis. Leaning: **option 3** broadly, **option 2** only for ChromBPNet cross-dataset parity.

### Action items for tomorrow (04/17) / next session
- **v2 peak calling comparative analysis notebook** — `scratch/2026_03_23/5_peak_calling/comparative_analysis.ipynb` (carried from #5, bumped into today's scope but not executed; run it tomorrow first thing). Defines the v2 peak-set story before GCP upload + v3-ization.
- **#6 MANIFEST.md / LAYOUT.md** — design the canonical v2 bucket layout (no upload until this exists).
- **Monitor `9329911`** — when complete, generate step-9 per-ctc motif clustering scripts, submit.
- **Kick off #4 DESeq2 audit** — pick one treatment (propose 3-cyt), trace every DE result file across v1/v2, build `4_comparison`-style comparisons.
- **v3 ATAC merge** — `merge_ATAC.sh` hasn't been run yet against v3 per-sample `clustered.h5ad` outputs.
- **Decide pipeline-comparator strategy** (above) before kicking off #9 or #10.

---

## 04/15/2026

### Action item for tomorrow (04/16)
- Build the presentation

---

## 04/13/2026

### Cluster status at end of day
- **RUNNING:** `9324509` ChromBPNet motif discovery array (2/48 COMPLETED, 8 RUNNING, 38 PENDING; ~4h/task, ~24h total ETA → tomorrow morning)
- **RUNNING:** `9328213` gene-filter survey (extended w/ MAF family + per-gene n_cells cache)
- **All 04/09 cluster jobs (RNA QC `9061665`, AMULET `9062930`, ChromBPNet contributions `8797654`) finished earlier this week.**

---

### Issue #8: v3 uniform pipeline — blocked on merge_RNA OOM (HIGHEST PRIORITY)

Workspace: `igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_04_09/v3_uniform_pipeline/`

**v2 vs v3 sanity check (done):** [4_comparison/rna_barcode_v2_vs_v3.tsv](igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_04_09/v3_uniform_pipeline/4_comparison/rna_barcode_v2_vs_v3.tsv). RNA Jaccard 0.85, AMULET Jaccard 0.79. v3 slightly more stringent on cells, flags ~22% more doublets. Outlier to revisit later: **`45-1` / IGVFDS0008YGIA** (RNA Jaccard 0.42, v3 kept half the cells of v2).

**merge_RNA saga (job history):**
| Job | ReqMem | Gene filter | Elapsed | Outcome |
|---|---|---|---|---|
| 9278298 | 384G | none | 2h00m | OOM in SCTransform R block |
| 9285580 | 512G | none | 3h33m | OOM in SCTransform R block (MaxRSS 510G) |
| 9322933 | 512G | `min_cells=3` (62,757→48,811 genes) | 5h45m | OOM in SCTransform R block |

**Diagnosis:** OOM at `ro.r('... SCTransform ...')`. At 312,149 cells × 48,811 genes, Seurat's `SCTransform` with `do.correct.umi=TRUE` materializes intermediate corrected-counts matrices across all input genes (even though final `scale.data` is only ~3k HVGs). Memory scales with input gene count.

**Plumbing already in place:**
- Added `--min_cells_per_gene` flag to cellcommander normalize, applied upstream of all methods (log1p + sctransform). Code: [cellcommander/normalize/run.py](../../opt/CellCommander/cellcommander/normalize/run.py), [argparser.py](../../opt/CellCommander/cellcommander/normalize/argparser.py). Logged before/after gene count.
- Step 1 (merge) commented out in [1_merge_RNA.sh](igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_04_09/v3_uniform_pipeline/3_integration/1_merge_RNA.sh) — reuses existing 20GB `merge.h5ad`.

**Plan for tomorrow (resubmit #4):**
1. **Read survey output** `9328213` → [4_comparison/gene_filter_survey/survey_9328213.out](igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_04_09/v3_uniform_pipeline/4_comparison/gene_filter_survey/) to see MAF family counts.
2. **Pick min_cells threshold:** earlier survey showed
   - `min_cells=50` → 36,346 genes (57.9%), drops only MAFA (408 cells) among checked markers
   - `min_cells=100` → 32,764 genes (52.2%), also drops MAFA
   - `min_cells=500` → 23,710 genes (37.8%), drops MAFA/HHEX/RBP4
   - Verify MAFB is retained (expected to replace MAFA as the dominant beta-cell MAF in SC-islets).
3. **Enable `conserve.memory=TRUE`** in [cellcommander/normalize/sctransform.py:76](../../opt/CellCommander/cellcommander/normalize/sctransform.py) Seurat `SCTransform(...)` call. This was the real lever per R `args(SCTransform)` check — chunks the corrected-counts step.
4. **Resubmit** `bash 3_integration/1_merge_RNA.sh`. Worst case if still OOM: drop sctransform from `--methods` (keep log1p only) and have reduce_dimensions use log1p + HVG path.

**Downstream (unblocked once normalize.h5ad exists):**
- reduce_dimensions → cell_metadata.tsv → make_batch_info (pandas ModuleNotFoundError on step 4a — need to invoke with env's python, not bare `python`)
- [2_analysis_RNA.ipynb](igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_04_09/v3_uniform_pipeline/3_integration/2_analysis_RNA.ipynb) → [3_integrate_RNA.ipynb](igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_04_09/v3_uniform_pipeline/3_integration/3_integrate_RNA.ipynb) → [4_cell_annotation.ipynb](igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_04_09/v3_uniform_pipeline/3_integration/4_cell_annotation.ipynb)
- ATAC QC (`2_sample_qc/4_qc_ATAC.sh`) — explicitly deferred until RNA integration done (user call)
- Formal v2 comparison in `4_comparison/`
- Investigate `45-1` outlier

---

### Issue #1: ChromBPNet — standardized + averaging done, motif discovery running

**Directory standardization (v1 layout adopted):**
- `fold_0/chrombpnet_model/` → `fold_0/chrombpnet/` (×24 ctcs)
- `fold_0/bias_model/` → `fold_0/bias/` (only SC.beta_control has a local bias tree; others reference it — shared bias model)
- Contribution files moved out of `predictions/` into sibling `contributions/` (144 files; `predictions/` now only holds model prediction bigwigs)
- `/1.0/` = β scaling parameter used in bias model training (NOT a version slot)

**Scripts rewired:**
- 27 `.sh` files sed'd: `chrombpnet_model/` → `chrombpnet/`, `bias_model/` → `bias/` (bounded with trailing slash to preserve filename `bias_model_scaled.h5` and Bash var `bias_models=`)
- [scripts/contributions/cell_type-condition_contributions.sh](igvf-data/igvf_sc-islet_10X-Multiome/bin/8_chrombpnet/scripts/contributions/cell_type-condition_contributions.sh) `output_dirs` now point to `contributions/`
- [0_prep_files.ipynb](igvf-data/igvf_sc-islet_10X-Multiome/bin/8_chrombpnet/0_prep_files.ipynb) and [README.md](igvf-data/igvf_sc-islet_10X-Multiome/bin/8_chrombpnet/README.md) updated to match

**Step 7 (average contributions) — DONE:**
- Built per-ctc scripts in [scripts/contributions/_generate_average_contribution_scripts.sh](igvf-data/igvf_sc-islet_10X-Multiome/bin/8_chrombpnet/scripts/contributions/_generate_average_contribution_scripts.sh) (24 generated). Structure supports multi-fold; single-fold collapses to `cp`. TODO: real `.h5` averaging when >1 fold trained.
- All 24 jobs `9323553..9323576` COMPLETED. Each ctc has `average/contributions/{counts_scores,profile_scores}.{bw,h5}`.

**Step 8 (motif discovery) — RUNNING:**
- Built [scripts/motifs/cell_type-condition_motif_discovery.sh](igvf-data/igvf_sc-islet_10X-Multiome/bin/8_chrombpnet/scripts/motifs/cell_type-condition_motif_discovery.sh) (48-task array: 24 ctcs × {counts, profile}).
- Resources: 16 CPU × 8 concurrent = 128 CPUs; 90G × 8 = 720G (within 128 CPU / 750G budget).
- Modisco params: `n_seqlets=100000`, `leiden_res=2`, `window=400`, Vierstra v2.0beta motif DB. PFMs exported per ctc×head.
- Job `9324509` — 2/48 complete (~4h/task); ETA tomorrow morning.
- **After step 8 completes:** run step 9 motif clustering (`9_motif_clustering.sh` needs inspection — may also need per-ctc scripts built).

---

### Issue #4: DESeq2 methodology (carried over, not touched today)
- [ ] Investigate OLD LRT vs NEW Wald union comparison — verify they converge
- [ ] Also try NEW LRT ∪ NEW Wald union
- [ ] Run ATAC DESeq2 on pseudobulked peak matrices

### Issue #5: Peak calling comparative analysis (carried over)
- [ ] Run [scratch/2026_03_23/5_peak_calling/comparative_analysis.ipynb](igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_03_23/5_peak_calling/comparative_analysis.ipynb)
- [ ] Compare 3 v2 peak sets against each other + v1 peaks

### DACC deliverables — done (per user, 04/13)

---

### Documentation / infra improvements today
- **New:** [doc/METHODS.md](doc/METHODS.md) — paper-style methods section covering design, 10X Multiome, QC, integration, peak calling, differential analysis, ChromBPNet, TF-MoDISco, snm3C, integrative analyses.
- **CLAUDE.md:** added explicit Scratch locations block (no top-level `scratch/`), pointed to METHODS.md.
- **doc/OVERVIEW.md:** v3 section now lists workspace path.
- **Memory:** saved `reference_scratch_locations.md` so future sessions don't re-search for `scratch/`.

---

### First thing to do tomorrow
1. Check `sacct -j 9328213` and read [survey_9328213.out](igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_04_09/v3_uniform_pipeline/4_comparison/gene_filter_survey/).
2. Confirm MAFB retained at chosen threshold; pick min_cells (likely 50 or 100).
3. Edit [cellcommander/normalize/sctransform.py:76](../../opt/CellCommander/cellcommander/normalize/sctransform.py) to add `conserve.memory = TRUE`.
4. Edit [merge_RNA.sh](pipelines/multiome-cell-annotation/src/scripts/merge_RNA.sh) `--min_cells_per_gene` value.
5. Resubmit `bash 1_merge_RNA.sh`.
6. Check `sacct -j 9324509` — step 8 should be ~halfway done.

---

## 04/09/2026

### Running on cluster
- [ ] ChromBPNet contributions (job `8797654`, tasks 4-7 running, 8-24 pending, 4 concurrent) — ~2-3 days remaining
- [ ] After contributions: submit averaging (steps 5, 7) and motif discovery (step 8)

### Issue #4: DESeq2 methodology
- [ ] Investigate OLD LRT vs NEW Wald union comparison — verify they converge
- [ ] Also try NEW LRT ∪ NEW Wald union as another comparison point
- [ ] If union comparison validates, decide on final methodology
- [ ] Run ATAC DESeq2 using pseudobulked peak matrices

### Issue #8: v3 uniform pipeline
Workspace: `igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_04_09/v3_uniform_pipeline/`
- [x] Downloaded 16 missing uniform pipeline samples from portal (all 54 now available)
- [x] Preprocessed h5ads (Ensembl → gene symbols) for 16 new samples
- [x] Consolidated all 54 preprocessed + 38 old RNA QC results into new workspace
- [x] Fixed cellcommander installation (editable install broke during project reorg)
- [ ] **RNA QC running** — job `9061665`, 16 new samples, 8 concurrent
- [ ] **AMULET running** — job `9062930`, all 54 samples, 10 concurrent, 32G mem
- [ ] After AMULET: consolidate AMULET results, run ATAC QC (all 54)
- [ ] Integration (RNA + ATAC) with all 54 samples
- [ ] Cell annotation
- [ ] Formal comparison to v2

### Issue #5: Peak calling comparative analysis
- [ ] Run comparative analysis notebook (`scratch/2026_03_23/5_peak_calling/comparative_analysis.ipynb`)
- [ ] Compare 3 v2 peak sets against each other
- [ ] Compare v2 peaks to v1 peaks (`scratch/2026_01_30/5_peak_analysis/peak_calls/rna_celltype/12-1/snapatac2/`)

---

## 04/06/2026

### Running on cluster
- [x] ChromBPNet predictions (job `8764145`, 24 tasks) — completed
- [x] ChromBPNet contributions (job `8764146`, 24 tasks, 4 concurrent) — cancelled, resubmitted as `8797654`
- [x] After contributions: submit motif discovery (step 8) and averaging (steps 5, 7)

### DACC prediction submission (urgent)
DACC is asking for prediction outputs for the flagship. See [PORTAL.md](igvf-data/PORTAL.md) for the full ask.

They need:
- [ ] **Differential elements** (27 DEG sets, 27 DAR sets, 27 DMR sets) — DEGs mostly done (#4), DARs need ATAC DESeq2, DMRs from snm3c
- [x] **ChromBPNet accessibility tracks** (30 bigwigs) — predictions done
- [ ] **TF binding site predictions** (33 BED files) — needs motif hit calling after contributions finish
- [ ] **Enhancer-promoter links** (9 sets, 3 methods) — depends on #2 (peak-to-gene links)
- [ ] **GWAS variant effects** (30 sets) — depends on #15 (variant effect predictions)

**Action items today:**
1. Respond to DACC with timeline and what we can deliver now vs later
2. Format ChromBPNet bigwigs for portal submission once predictions finish
3. Figure out portal schema for prediction file types (talk to data wrangler)

### Issue #4: DESeq2 methodology
- [x] Run OLD LRT vs NEW Wald union comparison (the proper test)
- [ ] Also try NEW LRT ∪ Wald union
- [ ] Submit ATAC DESeq2 using pseudobulked peak matrices (now available)

### Issue #1: ChromBPNet
- [x] All 24 models trained (counts r > 0.80 across the board)
- [x] Predictions completed
- [ ] Contributions running
- [ ] After: averaging, motif discovery, motif clustering

### Issue #5: Peak calling
- [x] All 3 peak sets processed (pseudorep, timepoint, no-rep)
- [x] ATAC pseudobulk generated for all 3 peak sets
- [ ] Comparative analysis notebook still pending

### Issue #8: v3 / barcode fix
- [x] R2 barcodes uploaded and verified
- [x] Uniform pipeline complete for 10 samples
- [ ] Download and run sample QC + cell annotation
- [ ] Formal comparison to CellRanger

### Other
- [ ] Review issue #3 (INS secretion score) — first pass done by Junxi, revisit later
- [ ] Review issue #11 (reprocess snm3c) — deferred, many steps
