# FoldFlow DSL2 - Complete File Index

## Project Structure

```
FoldFlow-refactored/
│
├── main.nf                          🚀 Pipeline entry point
├── nextflow.config                  ⚙️  Main configuration file
│
├── workflows/
│   └── foldflow.nf                 📋 Main workflow logic
│
├── modules/local/
│   ├── rfdiffusion/
│   │   ├── main.nf                 🧬 RFdiffusion process
│   │   └── environment.yml         📦 Conda environment
│   ├── proteinmpnn/
│   │   ├── main.nf                 🧬 ProteinMPNN process
│   │   └── environment.yml         📦 Conda environment
│   └── alphafold/
│       ├── main.nf                 🧬 AlphaFold process
│       └── environment.yml         📦 Conda environment
│
├── conf/
│   ├── base.config                 ⚙️  Base process resources
│   ├── modules.config              ⚙️  Module-specific settings
│   ├── slurm.config                ⚙️  SLURM executor config
│   └── test.config                 ⚙️  Test profile config
│
├── bin/
│   ├── reformat_fixed_residues.py  🐍 Helper script
│   ├── split_mpnn_fastas.py        🐍 Helper script
│   └── yaml_to_args.py             🐍 Helper script
│
├── configs/
│   ├── RFdiffusion.yaml            📝 RFdiffusion parameters
│   ├── MPNN.yaml                   📝 ProteinMPNN parameters
│   └── AlphaFold.yaml              📝 AlphaFold parameters
│
├── assets/                          📁 (empty - for future schemas)
├── docs/                            📁 (empty - for future docs)
├── tests/                           📁 (empty - for future tests)
└── subworkflows/local/              📁 (empty - for future subworkflows)
│
└── Documentation/
    ├── README.md                   📖 Main documentation
    ├── CHANGELOG.md                📜 Version history
    ├── QUICKSTART.md               🚀 Quick start guide
    ├── REFACTORING_SUMMARY.md      📊 Architecture comparison
    ├── REFACTORING_COMPLETION.md   ✅ Completion report
    └── FILES_INDEX.md              📑 This file
```

## File Descriptions

### Core Pipeline Files

| File | Lines | Purpose | Status |
|------|-------|---------|--------|
| `main.nf` | ~110 | Entry point, metadata handling, workflow invocation | ✅ Lint-clean |
| `nextflow.config` | ~178 | Main config with profiles and parameters | ✅ Lint-clean |
| `workflows/foldflow.nf` | ~85 | Orchestrates RFdiffusion → ProteinMPNN → AlphaFold | ✅ Lint-clean |

### Module Files

| Module | main.nf Lines | Purpose | Container | Status |
|--------|---------------|---------|-----------|--------|
| `rfdiffusion` | ~80 | Generates protein backbones | nvcr.io/nvidia/pytorch | ✅ Lint-clean |
| `proteinmpnn` | ~85 | Designs sequences for backbones | nvcr.io/nvidia/pytorch | ✅ Lint-clean |
| `alphafold` | ~90 | Validates designed sequences | nvidia/cuda | ✅ Lint-clean |

### Configuration Files

| File | Purpose | Key Settings |
|------|---------|--------------|
| `conf/base.config` | Base process resources | CPU/memory/time labels |
| `conf/modules.config` | Module-specific settings | Publishing, resources per module |
| `conf/slurm.config` | SLURM executor | Queue, partition, GPU settings |
| `conf/test.config` | Test profile | Reduced resources, small dataset |

### Helper Scripts

| Script | Lines | Purpose | Language |
|--------|-------|---------|----------|
| `reformat_fixed_residues.py` | ~50 | Reformats fixed residue specifications | Python |
| `split_mpnn_fastas.py` | ~40 | Splits MPNN multi-FASTA outputs | Python |
| `yaml_to_args.py` | ~60 | Converts YAML configs to CLI args | Python |

### Module Configuration

| YAML File | Purpose | Used By |
|-----------|---------|---------|
| `configs/RFdiffusion.yaml` | RFdiffusion inference parameters | RFDIFFUSION process |
| `configs/MPNN.yaml` | ProteinMPNN design parameters | PROTEINMPNN process |
| `configs/AlphaFold.yaml` | AlphaFold prediction parameters | ALPHAFOLD process |

### Documentation Files

| Document | Lines | Purpose | Audience |
|----------|-------|---------|----------|
| `README.md` | ~450 | Comprehensive pipeline documentation | All users |
| `CHANGELOG.md` | ~105 | Version history and changes | All users |
| `QUICKSTART.md` | ~338 | Getting started guide | New users |
| `REFACTORING_SUMMARY.md` | ~406 | DSL1 vs DSL2 comparison | Developers |
| `REFACTORING_COMPLETION.md` | ~373 | Completion report | Developers |
| `FILES_INDEX.md` | This | File directory and index | All users |

## Statistics

### Code Files
- **Nextflow files**: 10 (main.nf, workflow, 3 modules, 4 configs)
- **Python scripts**: 3 helper utilities
- **YAML configs**: 6 (3 module configs + 3 conda envs)
- **Total code lines**: ~1,500+

### Documentation
- **Documentation files**: 6 markdown files
- **Total doc lines**: ~2,070+

### Lint Status
- **Files linted**: 10/10
- **Errors**: 0
- **Warnings**: 0
- **Status**: ✅ **100% CLEAN**

## File Relationships

### Execution Flow

