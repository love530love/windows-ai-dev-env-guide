# 02 - Path Structure Specifications: Governance of Unified, Controllable, and Migratable Development Paths  


> This chapter focuses on how to build a path structure system with **uniformity**, **controllability**, and **migratability** in a local Windows development environment, serving as the foundational framework for the entire AI development environment.  


## I. Why Is Path Structure Critical?  

On Linux, path structures typically have inherent consistency and hierarchical logic. However, Windows systems face issues such as multiple drives, multi-user directories, and unstable mounting paths (e.g., WSL, containers, temporary paths). Path chaos directly leads to:  

- Failure to reproduce virtual environments or dependencies;  
- Frequent changes in container or subsystem mounting paths;  
- Inoperable environments after project migration;  
- Difficulty in standardizing teaching deployments.  

> ✅ **A unified path structure is a prerequisite for achieving migratable, reconstructible, and automatically deployable development environments.**  


## II. Four Goals of Unified Paths  

| Goal | Description |  
|------|-------------|  
| **Unified disk mounting location** | All environment installations, code projects, WSL mappings, container volumes, etc., are archived in a preset main drive (e.g., `I:\`) |  
| **Clear path hierarchy** | The path structure should reflect functional ownership: tools/environments/projects/models/data are categorized independently |  
| **Backups and migration support** | Paths should avoid binding to user directories, system drives, or dynamically mounted paths |  
| **Consistency across tools/languages** | Whether for Python, Node.js, CUDA, Docker, etc., all follow a unified path plan |  


## III. Recommended Path Structure (Example)  

The following path structure is recommended for **teaching/experimentation/individual developers**:  

```  
I:  
├── Tools\               # General development tools (e.g., Anaconda, Git, VSCode)  
├── Envs\                # Root directory for multi-version Python environments (not Conda's default path)  
│   ├── py39  
│   ├── py310  
│   └── py312  
├── WSL\                 # Migration directory for all WSL subsystems  
│   ├── Ubuntu  
│   └── podman-machine-default  
├── DockerVolumes\       # Docker persistent volume mapping paths (e.g., models, databases)  
├── Projects\            # Unified location for AI project code repositories  
│   ├── nlp-llm-demo  
│   ├── stable-diffusion  
│   └── voice-cloning  
├── Models\              # Local model cache directory  
├── Datasets\            # Dataset storage  
└── Templates\           # Templates for teaching/experimental projects  
```  

> 📌 **Practical Reference**:  
> The design philosophy of this path structure is detailed in the author’s CSDN series:  
> - [《Path Governance: Building Controllable, Migratable, and Reproducible AI Development Paths》](https://aicity.blog.csdn.net/article/details/149172789)  


## IV. Key Considerations in Path Governance  

### 1. Avoid using the system drive (`C:\`)  
System drive paths like `C:\Users\xxx` frequently cause issues during system reinstalls, permission restrictions, and WSL/Docker mappings.  


### 2. Do not rely on user directories or usernames  
Path structures should not include user account names. For example:  
```bash  
❌ D:\Users\love\Documents\MyCode  
✅ I:\Projects\MyCode  
```  


### 3. Place all environments in controllable directories  

- Anaconda installation path: `I:\Tools\Anaconda3`  
- WSL migration path: `I:\WSL\xxx`  
- Docker Desktop volume configuration: `I:\DockerVolumes\`  


### 4. Use `.env` / `.venv` / `.condarc` for path isolation  

- Project-specific `.env` files can preset key path variables  
- `.venv` should be bound to the project or a unified environment pool to avoid system-level interference  
- `.condarc` can redirect the default environment directory to `I:\Envs\`  


## V. Migration and Reproducibility Strategies for Path Governance  

Once the path structure is stable, **one-click migration** can be achieved through:  

- Compress and package the entire `I:\` drive (or synchronize via tools like rsync/robocopy)  
- Quickly restore on new devices using `.env`, `.condarc`, `.wslconfig`, etc.  
- Bind container data to Docker volumes with fixed paths: `I:\DockerVolumes\`  


## VI. Summary  

The path structure is the "foundation" of development environment governance. This scheme ensures the development environment through the following key features:  

- **Uniformity**: All paths are centralized with clear responsibilities;  
- **Controllability**: Customizable mounting drives and paths;  
- **Migratability**: Environments/projects/configurations can be copied to other devices for reconstruction;  
- **Teachability**: Template-based path structures facilitate training deployment and teaching sharing.  


---

> 📚 Recommended Reading (CSDN Original):  
>
> * [Building an End-to-End AI Development System with Windows + WSL + Docker + Anaconda + PyCharm](https://aicity.blog.csdn.net/article/details/148997254)  
> * [Path Governance: Building Controllable, Migratable, and Reproducible AI Development Paths](https://aicity.blog.csdn.net/article/details/149172789)
