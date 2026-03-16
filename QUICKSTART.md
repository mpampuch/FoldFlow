# FoldFlow Quick Start Guide

## Prerequisites

- Nextflow 25.04.7 or later
- One of: Docker, Singularity/Apptainer, or Conda
- For GPU processes: CUDA-capable GPU

## Basic Usage

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/FoldFlow.git
cd FoldFlow
```

### 2. Test the Pipeline

Quick stub test (no actual computation):

```bash
nextflow run main.nf -profile test -stub
```

### 3. Run with Test Data

```bash
# With Singularity
nextflow run main.nf -profile test,singularity

# With Docker
nextflow run main.nf -profile test,docker

# With Conda
nextflow run main.nf -profile test,conda
```

### 4. Run Production Analysis

```bash
nextflow run main.nf \
    --num_designs 100 \
    --output_prefix my_design \
    --outdir ./results \
    -profile singularity
```

## Common Scenarios

### Generate 50 Protein Designs

```bash
nextflow run main.nf \
    --num_designs 50 \
    --output_prefix protein_v1 \
    --mpnn_num_sequences 4 \
    --outdir results/batch1 \
    -profile singularity
```

### Run on SLURM Cluster

```bash
nextflow run main.nf \
    --num_designs 100 \
    --output_prefix cluster_run \
    --outdir /scratch/results \
    -profile slurm,singularity
```

### Resume Failed Run

```bash
nextflow run main.nf -resume -profile singularity
```

### Custom Configuration

Create `my_config.config`:

```groovy
params {
    num_designs = 20
    output_prefix = 'custom_design'
    mpnn_num_sequences = 3
}

process {
    withName: 'RFDIFFUSION' {
        cpus = 16
        memory = 128.GB
    }
}
```

Run with custom config:

```bash
nextflow run main.nf -c my_config.config -profile singularity
```

## Output Structure

```
results/
├── design_0/
│   ├── rfdiffusion/
│   │   ├── design_0.pdb
│   │   └── design_0.trb
│   ├── proteinmpnn/
│   │   ├── design_0_seq_0.fasta
│   │   ├── design_0_seq_1.fasta
│   │   └── ...
│   └── alphafold/
│       ├── design_0_seq_0_model.pdb
│       ├── design_0_seq_1_model.pdb
│       └── ...
├── design_1/
│   └── ...
├── pipeline_info/
│   ├── execution_report.html
│   ├── execution_timeline.html
│   └── execution_dag.svg
└── multiqc/
    └── multiqc_report.html
```

## Key Parameters

### Required
- None (all have defaults)

### Common Options

| Parameter | Default | Description |
|-----------|---------|-------------|
| `--num_designs` | 10 | Number of protein designs to generate |
| `--output_prefix` | 'design' | Prefix for output files |
| `--outdir` | './results' | Output directory |
| `--mpnn_num_sequences` | 2 | Sequences per backbone |

### Module Configs

Customize tool behavior via YAML files in `configs/`:

- `configs/RFdiffusion.yaml` - RFdiffusion parameters
- `configs/MPNN.yaml` - ProteinMPNN parameters
- `configs/AlphaFold.yaml` - AlphaFold parameters

## Profiles

Available execution profiles:

| Profile | Description |
|---------|-------------|
| `test` | Minimal test dataset, reduced resources |
| `debug` | Extended logging, small dataset |
| `docker` | Use Docker containers |
| `singularity` | Use Singularity/Apptainer containers |
| `conda` | Use Conda environments |
| `slurm` | SLURM executor configuration |

Combine profiles with commas:
```bash
-profile test,singularity
-profile slurm,singularity,debug
```

## Troubleshooting

### Pipeline Won't Start

```bash
# Check Nextflow version
nextflow -version

# Should be >= 25.04.7
```

### Out of Memory

Increase memory in config:

```groovy
process {
    withLabel: 'process_gpu' {
        memory = 128.GB
    }
}
```

### GPU Not Found

Check CUDA availability:

```bash
nvidia-smi
```

Ensure Singularity has `--nv` flag (automatically set in config).

### Containers Not Found

Pull containers manually:

```bash
# For Singularity
singularity pull docker://nvcr.io/nvidia/pytorch:24.07-py3
```

## Advanced Usage

### Modify Module Parameters

Edit YAML files in `configs/`:

```yaml
# configs/RFdiffusion.yaml
inference:
  num_designs: 1
  design_pdb: null
  contigmap:
    contigs: ["150-150"]
  ppi:
    hotspot_res: null
```

### Add Custom Preprocessing

Create script in `bin/`:

```python
#!/usr/bin/env python3
# bin/custom_process.py

import sys

def main():
    # Your preprocessing logic
    pass

if __name__ == '__main__':
    main()
```

Make executable:
```bash
chmod +x bin/custom_process.py
```

Use in process:

```groovy
script:
"""
custom_process.py input.txt > output.txt
"""
```

### Generate Pipeline Diagram

```bash
nextflow run main.nf -profile test -with-dag flowchart.png
```

## Performance Tips

1. **Use `-resume`** for interrupted runs
2. **Adjust `max_cpus`** in config to match your hardware
3. **Use local executor** for small runs
4. **Use SLURM/cloud** for large batch processing
5. **Monitor with** `-with-report -with-timeline -with-dag`

## Getting Help

```bash
# View all parameters
nextflow run main.nf --help

# View specific module docs
nextflow inspect main.nf

# Check configuration
nextflow config main.nf
```

## Next Steps

- Read [README.md](README.md) for detailed documentation
- Review [CHANGELOG.md](CHANGELOG.md) for version history
- See [REFACTORING_SUMMARY.md](REFACTORING_SUMMARY.md) for architecture details
- Customize module configs in `configs/`
- Explore module implementations in `modules/local/`

## Example Workflow

Complete example from start to finish:

```bash
# 1. Test the pipeline
nextflow run main.nf -profile test -stub

# 2. Run small production batch
nextflow run main.nf \
    --num_designs 5 \
    --output_prefix pilot_run \
    --outdir results/pilot \
    -profile singularity \
    -with-report \
    -with-timeline

# 3. Review results
ls -lh results/pilot/
open results/pilot/pipeline_info/execution_report.html

# 4. Scale up
nextflow run main.nf \
    --num_designs 100 \
    --output_prefix full_run \
    --outdir results/production \
    -profile slurm,singularity \
    -with-report \
    -resume

# 5. Analyze outputs
# - Check RFdiffusion structures in */rfdiffusion/
# - Review MPNN sequences in */proteinmpnn/
# - Validate with AlphaFold structures in */alphafold/
```

## Support

For issues, questions, or contributions:
- GitHub Issues: [your-repo-url/issues]
- Documentation: See README.md
- Community: [your-community-link]