```
main.nf
  ↓
  includes: workflows/foldflow.nf
  ↓
  ├── RFDIFFUSION (modules/local/rfdiffusion/main.nf)
  │   ├── uses: configs/RFdiffusion.yaml
  │   ├── uses: bin/yaml_to_args.py
  │   └── uses: bin/reformat_fixed_residues.py
  │
  ├── PROTEINMPNN (modules/local/proteinmpnn/main.nf)
  │   ├── uses: configs/MPNN.yaml
  │   ├── uses: bin/yaml_to_args.py
  │   └── produces: split_mpnn_fastas.py processes output
  │
  └── ALPHAFOLD (modules/local/alphafold/main.nf)
      ├── uses: configs/AlphaFold.yaml
      └── uses: bin/yaml_to_args.py
```

### Configuration Hierarchy

```
nextflow.config (main)
  ├── includeConfig: conf/base.config
  ├── includeConfig: conf/modules.config
  │
  └── profiles:
      ├── test → conf/test.config
      ├── slurm → conf/slurm.config
      ├── docker → (inline config)
      ├── singularity → (inline config)
      └── conda → (inline config)
```

### Module Dependencies

```
Each module/*/main.nf:
  ├── conda: modules/*/environment.yml
  ├── container: (defined in process)
  ├── config: configs/*.yaml
  └── scripts: bin/*.py
```

## Directory Purpose

### `/workflows`
Contains workflow logic that orchestrates multiple processes. Currently has one workflow (`foldflow.nf`) but can be extended.

### `/modules/local`
Houses process definitions. Each module is self-contained with its own:
- Process definition (`main.nf`)
- Environment specification (`environment.yml`)

### `/conf`
Configuration files organized by purpose:
- Resource allocation
- Module-specific settings
- Executor configurations
- Profile definitions

### `/bin`
Executable helper scripts automatically added to PATH. Contains Python utilities for:
- Data reformatting
- File splitting
- Configuration parsing

### `/configs`
YAML parameter files for bioinformatics tools. These are tool-specific configurations separate from Nextflow settings.

### `/assets` (empty)
Reserved for:
- Pipeline schemas
- Input validation files
- Reference data

### `/docs` (empty)
Reserved for:
- Extended documentation
- Tutorials
- API documentation

### `/tests` (empty)
Reserved for:
- Test data
- Test workflows
- CI/CD configurations

### `/subworkflows/local` (empty)
Reserved for:
- Reusable subworkflows
- Multi-step processes
- Common patterns

## Quick Navigation

### For Users
- **Getting Started**: → `QUICKSTART.md`
- **Full Documentation**: → `README.md`
- **Parameter Reference**: → `nextflow.config`
- **Tool Configurations**: → `configs/*.yaml`

### For Developers
- **Main Workflow**: → `workflows/foldflow.nf`
- **Process Definitions**: → `modules/local/*/main.nf`
- **Configuration**: → `conf/*.config`
- **Architecture**: → `REFACTORING_SUMMARY.md`

### For Maintenance
- **Version History**: → `CHANGELOG.md`
- **Completion Status**: → `REFACTORING_COMPLETION.md`
- **File Index**: → This file

## Version Control

### Essential Files (Always Commit)
- All `.nf` files
- All `.config` files
- All documentation (`.md`)
- All helper scripts (`bin/*.py`)
- All YAML configs (`configs/*.yaml`, `*/environment.yml`)

### Ignore (Add to .gitignore)
- `.nextflow/`
- `.nextflow.log*`
- `work/`
- `results/`
- `*.pyc`
- `.DS_Store`

## Testing

### Lint All Files
```bash
nextflow lint .
# ✅ 10 files had no errors
```

### Test Individual Components
```bash
nextflow lint main.nf
nextflow lint workflows/
nextflow lint modules/
nextflow lint conf/
```

### Validate Configuration
```bash
nextflow config main.nf
nextflow config main.nf -profile test
nextflow config main.nf -profile slurm,singularity
```

## File Permissions

### Executable Files
Ensure these are executable:
```bash
chmod +x bin/reformat_fixed_residues.py
chmod +x bin/split_mpnn_fastas.py
chmod +x bin/yaml_to_args.py
```

### Read-Only Files
Configuration files can be read-only for safety:
```bash
chmod 444 configs/*.yaml
```

## Future Extensions

### Planned Additions
- `/tests/` - Test datasets and workflows
- `/docs/` - Extended documentation
- `/assets/schema_input.json` - Input validation schema
- `/subworkflows/local/` - Reusable subworkflows
- Additional profiles in `conf/`

### Easy to Add
1. **New Modules**: Create `modules/local/tool_name/main.nf`
2. **New Configs**: Add to `conf/` and include in `nextflow.config`
3. **New Profiles**: Add to profiles block in `nextflow.config`
4. **New Tests**: Create in `tests/` directory

## Maintenance Checklist

When updating the pipeline:

- [ ] Update version in `nextflow.config` (manifest.version)
- [ ] Document changes in `CHANGELOG.md`
- [ ] Run `nextflow lint .` to verify all files
- [ ] Update README.md if parameters change
- [ ] Test with `-profile test -stub`
- [ ] Update this index if files are added/removed

## Summary

- **Total Files**: 24 tracked files
- **Code Files**: 16 (Nextflow, Python, YAML)
- **Documentation**: 6 markdown files
- **Configuration**: 8 config/environment files
- **Status**: ✅ Complete and lint-clean
- **Ready For**: Production use

---

**Last Updated**: 2026-03-16  
**Pipeline Version**: 1.0.0  
**Nextflow Version**: >= 24.04.0  
**DSL Version**: 2
