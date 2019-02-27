<div class="center">
    <img src="images/conda_logo.svg" alt="Conda logo" />
    <h3>Yet another package manager!</h3>
</div>

<div class="right">
    <img src="images/rse-logoonly-stroke.png" width="35%" style="padding: 0 15px; float: right;"/>
</div>

Will Furnass

Research Software Engineering team, University of Sheffield

2019-02-27

---




### Problem

  - Software infrequently exists in a vacuum













---
### Assumptions

Assumption: you want to work on / use software that has both:

  - Python bits
  - Non-Python bits

e.g.
---

  - use a Py pkg that depends on `libxml`;
  - use a Py pkg that depends on linear algebra C lib;
  - Mu, the BBC micro:bit IDE: depends on some Qt libraries for the GUI and serial comms.

---
### Requirements

When working on a Py + non-Py project we want:

  - To be able to use Py and non-Py pkgs and pkg versions you want, regardless of OS pkgs; 
  - Separation / independence from system packages to ensure/test portability; 
  - To work as an unprivileged user. 
  - For us and other users not to be tied to a given OS/environment. 

---
### Install everything as root using OS package manager?

  - Can only have one version of each pkg for all projects (no sandboxing)
  - Limited to older versions of pkgs
  - Limited selection of pkgs
  - May not be easy/possible to switch to Py3 (e.g. on Centos 7)

---
### Install Py stuff as root using pip?

  - Pros:
      - newer Py pkgs
  - Cons:
      - Now using two pkg managers
           - Conflicts! 
           - May break your OS as many \*nix OS utilities written in Py 
           - How to keep Py and non-Py deps in sync? 
      - No sandboxing 
      - Can only have one version of each package for all projects 
      - Slow to install if no wheel available and may need a compiler (urgh) 

---
### Install Py pkgs in virtualenv?

  - Pro: Newer Py pkgs  
  - Pro: Safer as no longer root  
  - Pro: Sandboxing of python pkgs - can have different vers per venv  
  - Con: Still using two pkg managers (OS for non-Py stuff) 
      - Pro: No conflicts and won't break your OS *but*
      - Con: How to keep Py and non-Py deps in sync? 
  - Con: Slow to install if no wheel available and may need a compiler (urgh) 

---
### Docker / Singularity?

  - Pro: Py and non-py pkgs contained
  - Con: Still using multiple packages internally if want latest Py pkgs
  - Con: How to track / maintain what is compatible?

---
### Another approach: Conda

Yet another package manager (groan) *but*:

  - A conda pkg can be pure Py (e.g. PyPI pkg with little extra md) 
  - *or* can be non-Py! 
  - Provides *environments* (like virtualenvs) 
      - Keep system and dev pkgs separate (even compiled libraries) 
      - Can do *everything* as unprivileged user 
      - Different pkgs and pkg versions per env 

---
  - Binary-only packages
      - Built for Windows (32b, 64b), macOS, Linux (32b, 64b) and 
      - for maj+min Py vers (+ maybe key dependency vers)
          - e.g. Binary pkg for Linux 64bit + Py 3.6 + `numpy` 1.12 
  - No reason not to use in Docker!

---
### Installing (Mini)conda

  - Download **Miniconda** 3 installer from [https://repo.continuum.io/miniconda/](https://repo.continuum.io/miniconda/)
  - Run installer (using e.g. `bash /Miniconda3-latest-Linux-x86_64.sh ` on macOS / Linux)
  - Install into default location (e.g. `~/miniconda3`) 
  - Suggestion: do not make Miniconda Python the default Python
      - **macOS / Linux** don't prepend the install dir to your `PATH` 
      - **Windows**: don't add Miniconda Python to registry 

---
### Making conda available

  - Instead:
      - **Windows**: Start a Miniconda shell from the *Start* menu. 
      - **Linux / macOS**: `source` the `conda.sh` script (adds conda dir to your `$PATH`):

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

 - Python pkgs (e.g. `requests`) and 
 - non-Python (inc. compiled) pkgs (e.g. `zlib`)!

---
### Creating a new environment

Best to create a new environment (distinct from the 'base') per project.
Need a name and some pkgs (optionally with version nums and build nums):

```console
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

Install a pkg inc. dependencies into the *curently active* environment:

```
conda install PKGNAME
```

Or to remove:

```
conda remove PKGNAME
```

Can confirm the effect using `conda list`.

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
  $ conda env create --name room102
  ```

---
- These `environment.yml` files are written in the *YAML* markup language.

They contain:

  - The installed conda packages (name, version, build) 
  - The installed pip packages (name, version) 
  - The used conda *channels* 

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
### Ok, so what exactly is a package?

Each conda package is a tarball built from a *recipe*:

  - Build scripts for Windows and for macOS / Linux, 
    which typically contain little more than 

    ```bash
    $PYTHON setup.py install
    ```
  - A `meta.yml` file containing package metadata

Given these, `conda build` can compile/bundle packages and optionally upload them to a repository.

NB Not going to cover exactly how to build a package here; see the conda and conda-forge docs for info.

---
### What is Conda-Forge?

  - *Community-driven packaging* for Conda
  - Provides a separate *channel* to the default conda repository
  - Packages built using public Continuous Integration (CI) infrastructure
  - Propose a new recipe via 
    the [conda-forge/staged-recipes](https://github.com/conda-forge/staged-recipes)

---
  - If CI confirms that your pull request can build (on multiple OSs) then...
  - ...a dedicated *feedstock* repo is created for your recipe... 
  - and post-build your packages will be available from the conda-forge channel using e.g. 

    ```bash
    conda install -c conda-forge mynewpkg
    ```
---
### Summary

  - Conda is a Python-centric but also general-purpose package manager 
  - Can separate projects using environments... 
  - ...can capture the state of an environment... 
  - ...and can recreate environments defined by others. 
  - Create packages for Python and non-Python software, 
  - then share your build recipes and packages with others using conda-forge 

**Thanks for listening!**
