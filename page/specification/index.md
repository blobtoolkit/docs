---
title: "Specification"
date: "2019-07-26"
---

The BlobToolKit Specification describes the `BlobDir` format required by [BlobTools2](https://blobtoolkit.genomehubs.org/blobtools2/) and the [Viewer](https://blobtoolkit.genomehubs.org/btk-viewer/) and includes a validator that can test a `BlobDir` dataset for departures from the required specification.

[BlobTools2](https://blobtoolkit.genomehubs.org/blobtools2/) is a re-implementation of [BlobTools](https://github.com/DRL/blobtools), written in python3 and based around a “BlobDir” directory of JSON format files. This data structure has been chosen as it can be easily validated using JSON-schema, and it is highly extensible. The BlobToolKit Specification describes this BlobDir format (required by [BlobTools2](https://blobtoolkit.genomehubs.org/blobtools2/) and the [BlobToolKit Viewer](https://blobtoolkit.genomehubs.org/btk-viewer/)) and includes a validator that can test a dataset for departures from the required specification.

Fields in the dataset (GC content, length, coverage from a single sequencing library, taxonomic inference based on BLAST hits, etc.) are represented by separate JSON files, each containing an array of values, with one entry per contig. These values may represent one of several generic datatypes (_identifiers_, _variables_, _categories_, _arrays of categories/variables_ and _arrays of arrays_), allowing the results of new analyses to be incorporated without requiring significant changes to the codebase. Field metadata are collated in a single JSON file allowing basic dataset information to be accessed without loading the full set of values.

DatasetID
+- meta.json                       # Dataset metadata
+- identifiers.json                # Sequence names
+- gc.json                         # Sequence GC-content (variable)
+- length.json                     # Sequence lengths (variable)
+- ncount.json                     # Portion of N bases (variable)
+- {LIBRARYNAME}\_cov.json          # Average per-base coverage in {LIBRARYNAME} read mapping file (variable)
+- {LIBRARYNAME}\_read\_cov.json     # Read coverage in {LIBRARYNAME} read mapping file (variable)
+- {TAXRULE}\_{RANK}.json           # Taxonomic assignments from sequence similarity searches at {RANK} using {TAXRULE} (category)
+- {TAXRULE}\_{RANK}\_cindex.json    # Number of alternative taxa at {RANK} (variable)
+- {TAXRULE}\_{RANK}\_positions.json # Start/end position, NCBI taxon ID and bitscore for each hit (array of arrays)
+- {TAXRULE}\_{RANK}\_score.json.    # Sum of bitscores for assigned taxon at {RANK} (variable)
+- {LINEAGE}\_busco.json            # Complete and fragmented {LINEAGE} BUSCOs (array of arrays)

The BlobDir format was favoured over alternatives such as SQLite or HDF5 as JSON is the native format for the javascript-based [BlobToolKit Viewer](https://blobtoolkit.genomehubs.org/btk-viewer/) and the typical patterns of use require all data for a given field at once.

The basic format of each of the datatypes can be described using a static JSON-schema, however a BlobDir has additional requirements (e.g. that the range of values in a _variable_ field match the range stated in the metadata for that field) that are best tested against dynamically generated schemas. The validator supplements the information in a set of static schema documents based on the BlobDir metadata before testing each field and includes tests for the presence of all expected files.

See the _[Specification Tutorials](https://blobtoolkit.genomehubs.org/specification/specification-tutorials/)_ for more information on how to validate a BlobDir dataset or check out our [open-source code](https://github.com/blobtoolkit/specification) on GitHub.
