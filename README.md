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

## Working with conda environments
All set? Great! let's see how to use conda. There are many ways to use conda (see more below) - here is what I think is the best practice.  
### Creating an environment
To create and environment, we'll start by creating a file detailing the packages included in the environment. Conda expects a specific format (yaml). Here is an example of a env yaml with common packages used in biological data analysis.
```
name: bio_data
channels:
  - conda-forge
  - bioconda
dependencies:
  - python=3
  - pandas
  - numpy
  - seaborn
  - biopython
```
`name` is an identifier of the environment, so we can activate and deactivate it. `dependencies` are the names of the packages you want to install within the environment, and `channels` are the package repositories to be searched. To specify package versions, you can use `<package>=<version>`, and you can be as specific as you like, e.g. `python` vs. `python=3` vs. `python=3.8` vs. `python=3.8.1`.  
OK, so how do you find the packages/channels you want?  
The answer is [Anaconda.org](https://anaconda.org/). Simply go there and search for whatever you want. If you search for pandas, It'll look something like this:
![image](https://user-images.githubusercontent.com/5146503/141656411-4a98bb7c-53e6-4815-a6de-87d42077bd06.png)
where `conda-forge` is the channel, `pandas` is the package, and `1.3.4` is the latest version.  
__The most useful channels__ are `conda-forge` (python libraries and general tools), `bioconda` for bioinformatic tools, and `r` for R packages.  
_Is everything on Anaconda.org?_  
No, but most modern and common packages and tools are. It basically depends on whether or not somebody (the developer or someone else) cared to create a conda package. Due to the popularity of conda, most developers create a package for their tools. Personally, if something is not on Anaconda.org, there will have to be a __very__ good reason for me to use it...  

Anyway... once you have your yaml file, it's time to create the environment. Do that with:
```
conda env create -f <env.yaml>
```
where `<env.yaml>` is the path to the file.  
When you hit Enter, conda will automatically resolve the dependencies for you (that is, it'll choose package versions that play nicely with each other), download the files and install them. This can take a while for envs with lots of packages. If no errors were thrown, then the env was created successfully. To check, you can list the existing envs with `conda env list`.
__Tip__: it's a good idea to keep your yaml files in a defined location, so you can always come back to them to rebuild or update your envs. This is also good for reproducibility reasons.

### Activating/deactivating an environment
Now that you have created a conda env, you can activate it with:
```
conda activate <env name>
```
in our example, `env name` would be `bio_data`. You should see `(bio_data)` added to the command prompt, which means it is activated. This means all installed packages are now available to you. You can always activate another env using `conda activate <other env name>`. Or you can deactivate an env with `conda deactivate`.  
__important note__: at any time, only one env can be active! This means that if env A has package X and env B has package Y, and you do: `conda activate A` and then `conda activate B`, only package Y is available, and package X is not.

### Updating an environment
If you want to change an env after creating it, simply modify the yaml file (add or remove any packages or versions, but __don't__ change the name), then run: `conda env update -f <env.yaml>`. Note that this can cause other packages to be updated as well, as conda will try to fetch their latest versions.

## Different ways to use conda

## Using conda on Power cluster jobs

## The next snake - mamba
