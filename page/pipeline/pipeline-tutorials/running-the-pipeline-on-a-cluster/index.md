---
title: "Running the Pipeline on a cluster"
date: "2020-05-13"
---

As a [Snakemake](https://snakemake.readthedocs.io/en/stable/) workflow, the Pipeline can be run on various types of cluster, however some variables may need to be set explicitly. In particular running the `transferCompleted.smk` workflow to validate generated BlobDirs and generate a set of static images requires [Firefox](https://www.mozilla.org/en-GB/firefox/new/) and [Selenium Webdriver](https://www.selenium.dev/projects/) and may need to be run under [Singularity](https://sylabs.io/docs/), using the [BlobToolKit Docker image](https://hub.docker.com/r/genomehubs/blobtoolkit).

The commands below should work within typical job submission scripts on SGE and LSF clusters and assume the install instructions have been used to set up a Conda environment. See [_Configuring the pipeline_](https://blobtoolkit.genomehubs.org/pipeline/pipeline-tutorials/configuring-the-pipeline/) for more information on the configuration file options.

### Set variables

either in a single job script or an array job wrapper:

SOFTWARE=/path/to/software
SCRATCH=/path/to/scratch

# BlobToolKit pipeline directory
export PIPELINE=$SOFTWARE/blobtoolkit/insdc-pipeline

# Working directory should contain the $ASSEMBLY.yaml config file
export WORKDIR=$SCRATCH/workdir

# Set a destination directory for completed analyses if running validation steps
export CLUSTER\_DESTDIR=$SCRATCH/completed

# Directory for Snakemake to create conda environments
export CONDA\_DIR=$SOFTWARE/.conda

# Add btk\_env python packages to PYTHONPATH
export PYTHONPATH=$SOFTWARE/miniconda3/envs/btk\_env/lib/python3.6/site-packages:$PYTHONPATH

# Control resource use - finer grained control available using a cluster.yaml file
export THREADS=32
export MULTICORE=8
export MAXCORE=16

Additional variables are required if using Singularity:

\# Make sure singularity is in the PATH
export PATH=$SOFTWARE/singularity/bin:${PATH}

# Directory for Snakemake to store singularity images
export SINGULARITY\_DIR=$SOFTWARE/.singularity

Running Singularity as described below also requires a minimal copy of your /etc/passwd file:

$ getent passwd "$USER" > $HOME/singularity.etc.passwd

### Run the Pipeline

Place commands to run the pipeline in a job script (can be the same script as variables above if not running array jobs):

\# Be sure to set the assembly name, this should match the YAML config filename
ASSEMBLY=assemblyName

# make sure conda activate is available in script
eval "$(conda shell.bash hook)"

# activate the Conda environment
conda activate btk\_env

# Run snakemake
snakemake -p \\
          --use-conda \\
          --conda-prefix $CONDA\_DIR \\
          --directory $WORKDIR/ \\
          --configfile $WORKDIR/$ASSEMBLY.yaml \\
          --latency-wait 60 \\
          --rerun-incomplete \\
          --stats $ASSEMBLY.snakemake.stats \\
          -j $THREADS \\
          -s $PIPELINE/Snakefile \\
          --resources btk=1

# Test for success
if \[ $? -ne 0 \];then
  echo ERROR: failed during replaceHits
  exit 1
fi

### Use singularity

Validation and image generation steps can be included as part of the job script. Here they also provide an example of additional variables required when using Singularity:

\# Exported paths below are relative to the container filesystem and should not be changed
mkdir -p $CLUSTER\_DESTDIR
export DESTDIR=/blobtoolkit/output
export PYTHONPATH=/home/blobtoolkit/miniconda3/envs/btk\_env/lib/python3.7/site-packages:$PYTHONPATH
export PATH=/home/blobtoolkit/miniconda3/envs/btk\_env/bin:$PATH

# Run the transferCompleted workflow
snakemake -p \\
          --directory $WORKDIR/ \\
          --configfile $WORKDIR/$ASSEMBLY.yaml \\
          --latency-wait 60 \\
          --rerun-incomplete \\
          --stats $ASSEMBLY.snakemake.stats \\
          -j $THREADS \\
          -s $PIPELINE/transferCompleted.smk \\
          --resources btk=1 \\
          --use-singularity \\
          --singularity-prefix $SINGULARITY\_DIR \\
          --singularity-args "-B $WORKDIR:/blobtoolkit/datasets \\
                              -B $CLUSTER\_DESTDIR:/blobtoolkit/output \\
                              -B $HOME/singularity.etc.passwd:/etc/passwd" \\
          --config destdir=$DESTDIR

# Test for success
if \[ $? -ne 0 \];then
  echo ERROR: failed during transferCompleted
  exit 1
fi

## Submit rules as jobs

On a cluster with drmaa, Snakemake can submit each rule as a separate job. This allows for using resources more efficiently, but the large number of jobs generated may affect priority calculations, depending on the cluster policies.

This requires additional options in the Snakemake `run` command. An example `cluster.yaml` file is provided in the [INSDC-Pipeline repository](https://github.com/blobtoolkit/insdc-pipeline):

\# Run snakemake
snakemake -p \\
          --use-conda \\
          --conda-prefix $CONDA\_DIR \\
          --directory $WORKDIR/ \\
          --configfile $WORKDIR/$ASSEMBLY.yaml \\
          --cluster-config $CLUSTER\_CONFIG \\
          --drmaa " -o {log}.o \\
                    -e {log}.e \\
                    -R \\"select\[mem>{cluster.mem}\] rusage\[mem={cluster.mem}\] span\[hosts=1\]\\" \\
                    -M {cluster.mem} \\
                    -n {cluster.threads} \\
                    -q {cluster.queue} " \\
              --latency-wait 60 \\
          --rerun-incomplete \\
          --stats $ASSEMBLY.snakemake.stats \\
          -j $THREADS \\
          -s $PIPELINE/Snakefile \\
          --resources btk=1
