---
title: "Configuring the pipeline"
date: "2019-08-08"
---

The Pipeline needs to be configured to match your local compute environment and to set parameters specific to your assembly and read data. These configuration options are set in a YAML format file before the Pipeline is run. The Pipeline has been designed for use with publicly available assemblies, but most configuration options are equally applicable for use with local datasets. Starting with a configuration file for a publicly available _Drosophila albomicans_ assembly, the configuration is divided into six sections: `assembly`, `busco`, `reads`, `settings`, `similarity`, `taxon` and `keep_intermediates`:

assembly:
  accession: GCA\_000298335.1
  alias: DroAlb\_1.0
  bioproject: PRJNA39511
  biosample: SAMN00003213
  level: scaffold
  scaffold-count: 26354
  span: 253560284
  prefix: ACVV01

busco:
  lineages:
    - diptera\_odb9,
    - arthropoda\_odb9
    - eukaryota\_odb9
  lineage\_dir: /path/to/busco/lineages 

reads:
  paired:
    - 
      - SRR026696
      - ILLUMINA
      - 482114248
      - ftp.sra.ebi.ac.uk/vol1/fastq/SRR026/SRR026696/SRR026696\_1.fastq.gz;ftp.sra.ebi.ac.uk/vol1/fastq/SRR026/SRR026696/SRR026696\_2.fastq.gz
    - 
      - SRR026697
      - ILLUMINA
      - 552054360
      - ftp.sra.ebi.ac.uk/vol1/fastq/SRR026/SRR026697/SRR026697\_1.fastq.gz;ftp.sra.ebi.ac.uk/vol1/fastq/SRR026/SRR026697/SRR026697\_2.fastq.gz
  single: \[\]
  coverage:
    max: 100
    min: 0.5
  
settings:
  blobtools2\_path: /path/to/blobtoolkit/blobtools2
  taxonomy: /path/to/blobtoolkit/taxdump/
  tmp: /tmp
  blast\_chunk: 100000
  blast\_max\_chunks: 10
  blast\_overlap: 500
  chunk: 1000000

similarity:
  defaults:
    evalue: 1e-25
    max\_target\_seqs: 10
    root: 1
    mask\_ids: 
      - 7215
  databases:
    - 
      local: /path/to/databases/ncbi\_2019\_08
      name: nt
      source: ncbi
      tool: blast
      type: nucl
    - 
      local: /ceph/software/databases/uniprot\_2019\_07
      max\_target\_seqs: 1
      name: reference\_proteomes
      source: uniprot
      tool: diamond
      type: prot
  taxrule: bestsumorder

taxon:
  taxid: 7291
  name: Drosophila albomicans

keep\_intermediates: true

#### `assembly`

assembly:
  accession: GCA\_000298335.1
  alias: DroAlb\_1.0
  bioproject: PRJNA39511
  biosample: SAMN00003213
  level: scaffold
  scaffold-count: 26354
  span: 253560284
  prefix: ACVV01

