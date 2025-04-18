# How to add a module to the [wf-paired-end-illumina-workflow](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly)

## Steps for contributing to the workflow

If you'd like to write some code for wf-paired-end-illumina-assembly, the standard workflow is as follows:

1. Check that there isn't already an issue about your idea in the [bacterial-genomics/wf-paired-end-illumina-assembly issues](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/issues) to avoid duplicating work. If there isn't one already, please create one so that others know you're working on this
2. [Fork](https://help.github.com/en/github/getting-started-with-github/fork-a-repo) the [bacterial-genomics/wf-paired-end-illumina-assembly repository](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly) to your GitHub account
3. Make the necessary changes / additions within your forked repository following [Pipeline conventions](#pipeline-contribution-conventions)
4. Use `nf-core schema build` and add any new parameters to the pipeline JSON schema (requires [nf-core tools](https://github.com/nf-core/tools) >= 1.10).
5. Submit a Pull Request against the `dev` branch and wait for the code to be reviewed and merged

If you're not used to this workflow with git, you can start with some [docs from GitHub](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests) or even their [excellent `git` resources](https://try.github.io/).

## Fork the GitHub repository to your GitHub account

The first step is to fork the [wf-paired-end-illumina-workflow](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly) repository:

1. On the [GitHub repository](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly) in the top right corner, click **Fork**.
   ![GitHub fork](images/github_fork.PNG)
2. Under "Owner", select the dropdown menu and click and owner for the forked repository.
3. By default, forks are named the same as their upstream repositories. Optionally, to further distinguish your fork, in the "Repository name" field, type a name.
4. Unselect "Copy the `main` branch only. The new module should be added to the `dev` branch of the workflow.
5. Click **Create fork**.
6. Then clone your forked repository:
   `git clone https://github.com/YOURUSERNAME/wf-paired-end-illumina-assembly.git`
7. Then create a new branch on your forked repository:
   `git checkout -b NEWBRANCHNAME`

Please create a new branch with the appropriate branch name for the module you are trying to add. This will make things easier when reviewing and ultimately merging the branches on the repository.

## Adding a module to the workflow

To add a new module to the workflow, these are all the files necessary to edit to incorporate a new module:

```console
conf/modules.config
conf/params.config
conf/workflow.config
modules/local/NEWMODULE/main.nf
modules/local/NEWMODULE/params.config
nextflow_schema.json
subworkflows/local/NEWMODULE.nf
workflows/assembly.nf
```

For adding a new module to the workflow, it is important to keep up the current naming scheme for the modules, which is function of the module, followed by the name of the program/software being used:

- initial process channel: `ch_output_from_<process>`
- intermediate and terminal channels: `ch_<previousprocess>_for_<nextprocess>`

For example, the program Kraken is used to classify reads and it's module name in the workflow is:

- `modules/local/read_classify_kraken`
- `modules/local/read_classify_kraken2`

Some other examples include:

- `modules/local/map_contigs_bwa`
- `modules/local/mlst_mlst` (looks strange but keeping up with the same naming scheme, this is ok)

### Adding a new step

If you wish to contribute a new step, please use the following coding standards:

1. Define the corresponding input channel into your new process from the expected previous process channel
2. Write the process block (see below).
3. Define the output channel if needed (see below).
4. Add any new parameters to `nextflow.config` with a default (see below).
5. Add any new parameters to `nextflow_schema.json` with help text (via the `nf-core schema build` tool).
6. Add sanity checks and validation for all relevant parameters.
7. Perform local tests to validate that the new code works as expected.
8. If applicable, add a new test command in `.github/workflow/ci.yml`.
9. Add a description of the output files and if relevant any appropriate images from the MultiQC report to `docs/output.md`.

#### Add parameters to

### Default values

Parameters should be initialised / defined with default values in `nextflow.config` under the `params` scope.

Once there, use `nf-core schema build` to add to `nextflow_schema.json`.

### Default processes resource requirements

Sensible defaults for process resource requirements (CPUs / memory / time) for a process should be defined in `conf/base.config`. These should generally be specified generic with `withLabel:` selectors so they can be shared across multiple processes/steps of the pipeline.
A nf-core standard set of labels that should be followed where possible can be seen in the [nf-core pipeline template](https://github.com/nf-core/tools/blob/master/nf_core/pipeline-template/conf/base.config), which has the default process as a single core-process, and then different levels of multi-core configurations for increasingly large memory requirements defined with standardised labels.

The process resources can be passed on to the tool dynamically within the process with the `${task.cpu}` and `${task.memory}` variables in the `script:` block.

### Submitting a pull request

When you are done adding and editing all the required files to incorporate your new module into the workflow, you can submit a pull request on the [wf-paired-end-illumina-workflow](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly) GitHub page.

### Nextflow version bumping

If you are using a new feature from core Nextflow, you may bump the minimum required version of nextflow in the pipeline with: `nf-core bump-version --nextflow . [min-nf-version]`

## Tests

When you create a pull request with changes, [GitHub Actions](https://github.com/features/actions) will run automatic tests.
Typically, pull-requests are only fully reviewed when these tests are passing, though of course we can help out before then.

There are typically two types of tests that run:

### Lint tests

`nf-core` has a [set of guidelines](https://nf-co.re/developers/guidelines) which all pipelines must adhere to.
To enforce these and ensure that all pipelines stay in sync, we have developed a helper tool which runs checks on the pipeline code. This is in the [nf-core/tools repository](https://github.com/nf-core/tools) and once installed can be run locally with the `nf-core lint <pipeline-directory>` command.

If any failures or warnings are encountered, please follow the listed URL for more documentation.

### Pipeline tests

Each `nf-core` pipeline should be set up with a minimal set of test-data.
`GitHub Actions` then runs the pipeline on this data to ensure that it exits successfully.
If there are any failures then the automated tests fail.
These tests are run both with the latest available version of `Nextflow` and also the minimum required version that is stated in the pipeline code.

## Patch

:warning: Only in the unlikely and regretful event of a release happening with a bug.

- On your own fork, make a new branch `patch` based on `upstream/main`.
- Fix the bug, and bump version (X.Y.Z+1).
- A PR should be made on `main` from patch to directly this particular bug.

## Getting help

For further information/help, please consult the [bacterial-genomics/wf-paired-end-illumina-assembly documentation](usage.md) and don't hesitate to create an issue for any clarifications!

## Pipeline contribution conventions

To make the bacterial-genomics/wf-paired-end-illumina-assembly code and processing logic more understandable for new contributors and to ensure quality, we semi-standardise the way the code and other contributions are written.

### Images and figures

For overview images and other documents we follow the nf-core [style guidelines and examples](https://nf-co.re/developers/design_guidelines).

## GitHub Codespaces

This repo includes a devcontainer configuration which will create a GitHub Codespaces for Nextflow development! This is an online developer environment that runs in your browser, complete with VSCode and a terminal.

To get started:

- Open the repo in [Codespaces](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/codespaces)
- Tools installed
  - nf-core
  - Nextflow

Devcontainer specs:

- [DevContainer config](../.devcontainer/devcontainer.json)
