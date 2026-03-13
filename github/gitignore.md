# Repo .gitignore
```
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg

# Virtual Environments
venv/
env/
ENV/
.venv/
.env/
env.bak/
venv.bak/
.python-version
.conda/
Pipfile.lock
poetry.lock

# Jupyter Notebooks
.ipynb_checkpoints
*/.ipynb_checkpoints/*
profile_default/
ipython_config.py

# Data Files
*.csv
*.dat
*.db
*.sqlite
*.sqlite3
*.h5
*.hdf5
*.pkl
*.pickle
*.json
*.feather
*.parquet
*.sav
data/
datasets/
raw/
processed/
interim/
external/

# Model files
*.model
*.joblib
*.pt
*.pth
*.onnx
*.h5
models/
checkpoints/
trained_models/

# Logs
logs/
*.log
tensorboard/
runs/
mlruns/
wandb/
lightning_logs/

# MacOS
.DS_Store
.AppleDouble
.LSOverride
Icon
._*

# Windows
Thumbs.db
ehthumbs.db
Desktop.ini
$RECYCLE.BIN/

# Linux
*~
.fuse_hidden*
.directory
.Trash-*
.nfs*

# VSCode
.vscode/*
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json
.history

# JetBrains IDEs
.idea/
*.iml
*.iws
*.ipr
*.iws
.idea_modules/

# Other common files
.coverage
htmlcov/
.pytest_cache/
.tox/
.coverage.*
*.cover
.hypothesis/
.mypy_cache/
.pyre/
.ruff_cache/
.dmypy.json
dmypy.json
.coverage
htmlcov/
*.sh
```

# Global .gitignore
```
echo ".DS_Store

.DS_Store?

._*

.Spotlight-V100

.Trashes

ehthumbs.db

Thumbs.db" > ~/.gitignore_global


git config --global core.excludesfile ~/.gitignore_global
```