The `assembly` section for a publicly available assembly contains details of the assembly GCA `accession` and `alias` to allow the assembly FASTA to be retrieved from NCBI. Assembly `bioproject` and `biosample` are included to allow direct links to the source data from within the [BlobToolKit Viewer](https://blobtoolkit.genomehubs.org/btk-viewer/). Assembly `level` can be set to _scaffold_, _contig_ or _chromosome_ depending on the top level to which the genome has been assembled. The `scaffold-count` and `span` provide metadata that can be checked against the values obtained when importing the assembly and `span` can be used to determine read coverage prior to mapping. Finally the `prefix` is set to the assembly WGS accession by convention and is used to determine the assembly filename. For local assemblies many of these fields may be omitted and the prefix should be set to the basename of your assembly FASTA file, e.g. if this were a local assembly saved as `Dalbomicans_v1.fasta`:

assembly:
  accession: draft
  level: scaffold
  scaffold-count: 26354
  span: 253560284
  prefix: Dalbomicans\_v1

#### `busco`

busco:
  lineages:
    - diptera\_odb9,
    - arthropoda\_odb9
    - eukaryota\_odb9
  lineage\_dir: /path/to/busco/lineages

Running [BUSCO](https://busco.ezlab.org) analyses as part of the Pipeline is optional, but the section should be present in either case. A separate BUSCO analysis will be run for each of the listed `lineages`. If any of the listed lineages are not already available in the `lineage_dir`, they will be fetched automatically when the Pipeline is run. Valid lineage names are any lineages available from the [BUSCO website](https://busco.ezlab.org). To run the Pipeline without any BUSCO analyses, the list of lineages should be left empty and a minimal `busco` section would be:

busco:
   lineage\_dir: /path/to/busco/lineages
   lineages: \[\]

#### `reads`

reads:
  paired:
    - 
      - SRR026696
      - ILLUMINA
      - 482114248
      - ftp.sra.ebi.ac.uk/vol1/fastq/SRR026/SRR026696/SRR026696\_1.fastq.gz;ftp.sra.ebi.ac.uk/vol1/fastq/SRR026/SRR026696/SRR026696\_2.fastq.gz
    - 
      - SRR026697
      - ILLUMINA
      - 552054360
      - ftp.sra.ebi.ac.uk/vol1/fastq/SRR026/SRR026697/SRR026697\_1.fastq.gz;ftp.sra.ebi.ac.uk/vol1/fastq/SRR026/SRR026697/SRR026697\_2.fastq.gz
  single: \[\]
  coverage:
    max: 100
    min: 0.5

The `reads` section contains information on `paired` and/or `single` ended read files to be mapped to the genome. Either or both of these sections may be present, depending on the read files available for mapping, and both follow the same pattern. Each read file is represented by an array of up to four values: (_i_) _name_, (_ii_) _sequencing platform_, (_iii_) _base count_ and (_iv_) _remote file URL(s)_. For local files, only the first two values are required.

(_i_) For publicly available assemblies, the _name_ should be the SRA accession and must match the accession in the _remote file URL_. For local assemblies the _name_ should match the basename of your read FASTQ file. Local filenames must match the pattern `name.fastq.gz` for single end reads, `name_subreads.fastq.gz` for PacBio reads or `name_1.fastq.gz` and `name_2.fastq.gz` for paired end files.

(_ii_) The _sequencing platform_ is used to set the appropriate parameters for read mapping using [Minimap2](https://github.com/lh3/minimap2). Valid values are "ILLUMINA", "OXFORD\_NANOPORE", "PACBIO\_SMRT" and "LS454".

(_iii_) If set, the _base count_ value is used in conjunction with the `coverage` subsection described below to determine whether read files should be subsampled prior to mapping.

(_iv_) For public assemblies, the _remote file URL(s)_ from which the read files can be obtained must be set so they can be fetched by the Pipeline. for `paired` files, the two URLs should be separated by a semicolon as in the example above.

The `coverage` subsection allows `max` and `min` coverage values to be set based on _base count_ values provided in the `paired` or `single` subsections. Files with a coverage (_base count_ / `assembly.span`) greater than `max` will be subsampled using [seqtk](https://github.com/lh3/seqtk), while files with less than `min` coverage will not be mapped.

To map all reads in local PacBio read files named `library1_subreads.fastq.gz` and `library2_subreads.fastq.gz`, regardless of coverage, the reads section would look like:

reads:
  single:
    - 
      - library1
      - PACBIO\_SMRT
    - 
      - library2
      - PACBIO\_SMRT

To map all reads in local paired Illumina read files named readfile\_1.fastq.gz and readfile\_2.fastq.gz, the reads section would look like:

reads:
  paired:
    - 
      - readfile
      - ILLUMINA

`settings`

settings:
  blobtools2\_path: /path/to/blobtoolkit/blobtools2
  taxonomy: /path/to/databases/taxonomy\_2019\_08
  tmp: /tmp
  blast\_chunk: 100000
  blast\_max\_chunks: 10
  blast\_overlap: 500
  chunk: 1000000

Configuration options in the `settings` section are used to set some program/file locations that are not handled elsewhere and a number of parameters for the BLAST wrapper script. Most Pipeline dependencies are handled using Conda environments, however the `blobtools2_path` must be set to the location of this program on your local system.

The Pipeline requires a local copy of the NCBI taxonomy [new\_taxdump](https://ncbiinsights.ncbi.nlm.nih.gov/2018/02/22/new-taxonomy-files-available-with-lineage-type-and-host-information/), which will be automatically fetched and added to the `taxonomy` directory. This file should match the taxonomy used in the `nt` database (see below) so it is useful to keep an explicit record of the date downloaded in the directory name. Finally, some database preparation steps can use a large amount of disk space so a suitable `tmp` directory should be specified.

The remaining parameters set defaults that need to be included in the file but will not normally need to be changed. These mostly relate to the BLAST wrapper script that splits long scaffold sequences into chunks before running BLAST searches to avoid subsequent taxonomic inference being based on a single region within the sequence. `blast_chunk` value determines a minimum length of the chunks that long scaffolds will be split into by the BLAST wrapper script. `blast_max_chunks` ensures that very long scaffolds will only be split into a limited number of chunks, for scaffolds longer than `blast_chunk` x `blast-max_chunks`, the chunk length will be increased so all subsequences are of equal size. The `blast_overlap` value sets a small overlap between chunks to allow for hits that span the break points. (The similarly named `chunk` parameter is unrelated and should no longer be required but this needs testing.)

#### `similarity`

similarity:
  defaults:
    evalue: 1e-25
    max\_target\_seqs: 10
    root: 1
    mask\_ids: 
      - 7215
  databases:
    - 
      local: /path/to/databases/ncbi\_2019\_08
      name: nt
      source: ncbi
      tool: blast
      type: nucl
    - 
      local: /ceph/software/databases/uniprot\_2019\_07
      max\_target\_seqs: 1
      name: reference\_proteomes
      source: uniprot
      tool: diamond
      type: prot
  taxrule: bestsumorder

The `similarity` section controls the databases and settings used for sequence similarity searches. The structure of this section reflects a slightly more flexible approach to database specification than is currently in use.

Similarity `defaults` are settings applied to all sequence similarity searches and in the example file includes the `evalue` and `max_target_seqs` values. The `root` value is the NCBI Taxonomy ID of the root of the clade that you wish to search against. For analyses of public datasets we always set this to 1 to include all taxa but it provides the option to limit a search to a single kingdom or phylum when analysing local assemblies. `mask_ids` is a list of Taxonomy IDs to for which all descendants will be excluded from the similarity searches. For the analysis of public assemblies we set this to the genus of the taxon being analysed (7215 is _Drosophila_) to avoid assigning taxonomy based on the data in the assembly that may already be in the database we are searching against. For analyses of local assemblies this should usually be set to be an empty list (i.e. `mask_ids: []`).

The `databases` subsection is a list of database-specific parameters. `local` is the path to a local copy of the database (that will be fetched/generated if it does not already exist). `name` is the database name, for NCBI BLAST searches this must match `nt` and be a version 5 database to support the taxonomy-based filtering discussed above. `source` is either "ncbi" or "uniprot" and determines where the database files should be fetched from. `tool` is either "blast" for NCBI BLAST or "diamond" and `type` is "nucl" for nucleotide or "proc" for protein. The flexibility that this implies has not been tested recently so it is best to leave all but `local` unchanged.

The `taxrule` specifies which [BlobTools2](https://blobtoolkit.genomehubs.org/blobtools2/) taxrule should be used to assign taxonomic labels to the assembly scaffolds. In practice, this should always be set to "bestsumorder" as Diamond searches are only performed for scaffolds with no NCBI BLAST hits.

#### `taxon`

taxon:
  taxid: 7291
  name: Drosophila albomicans

The `taxon` section should contain a NCBI `taxid` to allow full taxonomic information to be added to the dataset metadata. The taxon `name` should be the species, subspecies or strain name, as appropriate.

#### `keep_intermediates`

keep\_intermediates: true

`keep_intermediates` is a flag that should be set to "true" if you wish to keep all intermediate files generated by the pipeline. When running the pipeline on public assemblies we set this flag to "false" so some large files are discarded once they have been used. For local assemblies, these files (e.g. read-alignment BAM files) are likely to be useful for further analyses.
