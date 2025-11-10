# general_pipeline_instructions
This repository contains general instructions for running our lab's pipelines that are based on nextflow and singularity.

Most of our lab's bioinformatic pipelines were implemented as nextflow workflows that use singularity to handle software dependencies.  The goal of this approach is to create pipelines that are reproducible, portable, efficient, and useful for us and others.  Some of these pipelines are implemented in a [nf-core](https://nf-co.re/) manner, using some [nf-core modules](https://github.com/nf-core/modules/tree/master/modules/nf-core), and others are implemented as simpler nextflow workflows.

The purpose of this repository is to provide documentation that describes general instructions for running these pipelines.  Throughout this documentation, I refer to "pipeline-name" generically. This should be replaced with the actual name of the pipeline/repository (e.g. you would want to replace "pipeline-name" with "read_preprocessing" when running our [read preprocessing pipeline](https://github.com/stenglein-lab/read_preprocessing)

# Contents

- [Pipelines](#Pipelines)
- [Running the pipeline](#Running-pipelines)
    - [Running from github](#Running-from-github)
    - [Running test datasets](#Running-test-datasets)
    - [Running a specific version:](#Running-a-specific-version)
    - [Making sure that you are running the latest version](#Making-sure-that-you-are-running-the-latest-version)
    - [Running by cloning the pipeline's repository](#Running-by-cloning-the-pipeline-repository)
- [Inputs](#Inputs)
- [Outputs](#Outputs)
    - [Output directory](#Output-directory)
- [Configurable parameters](#Configurable-parameters)
- [Software Dependencies](#Software-dependencies)
    - [Nextflow](#Nextflow)
    - [Singularity ](#Singularity)
    - [Conda](#Conda)
    - [R libraries](#R-libraries)
- [Getting more help](#Getting-more-help)
    - [Issues with the pipelines](#Issues-with-the-pipelines)


## Pipelines

Some of the pipelines that these instructions relate to include:

Pipeline | Purpose | 
--- | --- | 
**[Read preprocessing](https://github.com/stenglein-lab/read_preprocessing)** | A workflow to perform quality and adapter trimming on Illumina fastq format datasets. |
**[Remapping workflow](https://github.com/stenglein-lab/remapping_workflow)** | A workflow for remapping reads to sets of virus sequences. In our lab we use it to validate virus sequences identified by metagenomic sequencing and to output mapping statistics and plots.  |
**[Species ID](https://github.com/stenglein-lab/species_id)** | A workflow to identify species by mapping reads to a database of discriminating sequences (for instance, to a set of cytochrome c oxidase I sequences)  |
**[Align and Tree](https://github.com/stenglein-lab/align_and_tree)** | A workflow to make alignments and phylogenetic trees from one or more sets of nucleotide or protein sequences. |
**[Taxonomy pipeline](https://github.com/stenglein-lab/taxonomy_pipeline)** | A workflow to taxonomically assess the sequences in an Illumina NGS dataset, with a focus on identifying and characterizing virus sequences.  This pipeline is implemented in older nextflow DSL1 syntax, and must be run with an older version of nextflow. |
**[Read based classification](https://github.com/stenglein-lab/read_based_classification)** | A workflow to perform read-based metagenomic classification. |
**[Viral variant caller](https://github.com/stenglein-lab/viral_variant_caller)** | A workflow to call single nucleotide and structural variants in viral populations  |

## Running pipelines

See the [dependencies section](#Software-dependencies) below for information about the main dependencies required for running these pipelines (including nextflow and singularity).

### Running from github

A simple way to run these pipelines is [directly from github](https://nextflow.io/docs/latest/cli.html#launching-a-project), like this:

```
nextflow run stenglein-lab/pipeline-name -resume -profile singularity  ...additional_input...
```

**...additional_input...**: Each pipeline has certain additional input requirements.  For instance, you must specify a directory containing the input fastq files when running the [read_processing pipeline]().  These input requirements are described separately for each pipeline in the corresponding repository.

### Running test datasets

Most pipeline includes small test datasets or inputs.  These are included in the [test directory](./test/) of each repository.  These datasets serve as positive and negative controls and allow you to test that the pipeline is working as expected, at least for small test datasets.  To use these test datasets, run with the test profile, for instance:

```
nextflow run stenglein-lab/pipeline-name -profile singularity,test
```

The results of the test run will be placed in a `test/results` sub-directory.

### Running a specific version

To run a specific version of a pipeline, use the -r option, for example:

```
nextflow run stenglein-lab/pipeline-name -profile singularity,test -r v1.0
```

### Making sure that you are running the latest version

Nextflow will cache pipeline code in `$HOME/.nextflow/assets/` and may continue to use the cached version, even if the pipeline has newer versions on github.  To remove the locally cached version, which will force nextflow to download and cache the latest version, run:

```
# delete cached code
nextflow drop stenglein-lab/pipeline-name

# now run the latest version
nextflow run stenglein-lab/pipeline-name -profile singularity,test
```

Running nextflow pipelines from github is [described in more detail here](https://www.nextflow.io/docs/latest/sharing.html).

### Running by cloning the pipeline repository

It is also possible to download the pipeline code to a directory of your choosing.  This can be useful if, for instance, you want to modify or debug the code.  You can do this by cloning the repository (or a fork of the repository):

```
# copy the real URL from the repository in question
git clone https://github.com/stenglein-lab/pipeline-name.git

# change to the pipeline base directory
cd pipeline-name

# run the workflow, assuming that main.nf contains the main entry point
nextflow run main.nf -resume -profile singularity **...pipeline-specific-inputs...***
```

## Inputs

Pipeline inputs vary by pipeline.  See individual repositories for more information.

## Outputs

Outputs also vary by pipeline.  See individual repositories for more information about each workflows output.

### Output directory

Output files are placed by default in a `results` directory (or `test/results` when running with `-profile test`).  The output directory location [can be overridden](https://nextflow.io/docs/latest/workflow.html#publishing-files) using the `-output-dir` command line parameter.  For instance:

```
nextflow run stenglein-lab/pipeline-name ... --output-dir some_other_results_directory_name
```

## Configurable parameters

Pipelines have various configurable parameters, which have default values assigned in a nextflow.config config file.  Parameter should be described in each pipeline's documentation, and can be overridden using command line options or additional configuration files, as [described in detail here](https://nextflow.io/docs/latest/config.html).

## Software dependencies

These pipelines have two main dependencies: nextflow and singularity.  These programs must be installed on your computer to run the pipelines.

### Nextflow

To run these pipelines, you will need to be working on a computer that has nextflow installed. Installation instructions can be [found here](https://www.nextflow.io/docs/latest/getstarted.html#installation).  To test if you have nextflow installed, run:

```
nextflow -version
```

These pipelines generally require recent nextflow version, typically >= version 24.10

### Singularity

The pipelines use singularity containers to run programs like cutadapt, BLAST, R, etc.  To use these containers you must be running the pipeline on a computer that has [singularity](https://sylabs.io/singularity) [installed](https://sylabs.io/guides/latest/admin-guide/installation.html).  To test if singularity is installed, run:

```
singularity --version
```

There is no specified minimum version of singularity, but older versions of singularity (<~3.9) will likely not work.  

Singularity containers will be automatically downloaded and stored in a directory named `singularity_cacheDir` in your home directory.  They will only be downloaded once.

### Conda

It is possible to run some of the pipelines using [conda](https://docs.conda.io/en/latest/) to handle dependencies. But it is strongly recommended to use singularity instead of conda.


## Getting more help

Individual repositories/workflows will have additional documentation describing how to use the pipeline.  

Nextflow has [excellent documentation](https://nextflow.io/docs/latest/index.html).

### Issues with the pipelines

If you encounter issues with the pipelines, please feel free to create new issues at the individual repositories.  For instance, [here](https://github.com/stenglein-lab/remapping_workflow/issues).  Please follow [best](https://dev.to/usooldatascience/writing-great-github-issues-for-open-source-projects-a-newbies-guide-2c4d) [practices](https://stackoverflow.com/help/minimal-reproducible-example) for creating issues.  Issues could be with the pipeline code or documentation. 

