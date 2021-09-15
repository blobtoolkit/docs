---
title: "2019-12"
date: "2019-12-15"
---

[![](images/Screenshot-2019-12-15-at-22.38.23-1024x640.jpg)](https://docs.google.com/presentation/d/1yvY8K0hswL7pY0KVzVXkTTCRFCGYyovYpSARcK18zG0/edit?usp=sharing)

[View slides on Google Slides](https://docs.google.com/presentation/d/1yvY8K0hswL7pY0KVzVXkTTCRFCGYyovYpSARcK18zG0/edit?usp=sharing)

### Dependencies

**Install Firefox** (need the Firefox browser to be able to open plots from the command line):See [https://www.mozilla.org/en-GB/firefox/new/](https://www.mozilla.org/en-GB/firefox/new/)  

**Install Conda** (allows specific software versions to be set up in a discrete environment): 

\# Linux
curl [https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86\_64.sh](https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh) > Miniconda3.sh;
chmod +x Miniconda3.sh;
./Miniconda3.sh;
# Mac
curl [https://repo.continuum.io/miniconda/Miniconda3-latest-MacOSX-x86\_64.sh](https://repo.continuum.io/miniconda/Miniconda3-latest-MacOSX-x86_64.sh) > Miniconda3.sh;
chmod +x Miniconda3.sh;
./Miniconda3.sh;
# Then open a new terminal window before using conda

**Install XQuartz** (Mac only, need to run a version of X11 to be able to open plots from the command line):See [https://www.xquartz.org](https://www.xquartz.org/)

  
**Install BlobToolKit dependencies** (best to run these commands one at a time to make sure there are no errors before proceeding):

\# create a Conda environment with all remaining dependencies
conda create -n btk\_env -c conda-forge -y python=3.6 docopt pyyaml ujson tqdm nodejs=10 yq;
conda activate btk\_env;
conda install -c bioconda -y pysam seqtk;
conda install -c conda-forge -y geckodriver selenium pyvirtualdisplay;
pip install fastjsonschema;

\# fetch BlobToolKit code by cloning the repositories  
mkdir -p ~/blobtoolkit;  
cd ~/blobtoolkit;  
git clone [https://github.com/blobtoolkit/blobtools2](https://github.com/blobtoolkit/blobtools2);  
git clone [https://github.com/blobtoolkit/viewer](https://github.com/blobtoolkit/viewer);  
git clone [https://github.com/blobtoolkit/specification](https://github.com/blobtoolkit/specification);  
git clone [https://github.com/blobtoolkit/insdc-pipeline](https://github.com/blobtoolkit/insdc-pipeline);

\# install the Viewer packages  (It’s safe to ignore warnings about vulnerabilities as you won’t be making the viewer publicly accessible)
cd viewer;
npm install;
cd ..;

# fetch the NCBI taxdump
mkdir -p taxdump;
cd taxdump;
curl -L [ftp://ftp.ncbi.nih.gov/pub/taxonomy/new\_taxdump/new\_taxdump.tar.gz](ftp://ftp.ncbi.nih.gov/pub/taxonomy/new_taxdump/new_taxdump.tar.gz) | tar xzf -;
cd ..;
