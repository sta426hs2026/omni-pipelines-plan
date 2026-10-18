# Benchmarking scRNA pipelines (simplified for STA 426 HS 2026)

[![Open in OBEditor](https://img.shields.io/badge/open-obeditor-0078d4?style=flat)](https://omnibenchmark.github.io/obeditor?url=https://github.com/sta426hs2026/omni-pipelines-plan/blob/main/benchmark.yaml)

This repo contains a single-cell RNA-seq (scRNA-seq) pipelines benchmark created using [OmniBenchmark](https://omnibenchmark.org), but trimmed for the purposes of the [STA 426 course](https://github.com/sta426hs2026/material).

## Installing `ob` (Omnibenchmark)

OmniBenchmark is required to run this pipeline and can be installed following [this guide](https://docs.omnibenchmark.org/latest/howto/#installation) or by doing the following steps after (Mini)`conda` / `mamba` is installed (instructions [here](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html#installing-conda)):

```{bash}
conda create -n omni python=3.12 -y
conda activate omni
pip install omnibenchmark==0.7.0

ob --version
```

To run a benchmark (e.g., if you've created the conda environment but are now in a new shell), you will need to activate the software environment where you installed `ob`:

```{bash}
conda activate omni
```

## Quick start

```{bash}
git clone https://github.com/sta426hs2026/omni-pipelines-plan.git
cd omni-pipelines-plan
ob validate plan benchmark.yaml
ob run benchmark.yaml -c 2

#ob run benchmark.yaml -- --apptainer-args='--nv'  # not needed now, but might be later
```

## Other useful commands (run one by one, do not cut-paste)

```{bash}
ob validate plan benchmark.yaml 
ob run benchmark.yaml -d                       ## dry run (creates Snakefile, not run)
ob run benchmark.yaml -d --unpinned            ## if using branch refs (not hashes)
ob run benchmark.yaml -d --unpinned --dirty    ## if using local directories
ob run benchmark.yaml -c 16 --unpinned         ## specify number of cores
prlimit --rss=64g --cpu=10000 ob run benchmark.yaml -c 8 --unpinned --continue-on-error  ## process limits
```

## Getting help

The `ob --help` command gives a full description of the verbs that are implemented.

```{bash}
❯ ob --help   
Usage: ob [OPTIONS] COMMAND [ARGS]...

  OmniBenchmark Command Line Interface (CLI).

Options:
  --debug / --no-debug  Enable debug mode
  --version             Show the version and exit.
  --help                Show this message and exit.

Commands:
  cite         Extract citation metadata from CITATION.cff...
  collect      Gather scattered benchmark artefacts into...
    collect performance Gather all performance.txt files into a...
  create       Create new benchmarks or modules from templates.
    create benchmark  Create a new benchmark from a template.
    create module     Create a new module from a template.
  describe     Describe benchmarks and/or information about them.
    describe snakemake  Export a snakemake computational graph to...
    describe topology   Export benchmark topology to MERMAID...
    describe status     Show the status of a benchmark.
  remote       Manage remote storage.
    remote files      Manage files in remote storage.
    remote version    Manage benchmark versions.
    remote policy     Manage storage policies. (DEPRECATED)
  run          Run a benchmark.
  validate     Validate benchmarks and modules.
    validate plan       Validate benchmark YAML plan structure.
    validate module     Validate module (metadata, try to run).
  archive      Archive a benchmark and its artifacts.
  dashboard    Generate a dashboard from benchmark results.
```

Help pages for all subcommands are also available:

```{bash}
❯ ob run --help                                                          
Usage: ob run [OPTIONS] BENCHMARK [SNAKEMAKE_ARGS]...

  Run a benchmark.

  BENCHMARK: Path to benchmark YAML file.

  This command: 1. Fetches and caches all module repositories 2. Resolves
  modules and generates an explicit Snakefile 3. Runs snakemake on the
  generated Snakefile

  Any arguments after -- are passed directly to snakemake.

  Examples:

    ob run benchmark.yaml                    # Run full benchmark   ob run
    benchmark.yaml --cores 8          # Run with 8 cores   ob run
    benchmark.yaml --dry              # Generate Snakefile only   ob run
    benchmark.yaml --dirty            # Allow local paths with uncommitted
    changes   ob run benchmark.yaml --unpinned         # Allow branch refs on
    remote repos   ob run benchmark.yaml -m M1              # Dev mode: run
    only module M1   ob run benchmark.yaml --telemetry         # Emit
    OTLP/JSONL telemetry to stdout   ob run benchmark.yaml -- --rerun-triggers
    mtime  # Pass flags to snakemake   ob run benchmark.yaml -- --forceall
    # Force re-run all rules

Options:
  --debug / --no-debug     Enable debug mode
  -c, --cores INTEGER      Use at most N CPU cores in parallel. Default is 1.
  -d, --dry                Dry run (only generate Snakefile, don't execute).
  -k, --continue-on-error  Go on with independent jobs if a job fails (--keep-
                           going in snakemake).
  --out-dir TEXT           Output folder name. Default: `out`
  --dirty                  Allow local path module references with uncommitted
                           changes. Use for development only.
  --unpinned               Allow unpinned branch references on remote repos
                           (resolved to HEAD at run time). Use for development
                           only.
  --use-remote-storage     Execute and store results remotely using S3 storage
                           configured in the benchmark YAML.
  -m, --module TEXT        Run only the sub-graph needed for a single module
                           (development mode). Prunes all stages after the
                           target module's stage and keeps only the first
                           upstream input × parameter expansion for each
                           module.
  --telemetry              Emit OTLP telemetry as JSON Lines to stdout
                           (disables Rich progress).
  --telemetry-output PATH  Write telemetry to file instead of stdout. Allows
                           Rich progress to remain active. Implies
                           --telemetry.
  --with-capability NAME   Declare a host capability available on this machine
                           (repeatable). Modules whose requires_capabilities
                           are not all provided are pruned. Example: --with-
                           capability gpu --with-capability large_mem
  --until TEXT             Stop the pipeline at and including the named stage.
                           Only the named stage and its transitive ancestors
                           are kept in the resolved DAG, and metric collectors
                           whose inputs reference pruned stages are skipped.
  --filter FILE|BLOB       Run a slice of the benchmark defined by an obfilter
                           selection: a path to a YAML/JSON file of picks, a
                           file containing a packed blob, or an inline packed
                           blob. Prunes the DAG to the picked
                           stages/modules/param-combos. Incompatible with
                           -m/--module.
  --allow-drift            With --filter: if the benchmark has changed since
                           the filter was made and some selected
                           stages/modules/parameters no longer exist, run the
                           part that still matches instead of erroring.
                           Skipped selections are listed as warnings.
  --help                   Show this message and exit.
```

## Citation

If you use this benchmark in your research, please cite it using the information in `CITATION.cff`.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
