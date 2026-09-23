# Coverage Pipeline

A modular Snakemake pipeline designed for high-throughput sequencing data processing. The pipeline handles raw read cleaning, de novo assembly using MEGAHIT, read mapping via BWA or Strobealign, k-mer analysis, and coverage calculation.

## Overview

The `coverage_pipeline` repository provides a 3-stage bioinformatics workflow:

1. Quality Control and Read Cleaning: Filtering and trimming raw FASTQ files.
2. Contig Assembly: De novo assembly using MEGAHIT.
3. Read Alignment and Coverage Profiling: Mapping processed reads back to assembled contigs or references and computing sequence coverage profiles.

Each stage is defined by its own Snakefile, allowing users to run the full workflow sequentially or execute individual stages as standalone modules.

## Repository Structure

```
coverage_pipeline/
├── 1_Snakefile_cleaned_reads     # Stage 1: Quality trimming and cleaning
├── 2_Snakefile_megahit           # Stage 2: MEGAHIT de novo assembly
├── 3_Snakefile_mapping-2         # Stage 3: Alignment, BLAST, and coverage
└── yaml/                         # Configuration and environment files
    ├── config_cleaned.yaml       # Config for Stage 1
    ├── config_megahit.yaml       # Config for Stage 2
    ├── config_megahit_only.yaml  # Config for standalone assembly runs
    ├── config_fisher.yaml        # Config for downstream analysis
    ├── blast.yaml                # Conda environment: NCBI BLAST+
    ├── bwa.yaml                  # Conda environment: BWA and SAMtools
    ├── dsk.yaml                  # Conda environment: DSK k-mer counter
    ├── megahit.yaml              # Conda environment: MEGAHIT
    ├── seqkit.yaml               # Conda environment: SeqKit
    ├── seqtk.yaml                # Conda environment: Seqtk
    └── strobealign.yaml          # Conda environment: Strobealign
```

## Prerequisites and Installation

### Requirements

- Linux or macOS
- Conda or Mamba
- Snakemake (>= 7.0)

### Setup

Clone the repository:

```bash
git clone https://github.com/your-username/coverage_pipeline.git
cd coverage_pipeline
```

Install Snakemake and Mamba:

```bash
conda install -c conda-forge -c bioconda snakemake mamba
```

## Configuration

Configuration parameters (input/output paths, threads, filtering parameters) are managed through YAML files in the `yaml/` directory:

- `yaml/config_cleaned.yaml`: Settings for Stage 1 (read cleaning).
- `yaml/config_megahit.yaml`: Settings for Stage 2 (MEGAHIT assembly).
- `yaml/config_megahit_only.yaml`: Settings for isolated assembly runs.
- `yaml/config_fisher.yaml`: Settings for Stage 3 (mapping and coverage).

Edit the relevant `.yaml` file prior to running each stage.

## Usage Guide

Execute each step using `--use-conda` so Snakemake automatically manages tool environments.

### Stage 1: Read Cleaning

```bash
snakemake \
  -s 1_Snakefile_cleaned_reads \
  --configfile yaml/config_cleaned.yaml \
  --use-conda \
  --cores 16
```

### Stage 2: De Novo Assembly

```bash
snakemake \
  -s 2_Snakefile_megahit \
  --configfile yaml/config_megahit.yaml \
  --use-conda \
  --cores 32
```

### Stage 3: Mapping and Coverage

```bash
snakemake \
  -s 3_Snakefile_mapping-2 \
  --configfile yaml/config_fisher.yaml \
  --use-conda \
  --cores 32
```

To perform a dry run without executing commands, add `-n` to any Snakemake command.

## Tools and Conda Environments

The workflow uses software defined in the `yaml/` environment files:

- MEGAHIT (`megahit.yaml`): De novo read assembly.
- BWA (`bwa.yaml`): Short-read mapping.
- Strobealign (`strobealign.yaml`): Short-read alignment.
- SeqKit (`seqkit.yaml`): FASTA/Q file processing.
- Seqtk (`seqtk.yaml`): Sequence file processing.
- DSK (`dsk.yaml`): K-mer counting.
- NCBI BLAST+ (`blast.yaml`): Local sequence alignment searches.


# ViraLM Pipeline

A pipeline for processing and analyzing nucleotide sequences (with a focus on viral genomes like SARS-CoV-2) based on the hidden states of language models.

## Main Components
* **`viralm_pipeline.py`**: The main script for initializing and executing the entire pipeline.
* **`pipeline_no_rerun.sh`**: A Bash script to run the pipeline processes, automatically skipping any previously completed steps.
* **`make_subsequnces.py` / `make_subsequnces_pipe.py`**: Slices the original nucleotide sequences from FASTA files into manageable subsequences.
* **`viralm_hook.py`**: Hooks into the language model to extract its hidden states during sequence processing.
* **`pca_hidden_states_sample.py` / `pca_transform.py`**: Calculates and applies Principal Component Analysis (PCA) to reduce the dimensionality of the extracted hidden state features.
* **`check_classification_parameters.py`**: Evaluates the classification metrics based on the processed features.
* **`summarize_results.py`**: Aggregates the processed data and generates the final summary report.

## Test Data Structure (`TEST_DATA`)
* **`DSI/`**: Contains test files `ecoli.fa` and `ecoli2.fa`.
* **`DSII/`**: Contains test files `someSars.fa` and `someSars2.fa`.

## Usage

To run the pipeline while skipping already completed steps, use the provided Bash script:
```bash
bash pipeline_no_rerun.sh
```

For a standard execution directly through Python, run:
```bash
python viralm_pipeline.py
```
