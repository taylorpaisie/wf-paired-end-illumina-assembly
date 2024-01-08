# How to add a module to the [wf-paired-end-illumina-workflow](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly)

## Fork the GitHub [wf-paired-end-illumina-workflow](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly) repository

The first step is to fork the  [wf-paired-end-illumina-workflow](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly) repository:

1. On the [GitHub repository](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly) in the top right corner, click **Fork**.

![GitHub fork](docs/images/github_fork.PNG)

2. Under "Owner", select the dropdown menu and click and owner for the forked repository.

3. By default, forks are named the same as their upstream repositories. Optionally, to further distinguish your fork, in the "Repository name" field, type a name.

4. Unselect "Copy the `main` branch only. The new module should be added to the `dev` branch of the workflow.

5. Click **Create fork**. 

6. Then clone your forked repository:

`git clone git@github.com:YOURUSERNAME/wf-paired-end-illumina-assembly.git`

7. Then create a new branch on your forked repository:

`git checkout -b NEWBRANCHNAME`


