Installing Course Prerequrisites
===

## Introduction

For this class, you will need to install the following programs from the terminal via command line:  

- **Anaconda or miniconda:** a Python distribution that makes sharing environments and package installation easier.   
- **PyRosetta:** an interactive Python-based interface for the Rosetta molecular modeling suite.  
- **RosEasy:** a package for managing and analyzing protein design projects.  
- **klab:** a library of useful functions.  

**If you have never used a terminal, the basics will be outlined at the end of this page; it may be useful to look at that first.** 

## Initial steps
You may want to create a folder for the class where you can download and install prerequisites and exercises. 
```bash
mkdir proteindesign
cd proteindesign
```

## Installing Anaconda and creating a virtual environment

Download anaconda:
```bash
(MacOS) curl -O https://repo.anaconda.com/archive/Anaconda3-2020.11-MacOSX-x86_64.sh
(Linux) wget https://repo.anaconda.com/archive/Anaconda3-2020.11-Linux-x86_64.sh
```

Install Anaconda:
```bash
./Anaconda3-2020.11-<OS>.sh
```
You will be asked where you want to install Anaconda; try to remember where you install it in case we need to troubleshoot anything. 

Next we will set up a new conda virtual environment. Each time you open a new terminal, you will need to activate this environment, described below, so that your Python executable can access PyRosetta/RosEasy dependencies.  
To get started, download one of the following environment files, depending on your operating system:  
```bash
(MacOS) curl-O https://raw.githubusercontent.com/ckrivacic/roseasy/protein_design_course/conda_env.yml
(Linux) wget https://raw.githubusercontent.com/ckrivacic/roseasy/protein_design_course/linux_env.yml
```

Next, create the environment:
```bash
(MacOS) conda env create --file conda_env.yml
(Linux) conda env create --file linux_env.yml
```

Now you are ready to activate your environment. **You will need to run this command each time you open a new terminal.**  
```bash
conda activate proteindesign
```

To check whether your environment is active, run  
```bash
which python
```
The output should look something like:  
```bash
/path/to/anaconda/envs/proteindesign/bin/python
```
(where `/path/to/anaconda` is a placeholder for your Anaconda install directory.)

## Installing RosEasy and klab
We need to install these packages manually. Start by cloning the klab repository and installing it:

```bash
git clone https://github.com/Kortemme-Lab/klab
cd klab
python3 setup.py install
cd ..
```

We'll do the same for RosEasy, but in this case we will checkout out a branch that has been tailored for this class.  
```bash
git clone https://github.com/ckrivacic/roseasy.git
cd roseasy
git checkout protein_design_course
python3 setup.py develop
cd ..
```

## Installing PyRosetta
PyRosetta can be installed with a simple conda command:  
```bash
conda install -c https://USERNAME:PASSWORD@conda.graylab.jhu.edu pyrosetta
```
The username and password will be provided via email.


To make sure PyRosetta and RosEasy are installed correctly, open an interactive Python session:
```bash
python
```
Import PyRosetta and Roseasy to make sure they are installed:
```python
import pyrosetta
import roseasy
```

No errors should be thrown.


## Install wt_vs_mut
wt_vs_mut is a PyMOL plugin that will be useful for comparing designs to the input structure. It is not strictly necessary but it is highly recommended that you install it.  

In PyMOL, open the Plugin menu at the top and select Plugin Manager.
Click “Install New Plugin”.
In the URL field, paste the following URL and then click “fetch”: 
https://raw.githubusercontent.com/kalekundert/wt_vs_mut/master/wt_vs_mut.py
This allows you to examine specific differences between any proteins you design and their input structure. 

## Terminal basics

The terminal is a useful way of interacting with your operating system, and is the only way to run many academic programs. 

**To open a new terminal:**

- (MacOS) Using the Spotlight feature (press \<cmd\>+\<space\>), search for "terminal" and press \<enter\>.  
- (Linux) Opening a terminal on Linux will vary from distribution to distribution. Most distrubitions allow you to open a terminal using a keyboard shortcut. Press \<ctrl\> + \<alt\> + t at the same time and a terminal window should appear.

**Navigating a terminal**

To issue a command to the terminal, type the command and press \<enter\>. If the command has any text output, it will appear below your text. You will start in your home directory, which you can prove by issuing the "print working directory" command:
```bash
pwd
```
Below the command, the path to the current directory will be printed.  

**Some other useful commands:**
  
- `ls`: List the files and folders in the current directory
- `cd <path>`: Change the current directory to "path." Paths can be relative to the current directory (e.g. `cd Documents`) or absolute (e.g. `cd /Users/username/Documents`). Absolute paths always start with a forward slash, while relative paths do not. This is true for most commands.  
- `~`: Alias for your home folder.  E.g. `cd ~` will change your directory to your home folder.  
- `cp <src> <dest>`: Copy one or more files or folders from src to dest. E.g. `cp ~/docs/document.txt ~/Documents/` will copy 'document.txt' into the Documents folder, and `cp ~/docs/document.txt ~/Documents/doc.txt` will both copy and rename it.  
- `mv <src> <dest>`: Move one or more files or folders from src to dest.  
- `cat <file>`: Displays the contents of a file in the terminal.  
- `cat <file> | grep <keyword`: Displays all the lines of a file that contain the keyword.  
- `mkdir <folder>`: Create a new folder.  
