# Conda tutorial
A short tutorial about using the conda package/environment manager, specifically on Power cluster.

This document provides a practical guide for working with conda on Linux OS, specifically the Power cluster in TAU.

## What is Conda and why you need it
__TL;DR__: No more `module load`, no more having to wait for IT folks to install your stuff, no more messy compilations.

Conda is a package manager __and__ an environment manager. What does it mean?
1) __Package manager__ means you can very easily install/uninstall code libraries (python, R, Java, C...) and software tools (Blast, IQTREE, samtools...).
2) __Environment manager__ means you can easily create multiple working envs, each containing different installations, and you can turn them on and off as you like. This is very useful for example if different tools you develop have different dependencies (e.g. python 2/3 or specific versions of Biopython).

## Installing conda
Installing conda is very easy, and you don't need root access or help from IT. Detailed instructions can be found [here](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html).  
Or follow the steps below:
1) Go [here](https://docs.conda.io/en/latest/miniconda.html#linux-installers) and copy the URL of the latest Linux-64 installer.
2) In the command line, download the installer using:  
`wget <URL>`  
You should see a new file called something like `Miniconda3-py39_4.10.3-Linux-x86_64.sh`.
3) To install, run: `bash Miniconda3-py39_4.10.3-Linux-x86_64.sh`
4) Follow the instructions on the screen - generally you can accept all default settings.
5) Close and re-open the terminal.
6) Test by typing: `which conda`. It should print something like `~/miniconda3/condabin/conda`. Then try `conda activate`. If you see no error messages then you're done. Otherwise, follow the instructions on the screen (you might need to do `conda init`).

## Creating conda environments
All set? Great! let's see how to use conda. There are many ways to use conda (see more below) - here is what I think is the best practice.  


## Different ways to use conda

## Using conda on Power cluster jobs


