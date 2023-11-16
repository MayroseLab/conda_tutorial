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
To create an environment, we'll start by creating a file detailing the packages included in the environment. Conda expects a specific format (yaml). Here is an example of a env yaml with common packages used in biological data analysis.
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
__The most useful channels__ are `conda-forge` for python libraries and general tools, `bioconda` for bioinformatic tools, and `r` for R packages.  
_Is everything on Anaconda.org?_  
No, but most modern and common packages and tools are. It basically depends on whether or not somebody (the developer or someone else) cared to create a conda package. Due to the popularity of conda, most developers create a package for their tools. Personally, if something is not on Anaconda.org, there will have to be a __very__ good reason for me to use it...  

Anyway... once you have your yaml file, it's time to create the environment. Do that with:
```
conda env create -f <env.yaml>
```
where `<env.yaml>` is the path to the file.  
When you hit Enter, conda will automatically resolve the dependencies for you (that is, it'll choose package versions that play nicely with each other), download the files and install them. This can take a while for envs with lots of packages. If no errors were thrown, then the env was created successfully. To check, you can list the existing envs with `conda env list`.  
__Tip__: it's a good idea to keep your yaml files in a defined location (Github?), so you can always come back to them to rebuild or update your envs. This is also good for reproducibility reasons.

### Activating/deactivating an environment
Now that you have created a conda env, you can activate it with:
```
conda activate <env name>
```
in our example, `<env name>` would be `bio_data`. You should see `(bio_data)` added to the command prompt, which means it is activated. This means all installed packages are now available to you. You can always activate another env using `conda activate <other env name>`. Or you can deactivate an env with `conda deactivate`.  
__important note__: at any time, only one env can be active! This means that if env A has package X and env B has package Y, and you do: `conda activate A` and then `conda activate B`, only package Y is available, and package X is not. __However__, packages not related to conda are still available, which can sometimes be confusing. For example, if package X was previously installed on the machine, regardless of conda, it __will__ be available in env B, but maybe not the version you expect, so be careful!  
You can always list the packages included in the current conda env using `conda list`.

### Updating an environment
If you want to change an env after creating it, simply modify the yaml file (add or remove any packages or versions, but __don't__ change the name), then run: `conda env update -f <env.yaml>`. Note that this can cause other packages to be updated as well, as conda will try to fetch their latest versions.

## Different ways to use conda
You now know all the basics of working with conda and should be able to find your way around. Here are two common ways to work with conda - choose the one you like or make your own.

### 1. "One to rule them all"
If you have one main project in which you keep using the same packages, you only need one env including all of them. Just make one yaml file, create the env and activate it whenever you start working. You can even have it activated automatically by adding the `conda activate` command to your `.bashrc`, or just use the `base` env, which is available even if you don't ctivate anything. You can always add or change the packages by updating the env.  
__Pros__: easy, convenient, and you don't need to care too much about environments.  
__Cons__: When you start to accumulate many packages in your env, the chances of clashes increase. That is, at some point conda will not be able to resolve the dependencies in a way that everything plays nicely together. However, if your env is not too crazy, this shouldn't happen.

### 2. "Divide and conquer"
If you have several projects or fields in which you work, you might want to have multiple envs, and just activate the relevant ones when you need them. Envs could be created per project, or per programming language (`python2`,`python3`,`R`), or per field (`genomics`,`phylogenetics`,`machine_learning`). If you choose to work this way, it's particularly important to keep your yaml files and envs tidy.  
__Pros__: modularity, you only load relevant packages, lower chances of clashes.  
__Cons__: High maintenance, you might need to install the same package in several envs.

## Using conda on Power cluster jobs
Our beloved Power cluster can behave a bit strange around conda envs. To avoid funky behavior, simply include the following lines in the sh file you submit:
```
source ~/.bashrc
conda activate <env name>
export PATH=$CONDA_PREFIX/bin:$PATH
```
In interactive jobs, just activate the env as usual.

## The next snake - mamba
If you work with complex envs containing many (say 20+) packages, you might find that conda takes forever to create and update the env, or even fails miserbly. Luckily, there is another snake in town to sort that out - [mamba](https://github.com/mamba-org/mamba)!  
You can install mamba using conda:
```
conda install mamba -n base -c conda-forge
```
Once installed, you can simply replace `conda` with `mamba` and things should work __much__ faster, e.g.: `mamba env create -f <env.yaml>`. Activate/deactivate are still done with conda.

## Dark side of the snake - how and when NOT to use conda
The main purpose of conda is to allow for easy reproduction of working environments. However, it turns out that in certain cases it **does not** provide stable and reliable reproducibility, especially if environment configuration is sloppy. Here are some tips and warnings:  
### Always set a specific software version for each package
Here are two similar environment YAML files:  
**env1.yml**:
```
name: python_analyze
channels:
  - conda-forge
dependencies:
  - python=3
  - scipy
```
**env2.yml**:
```
name: python_analyze
channels:
  - conda-forge
dependencies:
  - python=3.9.0
  - numpy=1.26.0
  - pandas=2.1.3
  - scipy=1.11.3
```
These two envs will probably work similarly, however if you build `env1.yml` in a few months, you'll get a different env with the latest python and scipy versions, and whatever numpy and pandas versions set by the scipy package. This means that you may get different behavior or even runtime errors when running your code in the future. In contrast, if you use `env2.yml`, the chances for consistent behavior are much better.
### Different conda and mamba versions behave differently
We have seen cases where building an env from the same YAML file with different conda and mamba versions had different outcomes. Sometimes dependencies are solved differently, and sometimes a build will just fail with a specific version but complete with another. Therefore, it might be a good idea to specify the conda (and mamba) versions to use for building an env. Something like this:
```
# build with conda v22.9.0 and mamba v0.27.0
name: python_analyze
channels:
  - conda-forge
dependencies:
  - python=3.9.0
  - numpy=1.26.0
  - pandas=2.1.3
  - scipy=1.11.3
```
### Channels order and settings may affect the outcome
The order of channels listed in the YAML file may affect the way dependencies are resolved and may result in different environments. Moreover, default channel configurations in the `~/.condarc` file may also affect the behavior.
### Some packages contain inherent dependency ambiguity
Even if you follow the suggestions above, reproducibility is still not guaranteed. Why, you ask? let's look at an example:
```
# build with conda v22.9.0 and mamba v0.27.0
name: kingfisher
channels:
  - bioconda
  - rpetit3
dependencies:
  - kingfisher=0.2.2
  - aspera-connect=3.9.6
```
If we look at the dependencies of kingfisher (retrieved using [conda-tree](https://github.com/conda-incubator/conda-tree)), we see:
```
kingfisher==0.2.2
  ├─ aria2 1.36.0 [required: >=1.36.0]
  ...
  │  ├─ libssh2 1.11.0 [required: >=1.10.0,<2.0a0]
  │  │  ├─ libgcc-ng 13.1.0 [required: >=12]
  │  │  │  └─ dependencies of libgcc-ng displayed above
  │  │  ├─ libzlib 1.2.13 [required: >=1.2.13,<1.3.0a0]
  │  │  │  └─ dependencies of libzlib displayed above
  │  │  └─ openssl 3.1.2 [required: >=3.1.1,<4.0a0]
  │  │     ├─ ca-certificates 2023.7.22 [required: any] <--------- PROBLEM HERE
  │  │     └─ libgcc-ng 13.1.0 [required: >=12]
  │  │        └─ dependencies of libgcc-ng displayed above
```
See that `required: any` over there? This means that a dependency of a dependency  of a dependency of a dependency of package kingfisher is not accurately defined, meaning that even though our env YAML is configured well, you could get a different environment in the future. To make things worse, this inconsistency is burried rather deep, meaning it would be hard to detect.
### Bottom line
Conda is a great tool for quickly and easily creating personal working environments. However, it is hard (if not impossible) to guarantee reproducibility in future env builds. Therefore, conda is not recommended if you need long-term stability and reproducibility, e.g. when developing software tools and analysis pipelines that other users should use in the future. For such cases you may want to look into Docker.

## Useful links
* [Conda cheat sheet](https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf)
* [Conda official user guide](https://docs.conda.io/projects/conda/en/latest/user-guide/index.html)
* [Using conda envs in Pycharm](https://www.jetbrains.com/help/pycharm/conda-support-creating-conda-virtual-environment.html)
* [conda-tree - a usefull tool for analyzing and debugging conda envs](https://github.com/conda-incubator/conda-tree)
