# SLURM Submission Guidelines

## Cluster overview

| Partition | Nodes | Resources | Use for |
|-----------|-------|-----------|---------|
| `carter-compute` | Multiple | CPU only | Pseudobulk, peak calling, annotation, motif discovery |
| `carter-gpu` | carter-gpu-01 | 8× RTX 5000 | Light GPU work |
| `carter-gpu` | carter-gpu-[02-03] | 4× A30 each (8 total) | ChromBPNet training, contributions, predictions |

## Resource recommendations by task

### Peak calling & post-processing

| Task | Partition | CPUs | Memory | GPU | Time | Concurrency |
|------|-----------|------|--------|-----|------|-------------|
| SnapATAC2 peak calling | carter-compute | 20 | 256G | — | 14d | 1 |
| HOMER annotation | carter-compute | 1 | 8G | — | 1d | all |
| Post-processing (filter) | carter-compute | 1 | 8G | — | 1d | all |
| ATAC pseudobulk | carter-compute | 1 | 256G | — | 1d | 1 |

### Differential analysis (DESeq2)

| Task | Partition | CPUs | Memory | GPU | Time | Notes |
|------|-----------|------|--------|-----|------|-------|
| Per-condition DESeq2 | carter-compute | 1 | 32G | — | 14d | ~5-10 min per CT×condition |
| Joint DESeq2 (per-CT) | carter-compute | 1 | 32G | — | 14d | ~45 min per cell type |
| Joint DESeq2 (across-CTs) | carter-compute | 1 | 64G | — | 14d | ~8h (all samples) |
| Palmitate standalone | carter-compute | 1 | 16G | — | 14d | ~5 min (JE002 subset) |

### ChromBPNet pipeline

| Step | Task | Partition | CPUs | Memory | GPU | Time/job | Concurrency |
|------|------|-----------|------|--------|-----|----------|-------------|
| 1 | Negatives | carter-compute | 4 | 16G | — | ~20 min | 12 |
| 2 | Bias model | carter-gpu | 4 | 50G | a30:1 | ~7h | 1 (shared model) |
| 3 | ChromBPNet training | carter-gpu | 4 | 40G | a30:1 | 5-15h | 4 |
| 4 | Predictions | carter-gpu | 4 | 32G | any:1 | ~1h | 8 |
| 5 | Average predictions | carter-compute | 1 | 4G | — | <1h | all |
| 6 | Contributions | carter-gpu | 4 | 50G | a30:1 | 4-8h | 4 |
| 7 | Average contributions | carter-compute | 1 | 4G | — | <1h | all |
| 8 | Motif discovery | carter-compute | 16 | 16G | — | 2-4h | 10 |
| 9 | Motif clustering | carter-compute | 4 | 16G | — | <1h | 1 |

## General guidelines

### Concurrency

- **CPU jobs:** Generally safe to run all in parallel (`-x N` where N = total jobs)
- **GPU jobs:** Limit to 4 concurrent A30 jobs to leave capacity for others
- **Large memory jobs (>128G):** Limit to 1-2 concurrent

### SLURM helpers

Two SLURM submission helpers in `/cellar/users/aklie/projects/ML4GLand/chrombpnet/SLURM/`:

```bash
# CPU array jobs
cpu_array.sh -s <script> -j <name> -p carter-compute -a carter-compute \
  -c <cpus> -m <mem> -o <logdir> -n <num_tasks> -x <concurrency>

# GPU array jobs
gpu_array.sh -s <script> -j <name> -p carter-gpu -a carter-gpu \
  -g a30:1 -c <cpus> -m <mem> -o <logdir> -n <num_tasks> -x <concurrency>
```

### Common patterns

```bash
# Submit with dependency (run after previous job completes)
sbatch --dependency=afterok:<job_id> ...

# Submit single array task (e.g. task 16 for SC.beta_control)
sbatch --array=16 ...

# Check job status
sacct -j <job_id> --format=JobID%15,State%12,Elapsed,ExitCode --noheader

# Check GPU availability
sinfo -p carter-gpu --format="%P %a %D %N %G"
squeue -p carter-gpu --format="%.10i %.30j %.8u %.8T %.10b"
```

### Pre-submission checklist

1. Verify all input files exist (`ls -la` each path)
2. Check no output dirs pre-exist that would cause conflicts
3. Verify fold path format (`fold_0.json` not `fold_fold_0.json`)
4. For notebooks: ensure `nbconvert` is available and kernel is correct
5. For Python scripts: use `conda run -n <env>` not `source activate`
6. Set appropriate concurrency based on partition capacity

### Logging

All SLURM logs go to: `bin/slurm_logs/<step>/<substep>/%x.%A.%a.out`

Check for errors:
```bash
# Tail the latest log
tail -30 /path/to/log/*.out

# Find failures in array jobs
sacct -j <job_id> --format=JobID,State,ExitCode --noheader | grep FAILED
```
