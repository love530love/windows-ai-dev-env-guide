# 06 - Teaching Deployment and Experimental Environment: One-Click Distribution and Multi-Machine Reproduction  

> References for this chapter:  
> - [Teaching Sharing: Building an AI Experimental Teaching Environment Based on Windows Local Architecture](https://aicity.blog.csdn.net/article/details/148874083)  
> - [AI End-to-End Development System: Project Templates and Teaching Deployment](https://aicity.blog.csdn.net/article/details/149284845)  


## I. Analysis of Teaching Environment Requirements  

1. **Zero-threshold access**  
   New students do not need to manually configure the toolchain; environment setup can be completed through templates and scripts, focusing on learning content rather than environment debugging.  

2. **Rapid batch deployment**  
   Supports simultaneous distribution to multiple devices. Teachers do not need to configure each device individually, ensuring consistent environments across all devices through a unified template.  

3. **Migratable and reproducible**  
   The environment can be fully packaged, distributed, and restored, solving the teaching pain point of "students' machines failing to run the environment".  

4. **Offline support**  
   Built-in offline dependency packages, adapting to network-free computer rooms or campus environments, ensuring deployment is not restricted by the network.  


## II. Structure of the Teaching Template  

```tree
TeachingEnvTemplate/
├── .venv/                           # Pre-built virtual environment (including build tools and runtime dependencies)
├── offline_packages/                # Offline dependency package cache (supports network-free installation)
├── scripts/                         # Collection of automated scripts
│   ├── deploy.ps1                   # One-click deployment: unzip, link, activate environment
│   ├── update-env.ps1               # Environment update: replace dependencies, reinstall
│   └── clean-env.ps1                # Environment cleaning: delete .venv, clear cache
├── docs/                            # Teaching documents
│   └── student-guide.md             # Student operation manual (with step-by-step diagrams)
├── environment.yml                  # Conda environment definition (optional, multi-version compatible)
├── requirements.txt                 # pip dependency lock file (exact versions)
└── README.md                        # Template description and teacher deployment guide
```  


## III. Example of One-Click Deployment Script  

```powershell
# scripts/deploy.ps1
param(
  [string]$TargetDir = (Get-Location)  # Deployment target path, default to current directory
)

Write-Host "`nStarting deployment of the teaching environment to: $TargetDir`n"

# 1. Unzip the template package (assuming the template is TeachingEnvTemplate.zip)
if (Test-Path ".\TeachingEnvTemplate.zip") {
  Expand-Archive -Path ".\TeachingEnvTemplate.zip" -DestinationPath $TargetDir -Force
  Write-Host "✅ Template unzipped successfully"
} else {
  Write-Error "❌ Template file TeachingEnvTemplate.zip not found. Please check the path."
  exit 1
}

# 2. Create a symbolic link (map common paths for easy student access)
$linkPath = "$env:USERPROFILE\AI_Teaching_Env"
if (-not (Test-Path $linkPath)) {
  New-Item -ItemType SymbolicLink -Path $linkPath -Target "$TargetDir\TeachingEnvTemplate" | Out-Null
  Write-Host "✅ Shortcut created: $linkPath"
}

# 3. Activate and verify the .venv environment
$venvActivatePath = "$TargetDir\TeachingEnvTemplate\.venv\Scripts\activate.ps1"
if (Test-Path $venvActivatePath) {
  & $venvActivatePath
  Write-Host "`n✅ Virtual environment activated successfully"
  Write-Host "📌 Python version: $(python --version 2>&1)"
} else {
  Write-Error "❌ Virtual environment activation script not found. Please check if the .venv directory is complete."
  exit 1
}

Write-Host "`n🎉 Environment deployment completed! Please refer to student-guide.md to start the experiment`n"
```  


## IV. Offline Dependency Management  

### 4.1 Pre-package offline dependencies  
```powershell
# Execute on the teacher's machine: download all dependency packages based on requirements.txt
cd TeachingEnvTemplate
pip download -r requirements.txt -d offline_packages/
Write-Host "✅ Offline dependency packages saved to offline_packages/"
```  


### 4.2 Offline environment update  
Update students' environments with one click via scripts, without redistributing the complete template:  
```powershell
# Execute on the student's machine: update dependencies (using local offline packages)
.\scripts\update-env.ps1
```  

Core logic of `update-env.ps1`:  
```powershell
# Clean up old dependency cache
Remove-Item -Path ".\offline_packages\*" -Recurse -Force -ErrorAction SilentlyContinue

# Copy new offline packages (assuming the teacher provides the updated_offline_packages directory)
Copy-Item -Path ".\updated_offline_packages\*" -Destination ".\offline_packages\" -Recurse -Force

# Activate the environment and reinstall dependencies
.\.venv\Scripts\activate.ps1
pip install --no-index --find-links ./offline_packages -r requirements.txt
Write-Host "✅ Environment updated successfully"
```  


## V. Full Process of Teaching Usage  

### 5.1 Teacher Preparation Phase  
1. Build the base template (including `.venv`, offline packages, and scripts);  
2. Compress it into `TeachingEnvTemplate.zip` and attach `student-guide.md`;  
3. Distribute to students (via USB drive, local area network sharing, or online download).  


### 5.2 Student Operation Steps  
1. Unzip `TeachingEnvTemplate.zip` to a local directory;  
2. Double-click `scripts\deploy.ps1` to run the deployment script (allow PowerShell execution for the first time);  
3. Open the generated shortcut `AI_Teaching_Env`;  
4. Follow the steps in `student-guide.md` to start the experiment (e.g., run `src/main.py` or training scripts).  


### 5.3 Environment Maintenance and Updates  
- After teachers update dependencies, only distribute the `offline_packages` directory and `update-env.ps1`;  
- Students execute `.\scripts\update-env.ps1` to synchronize the latest environment.  


## VI. Common Issues and Solutions  

| Issue | Solution |  
|-------|----------|  
| **Scripts are blocked from execution** | Run PowerShell as administrator on the student's machine and execute: `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` (select Y to confirm) |  
| **Missing dependencies in offline packages** | Teachers re-run `pip download -r requirements.txt -d offline_packages/` on their machines to generate complete packages |  
| **Virtual environment activation fails** | Check if the `.venv` directory is complete, or re-create it: `python -m venv .venv` |  


## VII. Summary  

Through the combined strategy of "template standardization + script automation + offline localization", this solution achieves **zero-threshold distribution, consistent multi-machine reproduction, and stable offline operation** of the teaching environment. Teachers no longer need to debug each machine, and students do not need manual configuration, significantly reducing the environmental barriers in AI experimental teaching, allowing energy to focus on core knowledge transmission and experimental practice.  

This model is not only applicable to classroom teaching but also extensible to campus laboratories, enterprise training, and other scenarios, providing a standardized environment governance solution for AI teaching on the Windows platform.
