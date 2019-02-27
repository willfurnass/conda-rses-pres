<img src="images/conda_logo.svg" alt="Conda logo" />
### Yet another package manager!

Will Furnass

Research Software Engineering team, University of Sheffield

2019-02-27

[https://learningpatterns.me/conda-rses-pres](https://learningpatterns.me/conda-rses-pres)

---
### Overview

 1. Problem of dependency management for research computing workflows
 2. Basics of conda
 3. Workflow recommendations

---
### Problem

  - Software infrequently exists in a vacuum
  - Your research workflow has tree of software dependencies e.g.
      - Python/R packages and/or
      - Scientific C/C++ libraries and/or
      - 'Operating system packages' (*often ignored!*)
  - Dependencies have dependencies have dependencies have...
  - Behaviour is version-specific

---
### Requirements for (many) research computing workflows

**Portability** and **reproducibility** 

More specifically:

  - For us and other users not to be tied to a given OS/environment
  - To work as an unprivileged user
  - Want to be able to use pkg versions *we want*, regardless of OS pkgs
  - Separation / independence from OS pkgs to ensure/test portability
  - Can use different versions of pkg X in different projects

---
### Options

  - OS package manager: 
    - Cannot have multiple versions of same pkg
    - Cannot use on HPC unless sysadmin
    - Small selection of old pkgs?

---
- Python's `virtualenv` tool:
    - can create isolated set of Python packages as non-sysadmin
    - but not non-Python packages
        - inc Python itself!
    - need to manually keep virtualenv pkgs and OS packages in sync 
        - yuk!

---
&nbsp;
  - Conda package manager
  - Other approaches (inc. Singularity, Docker, spack, EasyBuild, nix)

---
### Conda

Yet another pkg manager (groan) *but*:

  - A conda pkg does not need to be Python package
  - Provides package *environments* 
      - Keep OS and workflow pkgs separate (even compiled libraries) 
      - Can do *everything* as non-sysadmin
      - Different pkgs and pkg versions per env 

---
  - Binary-only packages
      - Built for Windows (32b, 64b), macOS, Linux (32b, 64b) and 
      - (optionally) for Py vers (+ maybe key dependency vers)
          - e.g. Binary pkg for Linux 64bit + Py 3.6 + `numpy` 1.12 

---
### Installing (Mini)conda

  - Download **Miniconda** 3 installer from [https://repo.continuum.io/miniconda/](https://repo.continuum.io/miniconda/)
  - Run installer (using e.g. `bash /Miniconda3-latest-Linux-x86_64.sh ` on macOS / Linux)
  - Install into default location (e.g. `~/miniconda3`) 

*NB conda on local HPC but is old - best to install into /home or /data dir*

---

  - Suggestion: do not make Miniconda Python the default Python
      - **macOS / Linux** don't prepend the install dir to your `PATH` 
      - **Windows**: don't add Miniconda Python to registry 
  - Instead:
      - **Windows**: Start a Miniconda shell from the *Start* menu. 
      - **Linux / macOS**: `source` the `conda.sh` script (adds conda dir to your `$PATH`):

---
### Making conda available

```
$ python --version
Python 3.6.0

$ source ~/miniconda3/etc/profile.d/conda.sh 

$ conda --version
conda 4.5.12
$ which python
/usr/bin/python
$ python --version
Python 3.6.0
```

---
### Activating the 'base' conda environment
```
$ conda activate base

(base) $ which python
/home/will/miniconda3/bin/python
(base) $ python --version
Python 3.7.1

(base) $ conda deactivate 

$ python --version
Python 3.6.0
```


---
### Listing packages in a conda env

'Small' number of pkgs installed in base env:

```
(base) $ conda list
# packages in environment at /home/will/miniconda3:
#
# Name                    Version                   Build  Channel
asn1crypto                0.24.0                   py37_0    defaults
ca-certificates           2018.03.07                    0    defaults
certifi                   2018.11.29               py37_0    defaults
cffi                      1.11.5           py37he75722e_1    defaults
chardet                   3.0.4                    py37_1    defaults
conda                     4.5.12                   py37_0    defaults
conda-env                 2.6.0                         1    defaults
cryptography              2.4.2            py37h1ba5d50_0    defaults
idna                      2.8                      py37_0    defaults
libedit                   3.1.20170329         h6b74fdf_2    defaults
libffi                    3.2.1                hd88cf55_4    defaults
libgcc-ng                 8.2.0                hdf63c60_1    defaults
libstdcxx-ng              8.2.0                hdf63c60_1    defaults
ncurses                   6.1                  he6710b0_1    defaults
openssl                   1.1.1a               h7b6447c_0    defaults
pip                       18.1                     py37_0    defaults
pycosat                   0.6.3            py37h14c3975_0    defaults
pycparser                 2.19                     py37_0    defaults
pyopenssl                 18.0.0                   py37_0    defaults
pysocks                   1.6.8                    py37_0    defaults
python                    3.7.1                h0371630_7    defaults
readline                  7.0                  h7b6447c_5    defaults
requests                  2.21.0                   py37_0    defaults
ruamel_yaml               0.15.46          py37h14c3975_0    defaults
setuptools                40.6.3                   py37_0    defaults
six                       1.12.0                   py37_0    defaults
sqlite                    3.26.0               h7b6447c_0    defaults
tk                        8.6.8                hbc83047_0    defaults
urllib3                   1.24.1                   py37_0    defaults
wheel                     0.32.3                   py37_0    defaults
xz                        5.2.4                h14c3975_4    defaults
yaml                      0.1.7                had09818_2    defaults
zlib                      1.2.11               h7b6447c_3    defaults

```

---

NB includes:

 - Python pkgs e.g. 
    - `requests`
 - non-Python (inc. compiled) pkgs e.g.
    - `zlib`
    - C++ standard library

---
### Creating a new environment

- (Ideally) create a new env (distinct from `base`) per project.
- Need a name and some pkgs (optionally with version nums and build nums):

```
$ conda create --name room101 python=3.7 beautifulsoup4
...
  environment location: /home/will/miniconda3/envs/room101
...
The following NEW packages will be INSTALLED:

    beautifulsoup4:  4.7.1-py37_1            defaults
    ca-certificates: 2019.1.23-0             defaults
    certifi:         2018.11.29-py37_0       defaults
    libedit:         3.1.20181209-hc058e9b_0 defaults
    libffi:          3.2.1-hd88cf55_4        defaults
    libgcc-ng:       8.2.0-hdf63c60_1        defaults
    libstdcxx-ng:    8.2.0-hdf63c60_1        defaults
    ncurses:         6.1-he6710b0_1          defaults
    openssl:         1.1.1b-h7b6447c_0       defaults
    pip:             19.0.3-py37_0           defaults
    python:          3.7.2-h0371630_0        defaults
...
```

---
### *Activate* the environment...

...to start using it:

```bash
conda activate room101
```

(*may need to use `source` instead of `conda` on if using old conda*)

Your prompt now includes the name of your active environment:


```
(room101) $ which python
/home/will/miniconda3/envs/room101/bin/python
(room101) $ python --version
Python 3.7.2
```

---
### Installing and removing packages

  - Install a pkg inc. dependencies into the *curently active* environment:

    ```
    conda install PKGNAME
    ```

  - Or to remove:

    ```
    conda remove PKGNAME
    ```

  - Can confirm the effect using `conda list`.

---
### Compatability with pip

- pip: default tool for Python package installation
- pip installs packages from Python Package Index (by default)
- Many packages only available in PyPI
- Can install Python pkgs from PyPI into the active conda env!

  ```
  conda activate ENVNAME
  pip install PKGNAME
  ```

---
### Compatability with pip

- Recommendation: install conda pkg instead if one exists
    - conda pkg may include additional non-Python parts

---
### Less Python-centric environments

- Conda pkgs don't have to be Python pkgs  
- So we can use conda as a general purpose package manager! E.g.

```
(room101) $ conda create --name piratical_fun r-base r-yaml
...

(room101) $ conda activate piratical_fun
...

(piratical_fun) $ which R
/home/will/miniconda3/envs/piratical_fun/bin/R

(piratical_fun) $ R
...
R version 3.5.1 (2018-07-02) -- "Feather Spray"
...
> print("Arr!")
[1] "Arr!"
```

---
### Deactivating and deleting environments

Let's deactivate and delete this new conda env:

```
(piratical_fun) $ conda deactivate
$ conda env remove --name piratical_fun
```

NB as our conda env definition is contained within a dir we could just delete that instead:

```
$ rm -rf /home/will/miniconda3/envs/piratical_fun
```

---
### *Really* deleting conda packages

- Problem: miniconda dir can get *large* if install e.g. CUDA or Intel MKL packages
- Warning: **deleting an environment may not delete package files!**
- Reason: 
  - conda downloads pkgs into a **pkg cache** dir
  - then links to those from relevant env dir

  ```
  $ du -hsBM ~/miniconda3/* | sort -rn | head -n 2
  832M	/home/will/miniconda3/envs
  564M	/home/will/miniconda3/pkgs
  ```

---
- To remove pkgs from cache that are no longer referenced by an env:
   
  ```bash
  conda clean -pts
  ```

- Also helpful: tell conda to use env and/or pkg cache dirs somewhere else
    - `~/.condarc` file or
    - `CONDA_ENVS_PATH` + `CONDA_PKGS_DIRS` shell environment variables

---
### Auditing and reinstantiating environments

- What if you want to share your environment with a collaborator?
- Save the env state to a file:

  ```
  $ conda activate room101
  (room101) $ conda env export > environment.yml
  ```

- Then on this machine or elsewhere:

  ```
  $ conda env create --name room102S --file environment.yml
  ```

---
- These `environment.yml` files are written in the *YAML* markup language.

They contain:

 1. The installed conda packages (name, version, build) 
 2. The installed pip packages (name, version) 
 3. The used conda *channels* 

There are other ways to export env definitions but some don't provide 2 or 3.

---
> WARNING: the list of packages is not just those explicitly installed.
> It can include OS-specific dependency packages so (unedited) environment files
> may not be as portable as you'd hope.

---
You can also clone environments locally:

```bash
$ conda create --name room102 --clone room101
```

NB creating/cloning environments is cheap as conda links to existing package installs in your pkg cache where possible.

---
### Conda channels

  - Anaconda (the company) provide the `defaults` conda package *channel*
    - Includes `numpy` built against the Intel MKL
  - Other important channels:
    - `conda-forge`: community-driven packaging
    - Intel Python Distribution (`intel`): optimised builds of `numpy` 
  - Can explicitly install a pkg from a channel
    ```bash
    conda install -c conda-forge mynewpkg
    ```
  - Or set default search order
  - Be wary of risk of conflicts!

---
### Ok, so what exactly is a package?

- Each conda package is built from a recipe containing
  - build scripts and 
  - metadata.

NB Not going to cover exactly how to build a package here; see the conda (and conda-forge) docs for info.

---
### Workflow suggestions

  - Avoid Anaconda distribution
  - Avoid using `base` conda env
  - (Related) separate environment per project
  - Commit environment definition files to version control
    - exact definition for benchmarking/testing
    - 'curated' portable definition (remove OS-specific dependencies)
  - Use Python 3.x in all new envs if possible

---
### Summary

  - Conda is a Python-centric but also general-purpose package manager 
  - Can separate projects using environments... 
  - ...can capture the state of an environment... 
  - ...and can recreate environments defined by others. 
  - Create packages for Python and non-Python software, 
  - then share your build recipes and packages with others using conda-forge 

**Thanks for listening!**

---
### Docker / Singularity?

  - Pro: Py and non-py pkgs contained
  - Con: Still using multiple packages internally if want latest Py pkgs
  - Con: How to track / maintain what is compatible?
