# 03 - Anaconda Multi-Version Strategy  

In the local Windows AI development environment, Anaconda plays a key role in unified management of multiple Python environments. To balance the goals of "controllable main versions", "coexistence of multiple versions", "project reproducibility", and "isolated development", this chapter details the design ideas, specific structures, and recommended practices of Anaconda-based multi-version strategies, as a core part of the overall governance plan.  


https://aicity.blog.csdn.net/article/details/149291978?spm=1011.2415.3001.5331  


## I. Design Goals  

* **Unified Management**: Centrally install and manage multiple main Python versions through Anaconda to avoid system-level pollution.  
* **Multi-Version Coexistence**: Support multiple main versions from Python 3.9 to 3.13, with quick switching when needed.  
* **Reproducibility and Isolation**: Achieve project-level isolation with virtual environments to ensure environment reproducibility.  
* **Orderly and Controllable Paths**: All environment installation paths are clear, structurally unambiguous, and easy to migrate and back up.  


## II. Structural Design  

The recommended structure is as follows:  

```  
I:\DevTools\Anaconda\  
├── base\               # Anaconda's default base environment (use with caution)  
├── py39\               # Python 3.9 main version environment  
├── py310\              # Python 3.10 main version environment  
├── py311\              # Python 3.11 main version environment  
├── py312\              # Python 3.12 main version environment  
├── py313\              # Python 3.13 main version environment  
└── msys2\              # MSYS2 environment supporting C/C++/GTK builds  
```  

Each `py3x` directory is a main version environment explicitly created using `conda create -p` (e.g., `conda create -p I:/DevTools/Anaconda/py310 python=3.10`).  

> ✅ It is strongly recommended to **avoid developing or installing toolchains in the base environment** to prevent pollution.  


## III. Path Strategy  

### 1. Environment Paths  

Explicitly specify each main version path using the `-p` parameter:  

```bash  
conda create -p I:/DevTools/Anaconda/py311 python=3.11  
```  

Check environments with `conda env list`:  

```text  
# conda environments:  
#  
base                  *  I:/DevTools/Anaconda/base  
                      I:/DevTools/Anaconda/py39  
                      I:/DevTools/Anaconda/py310  
                      I:/DevTools/Anaconda/py311  
                      I:/DevTools/Anaconda/py312  
                      I:/DevTools/Anaconda/py313  
```  


### 2. Activation Methods  

Activate a main version environment:  

```bash  
conda activate I:/DevTools/Anaconda/py311  
```  

Or add a shortcut name for it:  

```bash  
conda env config vars set CONDA_ENV_NAME=py311  
```  


## IV. Version Isolation Principles  

* Each main version environment **only installs toolchain build tools (e.g., uv/poetry/hatch) and pip**.  
* Dependencies actually used in the project are all installed in the `.venv` local isolated environment (see next chapter).  
* Keep main version environments **clean, minimal, and build-only** to facilitate migration and cross-machine sharing.  

Example:  

```bash  
conda activate I:/DevTools/Anaconda/py312  
pip install uv poetry hatch  
```  


## V. MSYS2 and C/C++ Compilation Environment  

Some Python packages (e.g., `torch`, `pycocotools`, `fairseq`) require a C/C++ toolchain. It is recommended to install MSYS2 in the same directory:  

```  
I:/DevTools/Anaconda/msys2  
```  

Configure the following system-level environment variables:  

```bash  
setx PATH "%PATH%;I:\DevTools\Anaconda\msys2\mingw64\bin"  
```  

MSYS2 installation commands (using pacman):  

```bash  
pacman -Syu  
pacman -S mingw-w64-x86_64-toolchain  
pacman -S mingw-w64-x86_64-gtk3  
```  


## VI. Version Update Strategy  

* Frequent updates to Python main versions are not recommended. Create a new `py3x` environment when a new main version is needed.  
* Freeze old version environments to ensure historical projects can be built normally.  
* Regularly upgrade the Conda toolchain and pip:  

```bash  
conda update conda  
pip install --upgrade pip  
```  


## VII. Environment Migration and Backup Recommendations  

* All environments are primarily identified by their paths; migrate by directly copying the `Anaconda/` directory.  
* It is recommended to back up snapshots of `conda-meta/` and `site-packages/` to support full reproducibility.  
* Export and rebuild environments using `conda list --explicit > env.txt`:  

```bash  
conda create -p I:/DevTools/Anaconda/py311 --file env.txt  
```  


## VIII. Summary  

Anaconda's multi-version management strategy not only solves traditional issues of `base` environment pollution and version conflicts but also provides a solid foundation for version reproducibility and isolated development of AI projects. In the subsequent chapter on toolchain isolation, we will further introduce how to achieve final project-level environment encapsulation through `.venv`.
