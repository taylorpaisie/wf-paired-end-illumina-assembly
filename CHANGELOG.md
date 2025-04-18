# wf-paired-end-illumina-assembly: Changelog

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/)
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## v3.0.0 - November 15, 2024

### `Added`

- Consistent metrics reported for each read cleaning step (@chrisgulvik)
- Added SeqFu for FastQ format validation (@chrisgulvik)
- Checksum (SHA-512) reporting of intermediate and output files (@chrisgulvik)
- Report full input paths for each sample (@chrisgulvik)
- For assembly depth reporting, added stdev depth metrics; added total paired+single mapped stats (@chrisgulvik)

### `Changed`

- Default uses SeqKit rather than SeqTk for downsampling (@chrisgulvik)
- Output structure and filenames revised (@chrisgulvik)
- For MLST, exclude all MLST databases with a \*\_<int> by default (> 1) to ensure the original MLST database version is used for each taxon (e.g., excludes leptospira_2 and leptospira_3) and avoids inconsistent versions used within a run which would occasionally give one sample a leptospira and a different sample leptospira_3 making it impossible to immediately compare between samples. (@chrisgulvik)
- For MLST, store novel FastA when that situation occurs (@chrisgulvik)
- Sample name in outputs and file content no longer contains assembler name (@chrisgulvik)
- Changed RDP output to exclude unneccesary data columns such as "Phylum\nphylum", "Genus\ngenus" (@chrisgulvik)
- Use both R1 and R2 and only Phred30 for estimate bp input for more accurate estimation of genome size (@chrisgulvik)
- Changed default to always on to store stats and FastA of discarded contigs during biopython filtering (@chrisgulvik)
- Output filenames within `pipeline_info/` changed to show month by name and include day of the week (@chrisgulvik)

### `Fixed`

- Order of operations in Trimmomatic process now ensures final output reads have minimum sequence length (default: 50 bp) (@chrisgulvik)
- Fixed issue with missing column header names in the .kraken_summary.tsv output files (@chrisgulvik)
- Fixed trailing tab character in Kraken1 and Kraken2 output TSV summaries, which made pandas XLSX conversion fail due to different column numbers in header and data (@chrisgulvik)
- Fixed VERSION reporting RDP bug by removing spaces (@chrisgulvik)

### `Updated`

- Coloring of workflow process now corresponds to tab color in XLSX output summary sheet (@chrisgulvik)
- Docker container version updates (@chrisgulvik)
- Updated description on output files based on new files created as well as some renamed output files (@chrisgulvik)

### `Deprecated`

- Removed gene calling from QUAST output summary (@chrisgulvik)

## v2.4.0 - August 28, 2024

### `Added`

- Statistics output files during the downsampling routine are all stored as output files under their CleanedReads/<package name>/ (@chrisgulvik)
- FastQ outputs for individual steps are now saved (perhaps temporary and to a non-default option) (@chrisgulvik)
- SeqKit downsampling option (already had Seqtk) (@chrisgulvik)
- Use of a default seed value for both SeqKit and Seqtk subsampling (@chrisgulvik)

### `Fixed`

- RDP Classifier always failed once before succeeding on retry, so a higher RAM request was given as label to make it succeed on first attempt for speed (@chrisgulvik)
- RDP Classifier data output are not tab-delimited instead of space-delimited (@chrisgulvik)

### `Updated`

- TSV output data files have header names with no spaces, all underscores replaced them (@chrisgulvik)

### `Deprecated`

## v2.3.1 - August 23, 2024

### `Added`

### `Fixed`

