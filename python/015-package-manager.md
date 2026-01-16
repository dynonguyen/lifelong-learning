# Package Manager

## pip - Package Installer for Python

### Basic pip Commands

```bash
# Install package
pip install requests

# Install specific version
pip install requests==2.28.0

# Install minimum version
pip install requests>=2.28.0

# Install version range
pip install "requests>=2.28.0,<3.0.0"

# Install from requirements.txt
pip install -r requirements.txt

# Upgrade package
pip install --upgrade requests
pip install -U requests  # Short form

# Uninstall package
pip uninstall requests

# Show package info
pip show requests

# List installed packages
pip list

# List outdated packages
pip list --outdated

# Search for packages
pip search requests  # Deprecated on PyPI
```

### requirements.txt

```bash
# Generate requirements.txt
pip freeze > requirements.txt

# Install from requirements.txt
pip install -r requirements.txt
```

```txt
# requirements.txt

# Exact versions (for reproducibility)
requests==2.28.1
numpy==1.24.2
pandas==2.0.0

# Minimum versions
flask>=2.0.0

# Version ranges
django>=3.2.0,<4.0.0

# Comments
# Development dependencies
pytest==7.3.0
black==23.3.0

# Install from git
git+https://github.com/user/repo.git@main#egg=package-name

# Install from local path
-e ./local-package

# Include other requirements files
-r requirements-dev.txt
```

### Installing from Different Sources

```bash
# From PyPI (default)
pip install requests

# From git repository
pip install git+https://github.com/psf/requests.git

# From specific branch/tag/commit
pip install git+https://github.com/psf/requests.git@main
pip install git+https://github.com/psf/requests.git@v2.28.0

# From local directory
pip install ./path/to/package

# In editable/development mode
pip install -e ./path/to/package

# From wheel file
pip install package-1.0.0-py3-none-any.whl

# From source distribution
pip install package-1.0.0.tar.gz

# From URL
pip install https://files.pythonhosted.org/packages/.../package.whl
```

### pip Configuration

```ini
# pip.conf (Linux/Mac: ~/.pip/pip.conf)
# pip.ini (Windows: %APPDATA%\pip\pip.ini)

[global]
timeout = 60
index-url = https://pypi.org/simple
trusted-host = pypi.org

[install]
use-feature = fast-deps
```

```bash
# Set config via command line
pip config set global.timeout 60
pip config set install.user true

# Show config
pip config list

# Get specific value
pip config get global.timeout
```

## Virtual Environments (venv)

### Creating and Using venv

```bash
# Create virtual environment
python -m venv myenv

# With specific Python version
python3.11 -m venv myenv

# Activate virtual environment
source myenv/bin/activate     # Linux/Mac
myenv\Scripts\activate        # Windows
myenv\Scripts\activate.bat    # Windows (cmd)
myenv\Scripts\Activate.ps1    # Windows (PowerShell)

# Check active environment
which python    # Linux/Mac
where python    # Windows

# Deactivate
deactivate

# Remove virtual environment
rm -rf myenv    # Linux/Mac
rmdir /s myenv  # Windows
```

### venv with specific options

```bash
# Create venv without pip
python -m venv myenv --without-pip

# Create venv with system packages
python -m venv myenv --system-site-packages

# Upgrade venv's pip
python -m venv myenv --upgrade

# Clear existing venv
python -m venv myenv --clear
```

## virtualenv (Third-party Alternative)

```bash
# Install virtualenv
pip install virtualenv

# Create virtual environment
virtualenv myenv

# With specific Python version
virtualenv -p python3.11 myenv
virtualenv --python=python3.11 myenv

# Activate and use (same as venv)
source myenv/bin/activate
```

## Poetry - Dependency Management

### Installing Poetry

```bash
# Install via official installer
curl -sSL https://install.python-poetry.org | python3 -

# Or via pip (not recommended)
pip install poetry

# Verify installation
poetry --version
```

### Creating a New Project

```bash
# Create new project
poetry new myproject

# Project structure:
# myproject/
#     pyproject.toml
#     README.md
#     myproject/
#         __init__.py
#     tests/
#         __init__.py

# Initialize in existing project
cd myproject
poetry init
```

### pyproject.toml

```toml
[tool.poetry]
name = "myproject"
version = "0.1.0"
description = "A sample Python project"
authors = ["Alice <alice@example.com>"]
readme = "README.md"
license = "MIT"

[tool.poetry.dependencies]
python = "^3.8"
requests = "^2.28.0"
numpy = "^1.24.0"

[tool.poetry.group.dev.dependencies]
pytest = "^7.3.0"
black = "^23.3.0"
mypy = "^1.3.0"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"

[tool.poetry.scripts]
mycommand = "myproject.cli:main"
```

### Poetry Commands

```bash
# Install dependencies
poetry install

# Add package
poetry add requests

# Add dev dependency
poetry add --group dev pytest

# Remove package
poetry remove requests

# Update dependencies
poetry update

# Update specific package
poetry update requests

# Show installed packages
poetry show

# Show dependency tree
poetry show --tree

# Run command in virtual environment
poetry run python script.py
poetry run pytest

# Activate shell
poetry shell

# Build package
poetry build

# Publish to PyPI
poetry publish

# Export requirements.txt
poetry export -f requirements.txt --output requirements.txt
```

### Poetry Version Constraints

