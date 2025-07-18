# 05 - Template Design and Migration Reproduction: One-Click Standardized Project Skeleton and Environment Migration  

> References for this chapter:  
> - [Teaching Sharing: Building an AI Experimental Teaching Environment Based on Windows Local Architecture](https://aicity.blog.csdn.net/article/details/148874083)  
> - [AI End-to-End Development System: Project Templates and Teaching Deployment](https://aicity.blog.csdn.net/article/details/149284845)  


## I. Why Need a Unified Template?  

- **Lower the entry barrier**: New developers don’t need to reconfigure the environment repeatedly; they can start development immediately after pulling the template.  
- **Standardize project structure**: Unify storage paths for code, data, and models to facilitate team collaboration and maintenance.  
- **Ensure environment consistency**: Built-in dependency lock files and build scripts avoid the "it works on my machine" issue.  
- **Accelerate teaching deployment**: Templates can be quickly distributed in teaching scenarios, focusing on core knowledge rather than environment configuration.  


## II. Core Structure of the Project Template  

```tree
MyAiTemplate/
├── .venv/                         # Optional: Pre-installed build tools (poetry/uv) and basic dependencies  
├── src/                           # Source code directory (core business logic)  
│   └── main.py                    # Example program entry  
├── data/                          # Dataset placeholder directory (with documentation)  
│   └── README.md                  # Explain data sources, formats, and acquisition methods  
├── models/                        # Model file directory (pre-trained models/checkpoints)  
│   └── README.md                  # Explain model download paths and loading methods  
├── scripts/                       # Automation scripts (training/evaluation/service startup)  
│   ├── train.ps1                  # Windows training startup script  
│   └── serve.ps1                  # Model service startup script  
├── Dockerfile                     # Containerization build definition (optional, supports cross-platform deployment)  
├── environment.yml                # Conda environment definition (version-locked)  
├── requirements.txt               # pip dependency lock file (exact versions)  
├── .gitignore                     # Ignore temporary files, virtual environments, and sensitive information  
├── README.md                      # Project description (environment configuration + running steps + directory explanation)  
└── LICENSE                        # Open-source license agreement (optional)  
```  


## III. Template Generation and Usage Process  

### 3.1 Clone the Template Repository  
```bash
# Clone the official template to a local project  
git clone https://github.com/yourusername/ai-project-template.git MyAiProject  
cd MyAiProject  
```  


### 3.2 Initialize the Virtual Environment (if `.venv` is not built into the template)  
```powershell
# Create a project-specific virtual environment  
python -m venv .venv  

# Activate the environment  
.\.venv\Scripts\activate  

# Install dependencies (based on lock files)  
pip install --upgrade pip  
pip install -r requirements.txt  
```  


### 3.3 Import Conda Environment (optional, suitable for multi-version management)  
```bash
# Create a Conda environment based on environment.yml  
conda env create -f environment.yml  

# Activate the environment  
conda activate myaienv  
```  


### 3.4 One-Click Run Project Scripts  
Quickly start tasks through predefined scripts to avoid manually entering complex commands:  
```powershell
# Start model training  
.\scripts\train.ps1  

# Start model service  
.\scripts\serve.ps1  
```  


## IV. Environment Migration and Reproduction Solutions  

### 4.1 Export Current Environment Configuration (for updating templates)  
#### Export Conda Environment  
```bash
# Activate the target environment  
conda activate myaienv  

# Export environment definition (including version information)  
conda env export > environment.yml  
```  

#### Export pip Dependencies  
```powershell
# Activate the .venv environment  
.\.venv\Scripts\activate  

# Generate dependency lock file  
pip freeze > requirements.txt  
```  


### 4.2 Migrate to a New Machine/New Environment  
#### Method 1: Migration Based on `.venv` (recommended for the same platform)  
1. Copy the entire project directory (including `.venv` and `requirements.txt`) to the new machine;  
2. Activate the environment and fix dependencies:  
   ```powershell
   cd MyAiProject  
   .\.venv\Scripts\activate  
   pip install --upgrade pip  # Fix path dependencies  
   ```  

#### Method 2: Rebuild Based on Dependency Files (recommended for cross-platform)  
1. Copy the project directory (excluding `.venv`, retaining only `environment.yml` and `requirements.txt`);  
2. Rebuild the environment:  
   ```powershell
   # Rebuild based on Conda  
   conda env create -f environment.yml  
   conda activate myaienv  

   # Or rebuild based on pip  
   python -m venv .venv  
   .\.venv\Scripts\activate  
   pip install -r requirements.txt  
   ```  


## V. Distribution Strategies for Teaching Scenarios  

### 5.1 Offline Template Package Production  
```powershell
# Compress the template directory (including offline dependencies)  
Compress-Archive -Path MyAiTemplate\* -DestinationPath ai_template.zip  

# Package dependency packages separately (supports offline environments)  
pip download -r requirements.txt -d offline_pkgs  
Compress-Archive -Path offline_pkgs\* -DestinationPath offline_pkgs.zip  
```  


### 5.2 One-Click Deployment Script (`deploy.ps1`)  
```powershell
# Unzip templates and dependencies  
Expand-Archive -Path ai_template.zip -DestinationPath MyAiProject  
Expand-Archive -Path offline_pkgs.zip -DestinationPath MyAiProject\offline_pkgs  

# Enter the project directory and initialize the environment  
cd MyAiProject  
python -m venv .venv  
.\.venv\Scripts\activate  
pip install --no-index --find-links offline_pkgs -r requirements.txt  

Write-Host "✅ Environment deployment completed! Run .\scripts\train.ps1 to start training"  
```  


### 5.3 Documentation Guidance Optimization  
Add a "Zero-Threshold Startup Guide" at the top of `README.md`:  
```markdown
# Quick Startup Steps  
1. Unzip `ai_template.zip` to a local directory  
2. Double-click to run `deploy.ps1` to automatically configure the environment  
3. Open PowerShell and execute:  
   ```powershell  
   cd MyAiProject  
   .\scripts\train.ps1  
   ```  


## VI. Summary  

Unified templates solve issues in Windows AI development such as "cumbersome environment configuration, chaotic project structure, and difficult migration/reproduction" through three core elements: "standardized structure + automated scripts + version locking". Whether for personal development, team collaboration, or teaching scenarios, templates enable an efficient development experience of "pull and develop, migrate and reproduce".  

Through the combination of templates, offline packages, and one-click scripts, stable AI development environments can be quickly built even in offline environments or on beginners' devices, allowing energy to focus on core business logic rather than environment debugging.
