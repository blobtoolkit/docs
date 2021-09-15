---
title: "Install"
date: "2020-02-10"
---

All BlobToolKit components ([Viewer](https://blobtoolkit.genomehubs.org/btk-viewer/), [BlobTools2](https://blobtoolkit.genomehubs.org/blobtools2/), [Pipeline](https://blobtoolkit.genomehubs.org/pipeline/) and [Specification](https://blobtoolkit.genomehubs.org/specification/)) can be run in [Docker](https://www.docker.com) containers via the [BlobToolKit Docker image](https://hub.docker.com/r/genomehubs/blobtoolkit). Alternatively they can be installed manually using the instructions below.

### Dependencies

To install BlobToolKit without using Docker, you will need Conda and, for some functionality, [Firefox](https://www.mozilla.org/en-GB/firefox/new/) and X11.

- **Conda** – allows specific software versions to be set up in a discrete environment.
- **Firefox** – the Firefox browser is required to be able to open plots from the command line.
- **X11** – an X window system is required to be able to open plots from the command line (available by default on Linux Desktop).

(optional) **Install Firefox and X11**

On Linux Desktop or Mac OS X, Firefox can be installed using the links at [www.mozilla.org/en-GB/firefox/new/](https://www.mozilla.org/en-GB/firefox/new/).

On Linux Server, Firefox and the required X11 components can be installed with the command:

sudo apt update && sudo apt-get -y install firefox xvfb

For Max OS X only, see [www.xquartz.org](https://www.xquartz.org/) to install X11 via XQuartz.

**Install Conda**

Conda can be installed on the command line using the Miniconda installer:

curl [https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86\_64.sh](https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh) > Miniconda3.sh;
chmod +x Miniconda3.sh;
./Miniconda3.sh;
# You must open a new terminal window before using conda as the commands will not be available in the current session

**Use Conda to install remaining dependencies**

conda create -n btk\_env -c conda-forge -y python=3.6 docopt psutil pyyaml ujson tqdm nodejs=10 yq;
conda activate btk\_env;
conda install -c bioconda -y pysam seqtk;
conda install -c conda-forge -y geckodriver selenium pyvirtualdisplay;
pip install fastjsonschema;

**Fetch BlobToolKit code**

mkdir -p ~/blobtoolkit;
cd ~/blobtoolkit;
git clone [https://github.com/blobtoolkit/blobtools2](https://github.com/blobtoolkit/blobtools2);
git clone [https://github.com/blobtoolkit/viewer](https://github.com/blobtoolkit/viewer);
git clone [https://github.com/blobtoolkit/specification](https://github.com/blobtoolkit/specification);
git clone [https://github.com/blobtoolkit/insdc-pipeline](https://github.com/blobtoolkit/insdc-pipeline);

**Install Viewer packages**

cd viewer;
npm install;
cd ..;

There are likely to be some warnings when running this command, these are mostly harmless, e.g. fsevents is a Mac OS X specific dependency). If any packages fail to install, this may prevent the Viewer from functioning so it will be necessary to retry until they can be installed. If you encounter problems, make sure you are running the command with `btk_env` activated, this will ensure that node version 10 is used as some packages cannot be installed under other versions. To reinstall after any errors, remove the `node_modules` subdirectory from the Viewer directory and run `npm install` again. If you continue to encounter problems, please submit a [GitHub issue](https://github.com/blobtoolkit/viewer/issues).

### Databases

A local copy of the [NCBI taxdump](ftp://ftp.ncbi.nih.gov/pub/taxonomy/new_taxdump) is required for any features that use taxonomy information. Typical usage also requires copies of the NCBI nucleotide (nt) and UniProt databases. These can all be fetched automatically when running the BlobToolKit Pipeline, alternatively use the commands below to fetch copies for standalone use.

**1\. Fetch the NCBI Taxdump**

mkdir -p taxdump;
cd taxdump;
curl -L [ftp://ftp.ncbi.nih.gov/pub/taxonomy/new\_taxdump/new\_taxdump.tar.gz](ftp://ftp.ncbi.nih.gov/pub/taxonomy/new_taxdump/new_taxdump.tar.gz) | tar xzf -;
cd ..;

**2\. Fetch the nt database**

mkdir -p nt
wget "ftp://ftp.ncbi.nlm.nih.gov/blast/db/nt.??.tar.gz" -P nt/ && \\
        for file in nt/\*.tar.gz; \\
            do tar xf $file -C nt && rm $file; \\
        done

**  
3\. Fetch and format the UniProt database**

Formatting the UniProt database requires the NCBI taxdump to be downloaded and uncompressed in a sister directory, as described in step 2 above.

mkdir -p uniprot
wget -q -O uniprot/reference\_proteomes.tar.gz \\
 ftp.ebi.ac.uk/pub/databases/uniprot/current\_release/knowledgebase/reference\_proteomes/$(curl \\
     -vs ftp.ebi.ac.uk/pub/databases/uniprot/current\_release/knowledgebase/reference\_proteomes/ 2>&1 | \\
     awk '/tar.gz/ {print $9}')
cd uniprot
tar xf reference\_proteomes.tar.gz

touch reference\_proteomes.fasta.gz
find . -mindepth 2 | grep "fasta.gz" | grep -v 'DNA' | grep -v 'additional' | xargs cat >> reference\_proteomes.fasta.gz

echo -e "accession\\taccession.version\\ttaxid\\tgi" > reference\_proteomes.taxid\_map
zcat _\*/\*_/\*.idmapping.gz | grep "NCBI\_TaxID" | awk '{print $1 "\\t" $1 "\\t" $3 "\\t" 0}' >> reference\_proteomes.taxid\_map

diamond makedb -p 16 --in reference\_proteomes.fasta.gz --taxonmap reference\_proteomes.taxid\_map --taxonnodes ../taxdump/nodes.dmp -d reference\_proteomes.dmnd
cd -

**Fetch any BUSCO lineages that you plan to use**

mkdir -p busco
wget -q -O eukaryota\_odb10.gz "https://busco-data.ezlab.org/v4/data/lineages/eukaryota\_odb10.2020-09-10.tar.gz" \\
        && tar xf eukaryota\_odb10.gz -C busco
