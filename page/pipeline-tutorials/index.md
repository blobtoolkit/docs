---
title: "Pipeline Tutorials"
date: "2019-07-26"
---

Follow these tutorials to learn how to use the BlobToolKit Pipeline.

#### [Pipeline configuration](https://blobtoolkit.genomehubs.org/pipeline/pipeline-tutorials/configuring-the-pipeline/)

The Pipeline needs to be configured to match your local compute environment and to set parameters specific to your assembly and read data. These configuration options are set in a YAML format file before the Pipeline is run. [Read more...](https://blobtoolkit.genomehubs.org/pipeline/pipeline-tutorials/configuring-the-pipeline/)

#### [Running the Pipeline](https://blobtoolkit.genomehubs.org/pipeline/pipeline-tutorials/running-the-pipeline/)

The Pipeline is implemented as a [Snakemake](https://snakemake.readthedocs.io/en/stable/) workflow that determines which jobs should be run to generate a complete [BlobDir](https://blobtoolkit.genomehubs.org/specification/) dataset based on the [_Pipeline configuration_](https://blobtoolkit.genomehubs.org/pipeline/pipeline-tutorials/pipeline-configuration/). The Pipeline can be run in the same way for both public and local assemblies, provided any local assembly and read files are available in the working directory. [Read more...](https://blobtoolkit.genomehubs.org/pipeline/pipeline-tutorials/running-the-pipeline/)

#### [Running the Pipeline on a cluster](https://blobtoolkit.genomehubs.org/pipeline/pipeline-tutorials/running-the-pipeline-on-a-cluster/)

As a [Snakemake](https://snakemake.readthedocs.io/en/stable/) workflow, the Pipeline can be run on various types of cluster, however some variables may need to be set explicitly. In particular running the `transferCompleted.smk` workflow to validate generated BlobDirs and generate a set of static images requires [Firefox](https://www.mozilla.org/en-GB/firefox/new/) and [Selenium Webdriver](https://www.selenium.dev/projects/) and may need to be run under [Singularity](https://sylabs.io/docs/), using the [BlobToolKit Docker image](https://hub.docker.com/r/genomehubs/blobtoolkit). Read more...
