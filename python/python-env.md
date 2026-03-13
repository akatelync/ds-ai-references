# Simple Guide to Using UV with venv and pyproject.toml

UV is a fast Python package installer and resolver written in Rust. Here's how to use it with virtual environments and pyproject.toml:

## Step 1: Install UV

```bash
pip install uv
```

## Step 2: Set Up Project and Create Virtual Environment

```bash
# Create project directory
mkdir myproject
cd myproject

# Create and activate virtual environment
uv venv
source .venv/bin/activate  # On macOS/Linux
```

## Step 3: Create a Basic pyproject.toml Template

```bash
cat > pyproject.toml << EOF
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "myproject"
version = "0.1.0"
description = "My Python project"
readme = "README.md"
requires-python = ">=3.8"
authors = [
    {name = "Your Name", email = "your.email@example.com"},
]
dependencies = [
]

[project.optional-dependencies]
dev = [
]
EOF
```

## Step 4: Install Packages and Auto-Update pyproject.toml

Now you can install packages as needed, and UV will automatically update your pyproject.toml:

```bash
# Install packages with the --add flag to automatically update pyproject.toml
uv pip install pandas numpy matplotlib --add
```

The `--add` flag adds the package to the `dependencies` section in pyproject.toml, while `--add-dev` adds it to the `dev` optional dependencies.

## Step 5: Create a Simple Script to Automate This Workflow

Save this as `setup-project.sh`:

```bash
#!/bin/bash

# Get project name from directory or argument
PROJECT_NAME=${1:-$(basename $(pwd))}
PYTHON_VERSION=">=3.8"
AUTHOR_NAME="Your Name"
AUTHOR_EMAIL="your.email@example.com"

# Create and activate virtual environment
uv venv
source .venv/bin/activate

# Create basic pyproject.toml
cat > pyproject.toml << EOF
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "$PROJECT_NAME"
version = "0.1.0"
description = "My Python project"
readme = "README.md"
requires-python = "$PYTHON_VERSION"
authors = [
    {name = "$AUTHOR_NAME", email = "$AUTHOR_EMAIL"},
]
dependencies = [
]

[project.optional-dependencies]
dev = [
]
EOF

echo "Project setup complete! Virtual environment created and activated."
echo "To install packages and auto-update pyproject.toml, use:"
echo "uv pip install package-name --add"
echo "uv pip install dev-package --add-dev"
```

Make it executable and use it:

```bash
chmod +x setup-project.sh
./setup-project.sh my-awesome-project
```

## Example Workflow

```bash
# Create a new project
mkdir data-analysis-project
cd data-analysis-project

# Set up project with venv and pyproject.toml
uv venv
source .venv/bin/activate
cat > pyproject.toml << EOF
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "data-analysis-project"
version = "0.1.0"
description = "Data analysis project"
readme = "README.md"
requires-python = ">=3.8"
authors = [
    {name = "Your Name", email = "your.email@example.com"},
]
dependencies = [
]

[project.optional-dependencies]
dev = [
]
EOF

# Install packages and auto-update pyproject.toml
uv pip install pandas numpy matplotlib scikit-learn --add
uv pip install pytest black jupyterlab --add-dev

# Continue working on your project
python -m jupyterlab
```

This approach lets you set up your environment interactively while automatically maintaining your pyproject.toml file. UV takes care of the dependency management so you can focus on your code!