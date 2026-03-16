# Changelog

All notable changes to FoldFlow will be documented in this file.

## [1.0.0] - 2026-03-16

### Added - DSL2 Refactoring

#### Major Changes
- Complete refactoring to Nextflow DSL2 architecture
- Modular structure with separate workflow and module files
- Enhanced configurability and maintainability
- Comprehensive linting compliance (all files pass `nextflow lint`)

#### New Structure
- **Main workflow**: `main.nf` - Entry point with parameter handling
- **Workflows**: `workflows/foldflow.nf` - Main pipeline logic
- **Modules**: Separated into `modules/local/`:
  - `rfdiffusion/` - Protein backbone generation
  - `proteinmpnn/` - Sequence design
  - `alphafold/` - Structure validation
- **Configuration**: Enhanced config files in `conf/`:
  - `base.config` - Resource defaults
  - `modules.config` - Module-specific settings
  - `slurm.config` - SLURM executor configuration
  - `test.config` - Testing profile

#### Features
- **Metadata tracking**: Each design carries metadata through the pipeline
- **Flexible publishing**: Configurable output directories per module
- **Resource labels**: Standard labels for CPU, memory, and GPU requirements
- **Version tracking**: Automatic version capture for all tools
- **Stub execution**: Test mode support without running actual tools
- **Error handling**: Improved retry and error strategies
- **Multiple profiles**: Support for Docker, Singularity, Conda, SLURM

#### Improvements
- Better channel handling with automatic forking
- Explicit parameter validation
- Comprehensive documentation (README.md)
- Helper scripts organized in `bin/` directory
- Module-specific environment files
- Execution reports and timelines
- Pipeline DAG visualization

#### Configuration Enhancements
- Centralized parameter management
- Profile-based execution (test, debug, singularity, docker, slurm)
- Resource checking with `check_max()` function
- Configurable publish modes
- Better container registry support

#### Documentation
- Comprehensive README with:
  - Quick start guide
  - Configuration examples
  - Troubleshooting section
  - Development guidelines
- Inline code documentation
- Example configuration files

### Technical Details

#### DSL2 Compliance
- Uses `nextflow.enable.dsl = 2`
- No deprecated operators (`set`, `tap`)
- Explicit closure parameters (no implicit `it`)
- Modern channel namespace (`channel.of()` vs `Channel.from()`)
- Workflow handlers using `workflow.onComplete`

#### Code Quality
- All files pass `nextflow lint` validation
- No warnings for projectDir usage in processes
- Proper variable scoping
- Clean separation of concerns

### Migration Notes

For users migrating from the original pipeline:

1. **Parameter names unchanged**: Core parameters like `num_designs`, `output_prefix` remain the same
2. **Config structure**: Old `nextflow.config` parameters still work but can now be organized by profile
3. **Output structure**: Results are now organized by module in separate directories
4. **Helper scripts**: Moved from `helper/` to `bin/` directory
5. **Module configs**: Still use YAML files in `configs/` directory

### Breaking Changes
- Output directory structure has changed (organized by module)
- Helper script paths now reference `bin/` instead of `helper/`
- Workflow handlers syntax updated for DSL2 compliance

### Known Issues
- None currently identified

### Testing
- All files pass Nextflow linting
- Test profile available for quick validation
- Stub mode supported for development

---

## [0.1.0] - Original Release

Initial implementation with basic DSL1 structure