- [#92](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/92) Refactored profiles to align with Seqera Tower requirements and added a Tower yaml for reporting (@slsevilla).
- [#88](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/88) Resolved issue where SPAdes fails when a non-integer is used with the memory parameter (@gregorysprenger).
- [#90](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/90) Add SKESA parameters to main params.config file to avoid `null` values (@gregorysprenger).
- [#88](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/88) Resolved issue where SPAdes fails when a non-integer is used with the memory parameter (@gregorysprenger).

### `Updated`

### `Deprecated`

## v2.3.0 - March 20, 2024

### `Added`

- [#82](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/82) Added CAT and CheckM2 to assess the assembly file formed (@chrisgulvik and @gregorysprenger).
- [#83](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/83) Added fastp as an alternative to Trimmomatic (@chrisgulvik and @gregorysprenger).

### `Fixed`

### `Updated`

### `Deprecated`

## v2.2.1 - February 16, 2024

### `Added`

- [#73](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/73) Check if input FastQ files are corrupted before proceeding to downstream analyses (@gregorysprenger).

### `Fixed`

- [#74](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/74) Fix emit statements to catch output files from modules (@gregorysprenger).

### `Updated`

- [#69](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/69) Replace baseDir with projectDir due to deprecation in latest version of nextflow (@gregorysprenger).
- [#71](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/71) Updated path to Kraken1 database in bash wrapper script (@gregorysprenger).
- [#77](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/77) Set max resources for release.yml GitHub action (@gregorysprenger).

### `Deprecated`

## v2.2.0 - February 14, 2024

### `Added`

- [#37](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/37) Allow customization of the MLST module, such as specifying schemes to include/exclude and minimum thresholds (@gregorysprenger).
- [#52](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/52) Concatenate kraken (1 and 2) output summaries and place into the output Summaries directory (@gregorysprenger).
- [#47](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/47) Added RDP Classifier as another tool to classify 16S ribosomal RNA (@taylorpaisie).
- [#51](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/51) Added header to BLAST output file before it is compressed (@gregorysprenger).
- [#35](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/35) Added parameters to customize Trimmomatic parameters (@gregorysprenger).
- [#61](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/61) Summarize all QA outputs and place into Summaries output directory, as well as able to convert TSV files to XLSX and a final excel report workbook (@gregorysprenger).

### `Fixed`

- [#50](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/50) Host removal now runs all samples, QC file checks are implemented for host removal, fixed minimum file size param for host removal, and fixed kraken2 database issue (@gregorysprenger).
- [#67](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/67) Added configuration file for gitleaks so that FastA files are ignored when checking for security tokens with MegaLinter (@gregorysprenger).

### `Updated`

- [#55](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/55) Replace linting GitHub workflows with MegaLinter to allow for more linters to be used, autofix issues and commit, etc. (@gregorysprenger).
- [#45](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/45) Updated output documentation to allow for better readability and interpretation (@gregorysprenger).
- [#56](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/56) Change how images are displayed based on light and dark themes due to GitHub changes (@gregorysprenger).
- [#62](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/62) Ignore warnings for RDP parameters (@taylorpaisie).

### `Deprecated`

## v2.1.0 - January 25, 2024

### `Added`

- [#23](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/23) Added a miscellanous issue template (@taylorpaisie).
- [#25](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/25) Fix broken repository links (@gregorysprenger).
- [#24](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/24) Update .nfcore.yml file to ignore specific nf-core file checks that do not apply to this workflow (@gregorysprenger)
- [#41](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/41) Added ability to run `hostile` in remove_host_hostile channel (@chrisgulvik)
- [#41](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/41) Added ability to run NCBI's SRA Human Scrubber (`scrub.sh`) in remove_host_sra_human_scrubber channel (@chrisgulvik)
  - Added ability to remove the broken sister reads from scrub.sh in the remove_broken_pairs_bbtools_repair channel
  - Added new prepare_db_sra_human_scrubber channel for future proofing in case a DB file retreived eventually gets gunzip compressed
  - Added new update_db_sra_human_scrubber channel to fetch the human_filter.db file and avoid requiring it inside the container environment
- [#41](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/41) Added 2 new test config files to locally run the miniburk test data with and without host removal (@chrisgulvik)
- [#41](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/41) Added a new subworkflow for human host removal `subworkflows/host_removal.nf` (@chrisgulvik)

### `Fixed`

- [#26](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/26) Updated database preparation modules to use `meta.id` to correctly rename `.command.{out,err}` files in the log directory (@gregorysprenger).
- [#30](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/23) Fixed the the parsing of PhiX removal information from BBDuk to be added to the summary files (@gregorysprenger).
- [#31](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/31) Removed the possibility of scientific notation in `Summary.CleanedReads-Bases.tab` file (@gregorysprenger).
- [#30](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/23) Fixed the the parsing of PhiX removal information from BBDuk to be added to the summary files (@gregorysprenger).

### `Updated`

- [#29](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/29) Updated nf-core GTDB-Tk and BUSCO modules to latest release and added `mash_db` parameter for GTDB-Tk (@gregorysprenger).
- [#36](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/36) Updated readme and markdown files in `docs/`, as well as separate skea and spades options in nextflow schema file (@gregorysprenger).
- [#41](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/41) Updated usage to include the new `--host_remove {both,hostile,skip,sra_human_scrubber}`. Default uses "both" SRA Human Scrubber first and then hostile, but options also exist to "skip" host removal entirely, or invoke just one as "hostile" or "sra_human_scrubber" (@chrisgulvik)
- [#41](https://github.com/bacterial-genomics/wf-paired-end-illumina-assembly/pull/41) Updated `workflows/assembly.nf` to run the host removal subworkflow after infile handling and prior to downsampling (@chrisgulvik)

### `Deprecated`

## v2.0.4 - December 8, 2023

### `Added`

- Added ability to use nf-core lint on pipeline.
- Validation parameters added to params.config for nf-core lint.
- Removal of duplicate entries in errors.tsv when using the bash runner scripts.
- Process name is now displayed when QC failures occur.
- Assembler name is appended to sample name in QC filechecks.

### `Fixed`

- Barrnap module now receives all biopython outputs.

### `Updated`

- Renamed container cache parameter and added it to conda, docker, and singularity profiles.
- Added missing parameters to nextflow_schema.json.
- Removed system.exit(1) from extract.record.from.genbank.py script.
- Updated error catching of biopython, barrnap, and QC failure that are added to errors.tsv when using bash runner scripts.
- Use exit 1 instead of exit 2 in barrnap module.
- Minimum nextflow version in github actions now matches the minimum version in nextflow.config.
- Markdown files now use `:::tip` or `:::note` for tips and notes.
- Removed genbank file input for barrnap module as it is not used.
- Updated .nf-core.yml file to ignore certain lints when using nf-core lint.
- Replaced `gregorysprenger` workflow prefixes with `bacterial-genomics`.

### `Deprecated`

- "Bigdata" parameter is no longer used as resources are different for everyone - create a custom config instead.

## v2.0.3 - November 27, 2023

### `Added`

- Catching of errors in bash runner scripts for BBDuk and SPAdes.
- Add sample name and assembler name to outputs.
- Added and sorted error codes in bash runner script to ignore if process is resubmitted and completes.
- Allow output of "fastq" or "fq" files from subsampled reads module.
- Within rosalind_hpc.config, resubmit SPAdes if the exit code is anything other than zero.

### `Fixed`

- Fixed parsing of 16S BLAST database input so that results have species names.
- Quast and Kraken1 software versions information is now parsed correctly.
- Bash runner scripts are updated to correctly parse run information for errors.
- Fixed publishDir for SPAdes and Kraken1 so that output files are placed into outdir.
- Concatenation of QC filechecks to create one large summary.
- Removal of input reads if reads are subsampled to avoid original and subsampled reads to be passed to downstream processes.

### `Updated`

- Ignore header when counting number of lines in genome coverage summary file within bash runner script.
- Changed number of retries for SPAdes in rosalind_hpc.config to 5.
- Removed non-working loop in SPAdes module as the process should be resubmitted to abide by resource constraints.
- Changed rosalind_hpc.config high memory parameter and allow memory to be increased for resubmissions.
- Consolidated input and outputs for each module.
- Updated QC filecheck function to remove failed inputs from downstream processes without terminating entire workflow.
- Parsed out meta information when collecting outputfiles to create a concatenated summary file.
- Added the addition of headers to QC filechecks within each module.
- Dropped forced reformatting of FastQ files within BBDuk module.

### `Deprecated`

## v2.0.2 - November 16, 2023

### `Added`

### `Fixed`

- Added collect operators to database/reference channels to allow for all inputs to be analyzed for each process
- Removed extra slashes '/' from run_assembly.uge-nextflow scripts
- Updated paths for summary email when using run_assembly.uge-nextflow scripts

### `Updated`

### `Deprecated`

## v2.0.1 - November 16, 2023

### `Added`

### `Fixed`

- Made bin directory executable when added to github so Nextflow can use them
- Changed log directory to pipeline_info directory in run_assembly.uge-nextflow scripts
- Removed --gtdb_db from run_assembly.uge-nextflow scripts until GTDB-Tk module version is updated

### `Updated`

### `Deprecated`

## v2.0.0 - November 15, 2023

### `Added`

- nf-core Styling
- Allow samplesheet (XLSX, CSV, TSV) OR directory as input
- Use SKESA instead of SPAdes for assembling contigs
- Downsampling of input FastQ files
- Contig taxonomic classification using GTDB-Tk
- Intra-contig gene information using BUSCO
- Ability to use a BUSCO config file
- Always reformat FastQ files before removing PhiX using BBDuk
- Database prep modules to decompress and extract databases
- Function to check QC File Checks if they fail and place them in the QC log directory to remove the hassle of passing these files to each subsequent module
- Comments in input/output channels in subworkflows to make it easier to reference for future changes/additions
- Added "-\<assembler\>" to outputs after contigs are assembled
- Added headers to each output file
- Option to change the "mode" of SPAdes (ie. isolate, meta, etc.)
- Option to use different k-mer sizes for SPAdes
- Added evalue similarity cut-off to Prokka
- Ability to use a curated protein file along with Prokka's database

### `Fixed`

- Errors in run_assembly.uge-nextflow scripts
- Handle "lock" issues when using the same work directory with run_assembly.uge-nextflow scripts
- Remove duplicate rows and junk information in errors.tsv if workflow fails due to a locked session
- Updated "INFO" messages to be more readable

### `Updated`

- Docker containers to ensure they all have built in tests and removed built-in databases
- More comments and whitespace to separate information in conf/params.config
- Separated kraken1 and kraken2 modules
- Use channels for databases instead of passing params
- Use downloadable database .tar.gz files instead of using built-in databases in docker images
- Converted spades and skesa workflows into one workflow and use subworkflows "downsampling.nf" and "assemble_contigs.nf"
- Renamed assemble*{spades,skesa} to assemble_contigs*{spades,skesa}
- **CHANGED OUTPUT FILE STRUCTURE:** Output files are generally under the name of the tool that produced them
- Updated docs/output.md to reflect new file structure
- Removed QC file checking from modules and use checkQCFileChecks function in workflows/assembly.nf and subworkflows/assemble_contigs.nf
- Renamed emit names to hopefully clarify what each output means
- Moved all `publishDir` information to conf/modules.config
- Set filter contig length to 1000
- Place .command.{out,err} on one line for each module
- Added content section to main README.md to allow for easier navigation
- Added BUSCO and GTDB-Tk references to CITATIONS.md
- Parsing of BBDuk output to add more information to a PhiX Summary file
- Fixed github actions to properly run

### `Deprecated`

## v1.0.0 - January 20, 2023

Initial release of wf-paired-end-illumina-assembly.