```toml
[tool.poetry.dependencies]
# Caret requirements (compatible versions)
requests = "^2.28.0"  # >=2.28.0, <3.0.0

# Tilde requirements (patch-level changes)
requests = "~2.28.0"  # >=2.28.0, <2.29.0

# Exact version
requests = "2.28.0"

# Version range
requests = ">=2.28.0, <3.0.0"

# Multiple constraints
requests = {version = "^2.28.0", python = "^3.8"}

# Git dependency
requests = {git = "https://github.com/psf/requests.git", branch = "main"}

# Path dependency
mypackage = {path = "../mypackage", develop = true}

# Optional dependency
pytest = {version = "^7.0", optional = true}
```

## Pipenv - Python Dev Workflow

```bash
# Install pipenv
pip install pipenv

# Create virtual environment and install packages
pipenv install

# Install specific package
pipenv install requests

# Install dev dependency
pipenv install --dev pytest

# Activate shell
pipenv shell

# Run command
pipenv run python script.py

# Check dependencies
pipenv check

# Show dependency graph
pipenv graph

# Lock dependencies
pipenv lock

# Uninstall package
pipenv uninstall requests

# Remove virtual environment
pipenv --rm
```

### Pipfile

```toml
[[source]]
url = "https://pypi.org/simple"
verify_ssl = true
name = "pypi"

[packages]
requests = "*"
numpy = ">=1.24.0"

[dev-packages]
pytest = "*"
black = "*"

[requires]
python_version = "3.11"

[scripts]
test = "pytest tests/"
format = "black ."
```

## Conda - Package and Environment Management

### Installing Conda

```bash
# Download Miniconda or Anaconda
# From https://docs.conda.io/en/latest/miniconda.html

# Verify installation
conda --version
```

### Conda Commands

```bash
# Create environment
conda create --name myenv python=3.11

# Activate environment
conda activate myenv

# Deactivate environment
conda deactivate

# List environments
conda env list

# Remove environment
conda remove --name myenv --all

# Install package
conda install numpy

# Install specific version
conda install numpy=1.24.0

# Install from specific channel
conda install -c conda-forge requests

# Install with pip in conda environment
conda install pip
pip install package-name

# Update package
conda update numpy

# Update conda itself
conda update conda

# List installed packages
conda list

# Search for package
conda search numpy

# Export environment
conda env export > environment.yml

# Create from environment.yml
conda env create -f environment.yml

# Clone environment
conda create --name newenv --clone myenv
```

### environment.yml

```yaml
name: myproject
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.11
  - numpy=1.24.0
  - pandas>=2.0.0
  - pip
  - pip:
      - requests==2.28.0
      - custom-package
```

## Comparing Package Managers

```python
# pip + venv (Standard)
# ✓ Built-in
# ✓ Simple and lightweight
# ✗ No dependency resolution
# ✗ Manual requirements.txt management

# Poetry (Modern)
# ✓ Excellent dependency resolution
# ✓ Lock files for reproducibility
# ✓ Build and publish tools
# ✗ Additional tool to install

# Pipenv (Ruby Bundler-inspired)
# ✓ Combines pip and venv
# ✓ Pipfile and Pipfile.lock
# ✗ Slow performance
# ✗ Less popular than Poetry

# Conda (Scientific Computing)
# ✓ Handles non-Python dependencies
# ✓ Great for data science
# ✗ Larger installation
# ✗ Separate ecosystem
```

## Project Dependency Management

```bash
# Development workflow with Poetry
poetry new myproject
cd myproject
poetry add requests pandas
poetry add --group dev pytest black mypy
poetry install
poetry run pytest

# Development workflow with pip + venv
mkdir myproject && cd myproject
python -m venv venv
source venv/bin/activate
pip install requests pandas
pip install pytest black mypy
pip freeze > requirements.txt

# Production deployment
# Use lock files (poetry.lock, Pipfile.lock)
# Or pinned versions in requirements.txt
```

## Advanced pip Usage

```bash
# Install without dependencies
pip install --no-deps package

# Download without installing
pip download requests

# Install to user directory
pip install --user requests

# Install with extras
pip install package[extra1,extra2]
pip install requests[security,socks]

# Upgrade all packages
pip list --outdated --format=freeze | grep -v '^\-e' | cut -d = -f 1 | xargs -n1 pip install -U

# Install from local index
pip install --index-url http://localhost:8080/simple/ package

# Show package dependencies
pip show requests | grep Requires

# Check installed package files
pip show -f requests
```

## Security and Auditing

```bash
# Check for vulnerabilities (pip-audit)
pip install pip-audit
pip-audit

# Safety - check for known security vulnerabilities
pip install safety
safety check

# With Poetry
poetry add --group dev safety
poetry run safety check

# Check outdated packages
pip list --outdated
poetry show --outdated
```

> **Good to know:** `pip` is the standard package manager included with Python. Virtual environments (`venv`) isolate project dependencies. `Poetry` offers superior dependency resolution and lock files for reproducibility. `Pipenv` combines pip and venv but has performance issues. `Conda` is ideal for data science projects with non-Python dependencies. Always use virtual environments to avoid system-wide package conflicts. Pin exact versions in production. `requirements.txt` doesn't lock transitive dependencies—use lock files. Poetry's `pyproject.toml` follows PEP 518. The `--editable` flag (`-e`) installs packages in development mode. Security auditing tools help identify vulnerable dependencies.

---

## References

- [pip - Official Documentation](https://pip.pypa.io/en/stable/)
- [venv - Virtual Environments](https://docs.python.org/3/library/venv.html)
- [Poetry - Official Documentation](https://python-poetry.org/docs/)
- [Pipenv - Python Dev Workflow for Humans](https://pipenv.pypa.io/en/latest/)
- [Conda - Official Documentation](https://docs.conda.io/en/latest/)
- [Python Packaging User Guide](https://packaging.python.org/)
- [PEP 518 - pyproject.toml](https://www.python.org/dev/peps/pep-0518/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
