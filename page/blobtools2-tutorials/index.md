---
title: "BlobTools2 Tutorials"
date: "2019-07-26"
---

Learn how to use BlobTools2 by following these tutorials. (_If you're here while they are still being written, take a look at the original_ [_Getting Started with BlobTools2_](https://blobtoolkit.genomehubs.org/blobtools2/blobtools2-tutorials/getting-started-with-blobtools2/) _page for some example commands._)

To run the commands you will either need to use the [BlobToolKit Docker image](https://blobtoolkit.genomehubs.org/install/use-docker/) or follow the instructions to [install BlobToolkit](https://blobtoolkit.genomehubs.org/install/).

#### [Creating a dataset](https://blobtoolkit.genomehubs.org/btk-viewer/viewer-tutorials/public-vs-local-instances/)

The minimum requirement to create a new dataset with BlobTools2 is an assembly FASTA file, but it is possible to create a much richer dataset by adding additional data and metadata. The underlying [BlobDir](https://blobtoolkit.genomehubs.org/specification/) data structure makes it trivial to separate dataset creation from the subsequent addition of analyses (see _[Adding data to a dataset](https://blobtoolkit.genomehubs.org/blobtools2/blobtools2-tutorials/adding-data-to-a-dataset/)_) and presents the data in a format that can be efficiently processed for interactive visualisation. [Read more...](https://blobtoolkit.genomehubs.org/blobtools2/blobtools2-tutorials/creating-a-dataset/)

#### [Adding data to a dataset](https://blobtoolkit.genomehubs.org/btk-viewer/viewer-tutorials/searching-available-datasets/)

Once you have a [BlobDir](https://blobtoolkit.genomehubs.org/specification/) dataset (see [Creating a dataset](https://blobtoolkit.genomehubs.org/blobtools2/blobtools2-tutorials/creating-a-dataset/)), further data can be added by parsing analysis output files into one or more fields using the `blobtools add` command. Dedicated parsers are available for a range of analysis types, assigning values to each contig in an assembly: BLAST or Diamond hits provide taxonomic assignments; read mapping files provide base and read coverage; BUSCO results show completeness metrics for full and filtered assemblies; and fields from generic text files can be imported for maximum flexibility. [Read more...](https://blobtoolkit.genomehubs.org/btk-viewer/viewer-tutorials/searching-available-datasets/)

#### [Updating metadata](https://blobtoolkit.genomehubs.org/blobtools2/blobtools2-tutorials/updating-metadata/)

Dataset metadata, including links to external resources, can be added or updated using the `blobtools add` command. [Read more...](https://blobtoolkit.genomehubs.org/blobtools2/blobtools2-tutorials/updating-metadata/)

#### [Filtering a dataset](https://blobtoolkit.genomehubs.org/blobtools2/blobtools2-tutorials/filtering-a-dataset/)

Coming soon. [Read more...](https://blobtoolkit.genomehubs.org/blobtools2/blobtools2-tutorials/filtering-a-dataset/)

#### [Opening a dataset in the viewer](https://blobtoolkit.genomehubs.org/blobtools2/blobtools2-tutorials/opening-a-dataset-in-the-viewer/)

Coming soon. [Read more...](https://blobtoolkit.genomehubs.org/blobtools2/blobtools2-tutorials/opening-a-dataset-in-the-viewer/)

#### [Generating plots on the command line](https://blobtoolkit.genomehubs.org/blobtools2/blobtools2-tutorials/generating-plots-on-the-command-line/)

Coming soon. [Read more...](https://blobtoolkit.genomehubs.org/blobtools2/blobtools2-tutorials/generating-plots-on-the-command-line/)
