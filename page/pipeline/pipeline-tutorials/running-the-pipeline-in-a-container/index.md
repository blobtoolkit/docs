---
title: "Running the Pipeline in a container"
date: "2020-06-12"
---

The Pipeline can be run on local data using Docker or Singularity. When the entire pipeline is run inside a container, paths in the configuration file must be relative to the container filesystem with local directories bound to directories inside the container. An example configuration and Docker command are given below.

Note that this differs from the example given in _[Running the Pipeline on a cluster](https://blobtoolkit.genomehubs.org/pipeline/pipeline-tutorials/running-the-pipeline-on-a-cluster/)_ where the file paths should be relative to the local filesystem as the Snakemake process itself would be run outside of the container filesystem with containers created for specific rules. In that case, the Snakemake workflow would take care of the mapping between filesystems.

#### Docker Configuration

Given a working directory with the following assembly and read files:

```
Dalbomicans_v1.fasta
illumina_reads_1.fastq.gz
illumina_reads_2.fastq.gz
pacbio_library1_subreads.fastq.gz
pacbio_library2_subreads.fastq.gz
```

An $ASSEMBLY.yaml (Dalbomicans\_v1.yaml) file should have paths set relative to the container filesystem:

```
assembly:
  accession: draft
  level: scaffold
  scaffold-count: 26354
  span: 253560284
  prefix: Dalbomicans_v1
busco:
  lineages:
    - diptera_odb9
  lineage_dir: /blobtoolkit/databases/busco
reads:
  single:
    - 
      - pacbio_library1
      - PACBIO_SMRT
    - 
      - pacbio_library2
      - PACBIO_SMRT
reads:
  paired:
    - 
      - illumina_reads
      - ILLUMINA
settings:
  blobtools2_path: /blobtoolkit/blobtools2
  taxonomy: /blobtoolkit/databases/ncbi_taxdump
  tmp: /tmp
  blast_chunk: 100000
  blast_max_chunks: 10
  blast_overlap: 500
  chunk: 1000000
similarity:
  defaults:
    evalue: 1e-25
    max_target_seqs: 10
    root: 1
    mask_ids: 
      - 7215
  databases:
    - 
      local: /blobtoolkit/databases/ncbi_db
      name: nt
      source: ncbi
      tool: blast
      type: nucl
    - 
      local: /blobtoolkit/databases/uniprot_db
      max_target_seqs: 1
      name: reference_proteomes
      source: uniprot
      tool: diamond
      type: prot
  taxrule: bestsumorder
taxon:
  taxid: 7291
  name: Drosophila albomicans
keep_intermediates: true
```

Then the docker container can be run by mounting the working directory along with some local directories to ensure database and snakemake-generated conda environments can be retained once the run is finished:

```
WORKDIR=/path/to/working/directory        # The local working directory

                                          # All databases can be fetched by the pipeline
BUSCO_DIR=/path/to/busco_lineages         # Directory containing BUSCO lineages
UNIPROT_DIR=/path/to/uniprot_2020_06      # Directory containing UniProt database
NT_DIR=/path/to/ncbi_2020_06              # Directory containing NCBI nt database
TAXDUMP_DIR=/path/to/ncbi_taxdump_2020_06 # Directory containing NCBI taxdump

ASSEMBLY=Dalbomicans_v1                   # The assembly prefix
CONDA_DIR=/path/to/.conda                 # A directory to contain the Conda environments for 
                                          # individual Snakemake rules
THREADS=32                                # The maximum number of parallel threads to run


docker run -it --rm --name btk \
           -u $UID:$GROUPS \
           -v $WORKDIR:/blobtoolkit/data \
           -v $BUSCO_DIR:/blobtoolkit/databases/busco \
           -v $UNIPROT_DIR:/blobtoolkit/databases/uniprot_db \
           -v $NT_DIR:/blobtoolkit/databases/ncbi_db \
           -v $TAXDUMP_DIR:/blobtoolkit/databases/ncbi_taxdump \
           -v $CONDA_DIR:/blobtoolkit/.conda \
           genomehubs/blobtoolkit:latest \
           snakemake -p \
                     --use-conda \
                     --conda-prefix /blobtoolkit/.conda \
                     --directory /blobtoolkit/data \
                     --configfile /blobtoolkit/data/$ASSEMBLY.yaml \
                     --stats $ASSEMBLY.snakemake.stats \
                     -j $THREADS \
                     -s /blobtoolkit/insdc-pipeline/Snakefile \
                     --resources btk=1
```
