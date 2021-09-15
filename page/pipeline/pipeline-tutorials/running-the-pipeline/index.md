---
title: "Running the Pipeline"
date: "2019-08-09"
---

The Pipeline is implemented as a [Snakemake](https://snakemake.readthedocs.io/en/stable/) workflow that determines which jobs should be run to generate a complete [BlobDir](https://blobtoolkit.genomehubs.org/specification/) dataset based on the [_Pipeline configuration_](https://blobtoolkit.genomehubs.org/pipeline/pipeline-tutorials/pipeline-configuration/). The Pipeline can be run in the same way for both public and local assemblies, provided any local assembly and read files are available in the working directory.

Before you start, make sure you have the latest version of the Pipeline from the [blobtoolkit/insdc-pipeline GitHub repository](https://github.com/blobtoolkit/insdc-pipeline) and have installed all dependencies listed in the [README file](https://github.com/blobtoolkit/insdc-pipeline/blob/master/README.md). The GitHub repository is named _insdc-pipeline_ as the Pipeline was developed for the analysis of publicly available (INSDC registered) datasets:

mkdir -p /home/ubuntu/blobtoolkit
cd /home/ubuntu/blobtoolkit
git clone https://github.com/blobtoolkit/insdc-pipeline

Create a working directory. All results will be written to this directory and the final BlobDir dataset will be written as a subdirectory of the working directory. If running the Pipeline on a local assembly, copy the assembly FASTA and read FASTQ files into this directory:

mkdir -p /path/to/working/directory

cp Dalbomicans\_v1.fasta /path/to/working/directory/
cp library\*.fastq.gz /path/to/working/directory/

Create/edit a YAML configuration file for the assembly (see the [_Pipeline configuration_](https://blobtoolkit.genomehubs.org/pipeline/pipeline-tutorials/pipeline-configuration/) tutorial) and place it in the working directory:

cat /path/to/working/directory/Dalbomicans\_v1.yaml

assembly:
  prefix: Dalbomicans\_v1
...
taxon:
  taxid: 7291
  name: Drosophila albomicans
keep\_intermediates: true

Change directory into the Pipeline directory and activate the Conda environment described in the [README file](https://github.com/blobtoolkit/insdc-pipeline/blob/master/README.md):

cd /home/ubuntu/blobtoolkit/insdc-pipeline
conda activate snake\_env

Set some parameters as environment variables to make it clear which values can be changed to suit your local compute environment:

WORKDIR=/path/to/working/directory        # The working directory created above
ASSEMBLY=Dalbomicans\_v1                   # The assembly prefix
CONDA\_DIR=/home/ubuntu/blobtoolkit/.conda # A directory to contain the Conda environments for individual Snakemake rules
THREADS=64                                # The maximum number of parallel threads to run

Run the Pipeline using the variables set above. Additional flags are used to print each command before it is run (`-p`) and to limit the number of concurrent rules using `blobtools add` to prevent multiple processes attempting to update the BlobDir at the same time (`--resources btk=1`):

snakemake -p \\
          --use-conda \\
          --conda-prefix $CONDA\_DIR \\
          --directory $WORKDIR/ \\
          --configfile $WORKDIR/$ASSEMBLY.yaml \\
          --stats $ASSEMBLY.snakemake.stats \\
          -j $THREADS \\
          --resources btk=1

To perform a dry-run to check the config file and see which rules will be used without actually running the Pipeline, add the `-n` flag to the command above.

The Pipeline is likely to take at least several hours to run, depending on the size and contiguity of your assembly and the number of BUSCO lineages. Once complete, the working directory will contain a number of analysis outputs and a BlobDir subdirectory that can be visualised in the [BlobToolKit Viewer](https://blobtoolkit.genomehubs.org/btk-viewer/).
