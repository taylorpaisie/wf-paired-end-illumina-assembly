# wf-paired-end-illumina-assembly: Usage

## Contents

- [FastQ input directory](#fastq-input-directory)
- [Samplesheet usage](#samplesheet-usage)
- [Samplesheet format](#samplesheet-format)
- [Multiple runs of the same sample](#multiple-runs-of-the-same-sample)
- [Running the pipeline](#running-the-pipeline)
- [Reproducibility](#reproducibility)
- [Core Nextflow arguments](#core-nextflow-arguments)
- [Custom configuration](#custom-configuration)
  - [Resource requests](#resource-requests)
  - [Updating containers (advanced users)](#updating-containers)
  - [nf-core/configs](#nf-coreconfigs)
- [Azure Resource Requests](#azure-resource-requests)
- [Running in the background](#running-in-the-background)
- [Nextflow memory requirements](#nextflow-memory-requirements)

## FastQ input directory

You will need the path to a directory containing paired-end FastQ files. This will import and attempt to assemble all FastQ files in the specified directory, as well as subdirectories.

```console
--input '/path/to/fastq/directory'
```

Please note the following requirements:

- File names must be unique
- File names must not include spaces
- File names must be separated by `_{1,2}`, such as `_R1` and `_R2`
- Valid file extensions: `.fastq.`, `.fq` with optional gzip compression (`.gz`)

## Samplesheet usage

You will need to create a samplesheet with information about the samples you would like to analyze before running the pipeline. Use this parameter to specify its location. Samplesheet can be a comma-separated (CSV), tab-separated (TSV), or Microsoft Excel file (XLSX) file with 3 columns, and a header row as shown in the examples below.

```console
--input '[path to samplesheet file]'
```

### Samplesheet format

The samplesheet can have as many columns as you desire, however, there is a strict requirement for the first 3 columns to match those defined in the table below.

A final samplesheet file consisting of only paired-end data may look something like the one below. This is for 3 samples:

```console
sample,fastq_1,fastq_2
CONTROL_REP1,AEG588A1_S1_L002_R1_001.fastq.gz,AEG588A1_S1_L002_R2_001.fastq.gz
CONTROL_REP2,AEG588A2_S2_L002_R1_001.fastq.gz,AEG588A2_S2_L002_R2_001.fastq.gz
CONTROL_REP3,AEG588A3_S3_L002_R1_001.fastq.gz,AEG588A3_S3_L002_R2_001.fastq.gz
```

| Column    | Description                                                                                                                                                                            |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `sample`  | Custom sample name. This entry will be identical for multiple sequencing libraries/runs from the same sample. Spaces in sample names are automatically converted to underscores (`_`). |
| `fastq_1` | Full path to FastQ file for Illumina short reads 1. File must be gzipped and have the extension ".fastq.gz" or ".fq.gz".                                                               |
| `fastq_2` | Full path to FastQ file for Illumina short reads 2. File must be gzipped and have the extension ".fastq.gz" or ".fq.gz".                                                               |

An [example samplesheet](../assets/samplesheet.csv) has been provided with the pipeline.

### Multiple runs of the same sample

The `sample` identifiers must be the same when you have re-sequenced the same sample more than once e.g., to increase sequencing depth. The pipeline will concatenate the raw reads before performing any downstream analysis. Below is an example for the same sample sequenced across 3 lanes:

```console
sample,fastq_1,fastq_2
CONTROL_REP1,AEG588A1_S1_L002_R1_001.fastq.gz,AEG588A1_S1_L002_R2_001.fastq.gz
CONTROL_REP1,AEG588A1_S1_L003_R1_001.fastq.gz,AEG588A1_S1_L003_R2_001.fastq.gz
CONTROL_REP1,AEG588A1_S1_L004_R1_001.fastq.gz,AEG588A1_S1_L004_R2_001.fastq.gz
```

## Running the pipeline

The typical command for running the pipeline with `docker` is as follows:

With `input_directory`:

```bash
nextflow run \
  bacterial-genomics/wf-paired-end-illumina-assembly \
  --input input_directory \
  --outdir <outdir> \
  -profile docker
```

With `samplesheet.csv`:

```bash
nextflow run \
  bacterial-genomics/wf-paired-end-illumina-assembly \
  --input samplesheet.csv \
  --outdir <outdir> \
  -profile docker
```

This will launch the pipeline with the `docker` configuration profile. See below for more information about profiles.

Note that the pipeline will create the following files in your working directory:

```console
work                # Directory containing the nextflow working files
<outdir>            # Finished results in specified location (defined with --outdir)
.nextflow_log       # Log file from Nextflow
# Other nextflow hidden files, e.g., history of pipeline runs and old logs.
```

### Updating the pipeline

When you run the above command, Nextflow automatically pulls the pipeline code from GitHub and stores it as a cached version. When running the pipeline after this, it will always use the cached version if available - even if the pipeline has been updated since. To make sure that you're running the latest version of the pipeline, make sure that you regularly update the cached version of the pipeline:

```bash
nextflow pull bacterial-genomics/wf-paired-end-illumina-assembly
```

### Reproducibility

It is a good idea to specify a pipeline version when running the pipeline on your data. This ensures that a specific version of the pipeline code and software are used when you run your pipeline. If you keep using the same tag, you'll be running the same version of the pipeline, even if there have been changes to the code since.

First, go to the [wf-paired-end-illumina-assembly releases page](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/releases) and find the latest pipeline version - numeric only (e.g., `1.0.0`). Then specify this when running the pipeline with `-r` (one hyphen) - e.g., `-r 1.0.0`. Of course, you can switch to another version by changing the number after the `-r` flag.

This version number will be logged in reports when you run the pipeline, so that you'll know what you used when you look back in the future.

## Core Nextflow arguments

> [!NOTE]
> These options are part of Nextflow and use a _single_ hyphen (pipeline parameters use a double-hyphen).

### `-profile`

Use this parameter to choose a configuration profile. Profiles can give configuration presets for different compute environments.

Several generic profiles are bundled with the pipeline which instruct the pipeline to use software packaged using different methods (Docker, Singularity, Conda) - see below.

> [!TIP]
> We highly recommend the use of Docker or Singularity containers for full pipeline reproducibility, however when this is not possible, Conda is also supported.

The pipeline also dynamically loads configurations from [https://github.com/nf-core/configs](https://github.com/nf-core/configs) when it runs, making multiple config profiles for various institutional clusters available at run time. For more information and to see if your system is available in these configs please see the [nf-core/configs documentation](https://github.com/nf-core/configs#documentation).

Note that multiple profiles can be loaded, for example: `-profile test,docker` - the order of arguments is important!
They are loaded in sequence, so later profiles can overwrite earlier profiles.

If `-profile` is not specified, the pipeline will run locally and expect all software to be installed and available on the `PATH`. This is _not_ recommended since it can lead to different results on different machines dependent on the computer environment.

- `test`
  - A profile with a complete configuration for automated testing
  - Includes links to test data so needs no other parameters
- `docker`
  - A generic configuration profile to be used with [Docker](https://docker.com/)
- `singularity`
  - A generic configuration profile to be used with [Singularity](https://sylabs.io/docs/)
- `conda`
  - A generic configuration profile to be used with [Conda](https://conda.io/docs/). Please only use Conda as a last resort i.e., when it's not possible to run the pipeline with Docker or Singularity.

### `-resume`

Specify this when restarting a pipeline. Nextflow will use cached results from any pipeline steps where the inputs are the same, continuing from where it got to previously. For input to be considered the same, not only the names must be identical but the files' contents as well. For more info about this parameter, see [this blog post](https://www.nextflow.io/blog/2019/demystifying-nextflow-resume.html).

You can also supply a run name to resume a specific run: `-resume [run-name]`. Use the `nextflow log` command to show previous run names.

### `-c`

Specify the path to a specific config file (this is a core Nextflow command). See the [nf-core website documentation](https://nf-co.re/usage/configuration) for more information.

## Custom configuration

### Resource requests

Whilst the default requirements set within the pipeline will hopefully work for most people and with most input data, you may find that you want to customize the compute resources that the pipeline requests. Each step in the pipeline has a default set of requirements for number of CPUs, memory, and time. For most of the steps in the pipeline, if the job exits with any of the error codes specified [here](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/blob/main/conf/base.config?plain=1#L13) it will automatically be resubmitted with higher requests (2 x original, then 3 x original). If it still fails after the third attempt then the pipeline execution is stopped.

For example, if the pipeline is failing after multiple re-submissions of the `READ_CLASSIFY_KRAKEN_ONE` process due to an exit code of `137` this would indicate that there is an out of memory issue:

```console
[62/149eb0] NOTE: Process `SPADES:READ_CLASSIFY_KRAKEN_ONE (test)` terminated with an error exit status (137) -- Execution is retried (1)
Error executing process > 'SPADES:READ_CLASSIFY_KRAKEN_ONE (test)'

Caused by:
    Process `SPADES:READ_CLASSIFY_KRAKEN_ONE (test)` terminated with an error exit status (137)

Command executed:
    kraken \
      --db /kraken_database/ \
      --threads 12 \
      --fastq-input \
      --gzip-compressed \
      test_R1.paired.fq.gz test_R2.paired.fq.gz test_single.fq.gz \
      > test_kraken.output

Command exit status:
    137

Command output:
    (empty)

Command error:
    .command.sh: line 9:  30 Killed    kraken --db /kraken_database/ --threads 12 --fastq-input --gzip-compressed test_R1.paired.fq.gz test_R2.paired.fq.gz test_single.fq.gz  > test_kraken.output

Work dir:
    /home/wf-paired-end-illumina-assembly/work/9d/172ca5881234073e8d76f2a19c88fb

Tip: you can replicate the issue by changing to the process work dir and entering the command `bash .command.run`
```

#### For beginners

A first step to bypass this error, you could try to increase the number of CPUs, memory, and time for the whole pipeline. Therefor you can try to increase the resource for the parameters `--max_cpus`, `--max_memory`, and `--max_time`. Based on the error above, you must increase the amount of memory. Therefore, you can go to the [params.config](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/blob/main/conf/params.config?plain=1#L48) and see the default value for memory. In this case 128.GB, you than can try to run your pipeline again with `--max_memory 200GB -resume` to skip all process, that were already calculated. If you cannot increase the resource of the complete pipeline, you can try to adapt the resource for a single process as mentioned below.

#### Advanced option on process level

To bypass this error, you would need to find exactly which resources are set by the `READ_CLASSIFY_KRAKEN_ONE` process. The quickest way is to search for `process READ_CLASSIFY_KRAKEN_ONE` in the [wf-paired-end-illumina-assembly GitHub repo](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/search?q=process+READ_CLASSIFY_KRAKEN_ONE).
We have standardized the structure of Nextflow DSL2 pipelines such that all module files will be present in the `modules/` directory and so, based on the search results, the file we want is `modules/local/read_classify_kraken/main.nf`.
If you click on the link to that file you will notice that there is a `label` directive at the top of the module that is set to [`label process_high`](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/blob/main/modules/local/read_classify_kraken/main.nf?plain=1#L11).
The [Nextflow `label`](https://www.nextflow.io/docs/latest/process.html#label) directive allows us to organize workflow processes in separate groups which can be referenced in a configuration file to select and configure subset of processes having similar computing requirements.
The default values for the `process_high` label are set in the pipeline's [`base.config`](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/blob/main/conf/base.config?plain=1#L33-L37) which in this case is defined as 72.GB.
Providing you haven't set any other standard nf-core parameters to **cap** the [maximum resources](https://nf-co.re/usage/configuration#max-resources) used by the pipeline then we can try and bypass the `READ_CLASSIFY_KRAKEN_ONE` process failure by creating a custom config file that sets at least 72.GB of memory, in this case increased to 100.GB.
The custom config below can then be provided to the pipeline via the [`-c`](#-c) parameter as highlighted in previous sections.

```nextflow
process {
    withName: 'SPADES:READ_CLASSIFY_KRAKEN_ONE' {
        memory = 100.GB
    }
}
```

> [!NOTE]
> We specify the full process name i.e., `SPADES:READ_CLASSIFY_KRAKEN_ONE` in the config file because this takes priority over the short name (`READ_CLASSIFY_KRAKEN_ONE`) and allows existing configuration using the full process name to be correctly overridden.

> [!TIP]
> If you get a warning suggesting that the process selector isn't recognized check that the process name has been specified correctly.

### Updating containers

The [Nextflow DSL2](https://www.nextflow.io/docs/latest/dsl2.html) implementation of this pipeline uses one container per process which makes it much easier to maintain and update software dependencies. If for some reason you need to use a different version of a particular tool with the pipeline then you just need to identify the `process` name and override the Nextflow `container` definition for that process using the `withName` declaration. For example, in the [nf-core/viralrecon](https://nf-co.re/viralrecon) pipeline a tool called [Pangolin](https://github.com/cov-lineages/pangolin) has been used during the COVID-19 pandemic to assign lineages to SARS-CoV-2 genome sequenced samples. Given that the lineage assignments change quite frequently it doesn't make sense to re-release the nf-core/viralrecon every time a new version of Pangolin has been released. However, you can override the default container used by the pipeline by creating a custom config file and passing it as a command-line argument via `-c custom.config`.

1. Check the default version used by the pipeline in the module file for [Pangolin](https://github.com/nf-core/viralrecon/blob/a85d5969f9025409e3618d6c280ef15ce417df65/modules/nf-core/software/pangolin/main.nf#L14-L19)
2. Find the latest version of the Biocontainer available on [Quay.io](https://quay.io/repository/biocontainers/pangolin?tag=latest&tab=tags)
3. Create the custom config accordingly:

   - For Docker:

   ```nextflow
   process {
       withName: PANGOLIN {
           container = 'quay.io/biocontainers/pangolin:3.0.5--pyhdfd78af_0'
       }
   }
   ```

   - For Singularity:

   ```nextflow
   process {
       withName: PANGOLIN {
           container = 'https://depot.galaxyproject.org/singularity/pangolin:3.0.5--pyhdfd78af_0'
       }
   }
   ```

   - For Conda:

   ```nextflow
   process {
       withName: PANGOLIN {
           conda = 'bioconda::pangolin=3.0.5'
       }
   }
   ```

> [!NOTE]
> If you wish to periodically update individual tool-specific results (e.g., Pangolin) generated by the pipeline then you must ensure to keep the `work/` directory otherwise the `-resume` ability of the pipeline will be compromised and it will restart from scratch.

### nf-core/configs

In most cases, you will only need to create a custom config as a one-off but if you and others within your organization are likely to be running nf-core pipelines regularly and need to use the same settings regularly it may be a good idea to request that your custom config file is uploaded to the `nf-core/configs` git repository. Before you do this please can you test that the config file works with your pipeline of choice using the `-c` parameter. You can then create a pull request to the `nf-core/configs` repository with the addition of your config file, associated documentation file (see examples in [`nf-core/configs/docs`](https://github.com/nf-core/configs/tree/master/docs)), and amending [`nfcore_custom.config`](https://github.com/nf-core/configs/blob/master/nfcore_custom.config) to include your custom profile.

See the main [Nextflow documentation](https://www.nextflow.io/docs/latest/config.html) for more information about creating your own configuration files.

If you have any questions or issues please send us a message on [Slack](https://nf-co.re/join/slack) on the [`#configs` channel](https://nfcore.slack.com/channels/configs).

## Azure Resource Requests

To be used with the `azurebatch` profile by specifying the `-profile azurebatch`.
We recommend providing a compute `params.vm_type` of `Standard_D16_v3` VMs by default but these options can be changed if required.

Note that the choice of VM size depends on your quota and the overall workload during the analysis.
For a thorough list, please refer the [Azure Sizes for virtual machines in Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes).

## Running in the background

Nextflow handles job submissions and supervises the running jobs. The Nextflow process must run until the pipeline is finished.

The Nextflow `-bg` flag launches Nextflow in the background, detached from your terminal so that the workflow does not stop if you log out of your session. The logs are saved to a file.

Alternatively, you can use `screen` / `tmux` or similar tool to create a detached session which you can log back into later.
Some HPC setups also allow you to run nextflow within a cluster job submitted your job scheduler (from where it submits more jobs).

## Nextflow memory requirements

In some cases, the Nextflow Java virtual machines can start to request a large amount of memory.
We recommend adding the following line to your environment to limit this (typically in `~/.bashrc` or `~./bash_profile`):

```bash
NXF_OPTS='-Xms1g -Xmx4g'
```
