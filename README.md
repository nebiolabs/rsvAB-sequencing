# rsvAB-sequencing
NEBNext multiplex PCR sequencing primers for respiratory syncytial virus (RSV) A & B

Primer positions were liberally estimated based on homology of 3' ends on the assumption that overtrimming (e.g. using [samtools ampliconclip](https://www.htslib.org/doc/samtools-ampliconclip.html)) unlikely to materially reduce coverage.

## Data Analysis

### Overview

To analyze sequencing data from RSV samples generated using the NEBNext multiplex PCR approach on an ONT sequencer, we recommend using a Galaxy workflow that aligns reads to RSV A and B reference genomes and generates consensus sequences with comprehensive quality metrics.

### About Galaxy

Galaxy is a web-based platform for accessible, reproducible, and transparent computational research. It provides a user-friendly interface for running complex bioinformatics workflows without requiring command-line expertise.

**Important:** You must create a free Galaxy account and be logged in to run workflows and save your analysis results.

### Getting Started

#### 1. Create a Galaxy Account

Register for a free account at [usegalaxy.org](https://usegalaxy.org) if you want to work in the servers in the United States, or [usegalaxy.eu](https://usegalaxy.eu) if want to use the European Galaxy.

#### 2. Access Example Data

Example RSV sequencing data is available to help you test the workflow:
- [Example NEB RSV Data History (usegalaxy.org)](https://usegalaxy.org/u/bradlanghorst/h/example-neb-rsv-data)
- [Example NEB RSV Data History (usegalaxy.eu)](https://usegalaxy.eu/u/brad_langhorst/h/example-neb-rsv-data)
- Once logged in, you can import this history to your account

#### 3. Import the Workflow

Access and import the analysis workflow:
- [RSV Analysis Workflow (usegalaxy.org)](https://usegalaxy.org/u/bradlanghorst/w/neb-ont-rsv)
- [RSV Analysis Workflow (usegalaxy.eu)](https://usegalaxy.eu/u/brad_langhorst/w/neb-ont-rsv)
- Click to import the workflow to your Galaxy account

### Workflow Inputs

The workflow requires the following inputs:

1. **Input Reads** (required): A collection of ONT reads in FASTQ format (optionally pre-trimmed using dorado)
2. **RSV Reference Fasta** (required): Reference genome containing contigs for both RSV A (rsva_vr1540) and RSV B (rsvb_vr1580)
3. **Primer Bed** (required): BED file containing primer locations on the reference genome
4. **Amplicon BED** (required): BED file containing the boundaries of overlapping amplicons for coverage measurement
5. **Min Depth** (parameter): Minimum depth to call consensus (default: 10, minimum: 1)

### Running the Workflow

1. Log in to your Galaxy account
2. Import the workflow using the link above
3. Upload your sequencing data or use the example data
4. Navigate to **Workflow** in the top menu
5. Find "NEB ONT RSV" and click the dropdown arrow, then select **Run**
6. Configure the workflow inputs:
   - Select your input reads collection
   - Select or upload the RSV reference FASTA
   - Select or upload the primer BED file
   - Select or upload the amplicon BED file
   - Set minimum depth parameter (if different from default)
7. Click **Run Workflow**
8. Monitor progress in your History panel

### Workflow Steps

The workflow performs the following analysis steps:

#### Preprocessing
- **Adapter trimming**: Removes sequencing adapters using Porechop (in case they were not trimmed previously)
- **Quality control**: Assesses read quality using Falco
- **Read alignment**: Maps reads to RSV A/B reference genomes using minimap2
- **Primer clipping**: Removes primer sequences using samtools ampliconclip (also counts the primer usage)

#### Consensus Building
- **Subtype separation**: Splits reads into RSV A and RSV B specific datasets
- **Consensus calling**: Generates consensus sequences for each subtype using ivar consensus
- **Variant calling**: Identifies variants using ivar variants

#### Quality Metrics
- Alignment summary metrics (Picard)
- Mapping statistics (samtools flagstat, idxstats, stats)
- Coverage analysis (mosdepth, QualiMap BamQC)
- Hybrid selection metrics (Picard CollectHsMetrics)
- Primer usage statistics

### Workflow Outputs

The workflow generates the following key outputs:

- **Consensus sequences**: FASTA files for RSV A and RSV B
- **Variant calls**: Tabular file listing detected variants
- **Primer usage**: Combined primer usage statistics across all samples
- **Quality reports**: Comprehensive QC reports including:
  - Coverage plots and statistics
  - Alignment metrics
  - Per-amplicon coverage data
  - Quality control visualizations

### Further Analysis

The workflow authors recommend further assessment of RSV subtypes using [Nextclade](https://clades.nextstrain.org/), which provides clade assignment and mutation analysis for RSV sequences.

### Citation

If you use this workflow in a publication, please acknowledge as follows:

**Creators:**
- Matthew Angel (ORCID: 0000-0002-3882-1504)
- Bradley W. Langhorst (ORCID: 0000-0001-7390-1094)
- New England Biolabs
