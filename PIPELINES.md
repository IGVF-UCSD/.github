# Pipelines

Reference: [Computational Pipelines Doc](https://docs.google.com/document/d/1ezAMH7XTlkiBBP1N7BCGbTHl9_7pYxSNOVsYbLyfgIw/edit?tab=t.7dod347fua6j#heading=h.zbn2iki948jz)

## Multiome

### Version comparison

| Step | v1 | v2 | v3 |
|------|----|----|-----|
| **Preprocessing** | CellRanger | CellRanger | Single cell uniform pipeline |
| **Sample QC** | Multiome Sample QC Pipeline | Multiome Sample QC Pipeline | Multiome Sample QC Pipeline |
| **Cell annotation** | Multiome Cell Annotation Pipeline | Multiome Cell Annotation Pipeline | Multiome Cell Annotation Pipeline |
| **Peak calling** | SnapATAC2 + MACS3 | SnapATAC2 + MACS3 (refined) | — |
| **Differential analysis** | DESeq2 pseudobulk | DESeq2 pseudobulk (refined) | — |
| **Peak-to-gene linking** | — | — | — |
| **Sequence modeling** | ChromBPNet | — | — |

### v1 steps (prototype)

Code in `igvf-data/igvf_sc-islet_10X-Multiome/scratch/2026_01_30/` and `scratch/2026_02_02/`.

| Step | Description |
|------|-------------|
| 1. RNA QC | Filter low-quality RNA barcodes (`1_qc_RNA.tsv`) |
| 2. ATAC QC | AMULET doublet detection + ATAC filtering (`2_AMULET.tsv`, `2_integrate_ATAC.tsv`) |
| 3. Sample QC | Per-sample QC, normalization, and initial clustering (RNA + ATAC) |
| 4. Integration | Cross-sample Harmony integration, cell type annotation |
| 5. Peak calling | SnapATAC2 MACS3 peak calling, annotation, pseudobulk peak matrices |
| 6. Joint analysis | Joint RNA-ATAC embedding, gene activity quantification, multiome correlation |
| 7. Differential analysis | DESeq2 pseudobulk DE (RNA) and DA (ATAC), DEGPatterns, complex heatmaps |
| 8. ChromBPNet | Sequence model training, contribution scores, TF-MoDISco motifs, variant scoring |
| 9. Upload | Cell-by-X matrices, pseudobulks, browser tracks, GRN outputs |

### v2 steps (refined)

Code in `igvf-data/igvf_sc-islet_10X-Multiome/bin/`.

| Step | Description |
|------|-------------|
| 1. Get data | Transfer FASTQs from TSCC, download portal metadata |
| 2. Process data | CellRanger-ARC count, run QC on CellRanger outputs |
| 3. Sample QC | RNA QC filtering, AMULET doublet detection, ATAC QC filtering |
| 4. Integration | Merge + Harmony integration (RNA, ATAC), cell annotation, pseudobulk generation |
| 5. Peak calling | MACS3 peak calls per celltype-condition, consensus peaks, peak matrices |
| 6. Differential analysis | DESeq2 pseudobulk RNA, DEG summaries, DEGPatterns |
| 9. Upload | Upload to `gs://igvf-data/` via notebook |
| 10. Submission | MD5sums, IGVF portal metadata tables, seqspec, analysis sets |

### Presentations by topic

#### Peak calling

- [2026_01_08 — IGVF BCN Agenda (peak calling)](https://docs.google.com/presentation/d/1xVHmcwG4UQxNJ57l3AO62mqsW-LCHWZGXZAtdkkDVkI/edit?usp=sharing)

#### Differential analysis

- [2025_01_13 — DEG Updates](https://docs.google.com/presentation/d/1m3vCMgAbjCvYADKz5E7UN2gepYFQ1Ht09nCrpxOwgHg/edit?usp=sharing)
- [2025_03_25 — Collinearity Issues](https://docs.google.com/presentation/d/1XMlbzTE7puWK8AKVvwEt2HKhamCFDvp4HwQz_grlXSA/edit?usp=sharing)
- [2026_02_19 — IGVF BCN Agenda (differential analysis)](https://docs.google.com/presentation/d/1kFX1gP1buFYyLZsAwXNzsUhnfejuRciiRAc6cDTsdco/edit?usp=sharing)

## snm3C-seq

- [2025_02_18 — snm3C-seq Discussion](https://docs.google.com/presentation/d/1yvshdJj4_O2FfXuGz9dNpUCFt7gQNHrkUm8yn2XuETk/edit?usp=sharing)

_Pipeline details coming soon_

## S2F (sequence to function modeling)

- [2025_03_17 — Variant Evaluation](https://docs.google.com/presentation/d/1JzIl7kmZyADAcsfxlCzFIWYKU4oBb5orOez6TLu2P0I/edit?slide=id.p#slide=id.p)
- [2025_04_17 — Variant Effect Prediction](https://docs.google.com/presentation/d/1PQPp3XkTNi4X4NlTdNjyUBnUrQyOO86ERv2ytno778E/edit?usp=sharing)
- [2025_05_15 — Variant Effect Prediction (update)](https://docs.google.com/presentation/d/1_Wcu84mLP_q5vyy2mDwOlxFXoRBb1m6mE5mMvDnrz0E/edit?usp=sharing)
- [2025_07_10 — S2F Updates](https://docs.google.com/presentation/d/1k6izEy2l7LBawUuAr7UFL6doBu9qBS5d7O-lEFGrM8A/edit?usp=sharing)
