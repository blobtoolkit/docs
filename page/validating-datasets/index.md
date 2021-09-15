---
title: "Validating datasets"
date: "2019-08-09"
---

The BlobToolKit Validator is included alongside the Specification and can be used to validate BlobDir datasets.

To run the Validator, clone the Specification GitHub repository and ensure you have the `ujson` and `fastjsonschema` python modules available in your environment:

mkdir -p /home/ubuntu/blobtoolkit
cd /home/ubuntu/blobtoolkit
git clone https://github.com/blobtoolkit/specification

pip install ujson fastjsonschema

Run the validator by passing a BlobDir or the `meta.json` file of your BlobDir dataset as an argument to the Validator:

/home/ubuntu/blobtoolkit/specification/validate.py /path/to/BlobDir

or:

/home/ubuntu/blobtoolkit/specification/validate.py /path/to/BlobDir/meta.json

A valid dataset will return `VALID` with a zero exit code, otherwise the Validator will throw an error and print an accompanying message.

By default, the Validator checks for all metadata fields used in the public Viewer instance. To validate a minimal BlobDir, pass the --basic flag to disable checking for assembly accession & prefix and taxon name & taxid:

/home/ubuntu/blobtoolkit/specification/validate.py --basic /path/to/BlobDir
