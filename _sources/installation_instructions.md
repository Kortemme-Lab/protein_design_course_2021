Installing Prerequrisites
===

### Installing course prerequisites

You may want to create a folder for the class.
mkdir proteindesign
cd proteindesign

Download anaconda:
(MacOS) curl -O https://repo.anaconda.com/archive/Anaconda3-2020.11-MacOSX-x86_64.sh
(Linux) wget https://repo.anaconda.com/archive/Anaconda3-2020.11-Linux-x86_64.sh

Install Anaconda:
./Anaconda3-2020.11-<OS>.sh

Download, install, and activate the following conda environment:
(MacOS) curl-O https://raw.githubusercontent.com/ckrivacic/roseasy/master/conda_env.yml
(Linux) wget https://raw.githubusercontent.com/ckrivacic/roseasy/master/conda_env.yml

conda env create --file conda_env.yml
conda activate proteindesign

Run the following commands to install additional dependencies:

git clone https://github.com/Kortemme-Lab/klab
cd klab
python3 setup.py install
cd ..
git clone https://github.com/ckrivacic/roseasy.git
cd roseasy
git checkout protein_design_course
python3 setup.py install
cd ..

Finally, install PyRosetta:
conda install -c https://levinthal:paradox@conda.graylab.jhu.edu pyrosetta

To make sure PyRosetta installed correctly, run the following:
Open an interactive Python session:
Python
Import PyRosetta and Roseasy to make sure they are installed:
import pyrosetta
import roseasy

No errors should be thrown.

Install wt_vs_mut
In PyMOL, open the Plugin menu at the top and select Plugin Manager.
Click “Install New Plugin”.
In the URL field, paste the following URL and then click “fetch”: 
https://raw.githubusercontent.com/kalekundert/wt_vs_mut/master/wt_vs_mut.py
This allows you to examine specific differences between any proteins you design and their input structure. 
