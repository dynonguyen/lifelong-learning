# Project Structure

## Basic Project Layout

### Simple Script

```
my_script/
    script.py
    requirements.txt
    README.md
```

### Single Package

```
myproject/
    mypackage/
        __init__.py
        module1.py
        module2.py
        utils.py
    tests/
        __init__.py
        test_module1.py
        test_module2.py
    requirements.txt
    setup.py
    README.md
    LICENSE
    .gitignore
```

### Application Layout

```
myapp/
    myapp/
        __init__.py
        __main__.py
        cli.py
        core/
            __init__.py
            engine.py
            config.py
        api/
            __init__.py
            routes.py
            handlers.py
        models/
            __init__.py
            user.py
            product.py
        utils/
            __init__.py
            helpers.py
            validators.py
    tests/
        __init__.py
        test_core/
        test_api/
        test_models/
    docs/
        conf.py
        index.md
    requirements.txt
    requirements-dev.txt
    setup.py
    pyproject.toml
    README.md
    LICENSE
    .gitignore
```

## Src Layout (Recommended)

### Why Src Layout?

```
# Problem with flat layout:
myproject/
    mypackage/
        __init__.py
    tests/
    setup.py

# Running tests imports local package (not installed)
# This can hide import errors and packaging issues

# Solution: src layout
myproject/
    src/
        mypackage/
            __init__.py
    tests/
    setup.py

# Forces testing against installed package
# Catches import and packaging issues early
```

### Complete Src Layout

```
myproject/
    src/
        mypackage/
            __init__.py
            __main__.py
            core/
                __init__.py
                module1.py
                module2.py
            api/
                __init__.py
                endpoints.py
            models/
                __init__.py
                user.py
            utils/
                __init__.py
                helpers.py
    tests/
        __init__.py
        unit/
            test_core.py
            test_models.py
        integration/
            test_api.py
        fixtures/
            __init__.py
            data.py
    docs/
        source/
            conf.py
            index.rst
    scripts/
        deploy.sh
        migrate.py
    .github/
        workflows/
            tests.yml
            release.yml
    requirements/
        base.txt
        dev.txt
        prod.txt
    pyproject.toml
    setup.py
    setup.cfg
    MANIFEST.in
    README.md
    LICENSE
    CHANGELOG.md
    .gitignore
    .pre-commit-config.yaml
```

## Configuration Files

### pyproject.toml

```toml
[build-system]
requires = ["setuptools>=65.0", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "mypackage"
version = "1.0.0"
description = "A sample Python package"
readme = "README.md"
requires-python = ">=3.8"
license = {text = "MIT"}
authors = [
    {name = "Alice", email = "alice@example.com"}
]
keywords = ["sample", "package"]
classifiers = [
    "Development Status :: 4 - Beta",
    "Intended Audience :: Developers",
    "License :: OSI Approved :: MIT License",
    "Programming Language :: Python :: 3",
    "Programming Language :: Python :: 3.8",
    "Programming Language :: Python :: 3.9",
    "Programming Language :: Python :: 3.10",
    "Programming Language :: Python :: 3.11",
]

dependencies = [
    "requests>=2.28.0",
    "click>=8.0.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0.0",
    "black>=23.0.0",
    "flake8>=6.0.0",
    "mypy>=1.0.0",
]
docs = [
    "sphinx>=5.0.0",
    "sphinx-rtd-theme>=1.0.0",
]

[project.urls]
Homepage = "https://github.com/user/mypackage"
Documentation = "https://mypackage.readthedocs.io"
Repository = "https://github.com/user/mypackage.git"
Changelog = "https://github.com/user/mypackage/blob/main/CHANGELOG.md"

[project.scripts]
mypackage = "mypackage.cli:main"

[tool.setuptools]
package-dir = {"" = "src"}

[tool.setuptools.packages.find]
where = ["src"]

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_functions = ["test_*"]
addopts = "-v --cov=mypackage --cov-report=html"

[tool.black]
line-length = 88
target-version = ["py38", "py39", "py310", "py311"]
include = '\.pyi?$'

[tool.isort]
profile = "black"
line_length = 88

[tool.mypy]
python_version = "3.8"
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true
```

### setup.py

```python
"""Setup script for mypackage."""

from setuptools import setup, find_packages

# For compatibility, most config is in pyproject.toml
setup(
    packages=find_packages(where="src"),
    package_dir={"": "src"},
)
```

### setup.cfg (Alternative)

```ini
[metadata]
name = mypackage
version = 1.0.0
description = A sample Python package
long_description = file: README.md
long_description_content_type = text/markdown
author = Alice
author_email = alice@example.com
license = MIT
classifiers =
    Development Status :: 4 - Beta
    Programming Language :: Python :: 3

[options]
packages = find:
package_dir =
    = src
python_requires = >=3.8
install_requires =
    requests>=2.28.0
    click>=8.0.0

[options.packages.find]
where = src

[options.extras_require]
dev =
    pytest>=7.0.0
    black>=23.0.0
```

### MANIFEST.in

```
# Include documentation
include README.md
include LICENSE
include CHANGELOG.md

# Include package data
recursive-include src/mypackage/data *
recursive-include src/mypackage/templates *

# Exclude test and development files
recursive-exclude tests *
recursive-exclude docs *
exclude .gitignore
exclude .pre-commit-config.yaml
```

### .gitignore

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

# Virtual environments
venv/
env/
ENV/

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# Testing
.pytest_cache/
.coverage
htmlcov/
.tox/

# MyPy
.mypy_cache/
.dmypy.json

# Documentation
docs/_build/

# OS
.DS_Store
Thumbs.db
```

## Entry Points

### CLI Application

```python
# src/mypackage/__main__.py
"""Main entry point for CLI."""

from mypackage.cli import main

if __name__ == "__main__":
    main()

# src/mypackage/cli.py
"""Command-line interface."""

import click

@click.group()
def main():
    """MyPackage CLI."""
    pass

@main.command()
@click.argument('name')
def greet(name):
    """Greet someone."""
    click.echo(f"Hello, {name}!")

@main.command()
@click.option('--count', default=1, help='Number of times')
def repeat(count):
    """Repeat greeting."""
    for _ in range(count):
        click.echo("Hello!")

if __name__ == "__main__":
    main()
```

```toml
# pyproject.toml
[project.scripts]
mypackage = "mypackage.cli:main"
greet = "mypackage.cli:greet"
```

```bash
# After installation:
mypackage greet Alice
mypackage repeat --count 3

# Or run as module:
python -m mypackage greet Alice
```

### Web Application

```python
# src/mypackage/__init__.py
"""MyPackage web application."""

from flask import Flask

def create_app(config=None):
    """Application factory."""
    app = Flask(__name__)

    if config:
        app.config.update(config)

    # Register blueprints
    from mypackage.api.routes import api_bp
    app.register_blueprint(api_bp, url_prefix='/api')

    return app

# src/mypackage/__main__.py
"""Run development server."""

from mypackage import create_app

if __name__ == "__main__":
    app = create_app()
    app.run(debug=True)

# Run:
# python -m mypackage
```

## Testing Structure

### Test Organization

```
tests/
    __init__.py
    conftest.py              # Shared fixtures
    unit/
        __init__.py
        test_models.py
        test_utils.py
        test_core/
            __init__.py
            test_engine.py
    integration/
        __init__.py
        test_api.py
        test_database.py
    e2e/
        __init__.py
        test_workflows.py
    fixtures/
        __init__.py
        data.py
        factories.py
```

### conftest.py

```python
"""Shared test fixtures."""

import pytest
from mypackage import create_app
from mypackage.database import Database

@pytest.fixture
def app():
    """Create application for testing."""
    app = create_app({"TESTING": True})
    yield app

@pytest.fixture
def client(app):
    """Create test client."""
    return app.test_client()

@pytest.fixture
def database():
    """Create test database."""
    db = Database(":memory:")
    db.create_tables()
    yield db
    db.close()

@pytest.fixture
def sample_user():
    """Create sample user."""
    return {
        "name": "Alice",
        "email": "alice@example.com",
        "age": 30
    }
```

## Documentation

### Sphinx Configuration

```python
# docs/source/conf.py
"""Sphinx configuration."""

import os
import sys

# Add src to path
sys.path.insert(0, os.path.abspath('../../src'))

project = 'MyPackage'
author = 'Alice'
version = '1.0.0'
release = '1.0.0'

extensions = [
    'sphinx.ext.autodoc',
    'sphinx.ext.napoleon',
    'sphinx.ext.viewcode',
    'sphinx.ext.intersphinx',
]

templates_path = ['_templates']
exclude_patterns = []

html_theme = 'sphinx_rtd_theme'
html_static_path = ['_static']

intersphinx_mapping = {
    'python': ('https://docs.python.org/3', None),
}
```

```rst
.. docs/source/index.rst

MyPackage Documentation
=======================

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   installation
   quickstart
   api
   contributing

Installation
============

Install from PyPI:

.. code-block:: bash

   pip install mypackage

API Reference
=============

.. automodule:: mypackage.core
   :members:
   :undoc-members:
   :show-inheritance:
```

## Packaging and Distribution

### Building Package

```bash
# Install build tools
pip install build twine

# Build distribution
python -m build

# Creates:
# dist/mypackage-1.0.0.tar.gz
# dist/mypackage-1.0.0-py3-none-any.whl
```

### Publishing to PyPI

```bash
# Upload to TestPyPI (testing)
python -m twine upload --repository testpypi dist/*

# Upload to PyPI
python -m twine upload dist/*

# Using API token
python -m twine upload -u __token__ -p pypi-token dist/*
```

### Version Management

```python
# src/mypackage/__init__.py
"""MyPackage."""

__version__ = "1.0.0"

# Or use setuptools_scm for git-based versioning
# pyproject.toml
[tool.setuptools_scm]
write_to = "src/mypackage/_version.py"
```

## CI/CD

### GitHub Actions

```yaml
# .github/workflows/tests.yml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ['3.8', '3.9', '3.10', '3.11']

    steps:
      - uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: ${{ matrix.python-version }}

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -e .[dev]

      - name: Lint
        run: |
          black --check src tests
          flake8 src tests
          mypy src

      - name: Test
        run: |
          pytest --cov=mypackage --cov-report=xml

      - name: Upload coverage
        uses: codecov/codecov-action@v3
```

### Release Workflow

```yaml
# .github/workflows/release.yml
name: Release

on:
  release:
    types: [published]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install build twine

      - name: Build package
        run: python -m build

      - name: Publish to PyPI
        env:
          TWINE_USERNAME: __token__
          TWINE_PASSWORD: ${{ secrets.PYPI_TOKEN }}
        run: twine upload dist/*
```

> **Good to know:** Use src layout to avoid testing against uninstalled package. `pyproject.toml` is the modern standard for Python project metadata. Separate requirements files for different environments (dev, prod, test). Use `__main__.py` to make packages executable with `python -m`. Entry points in `pyproject.toml` create CLI commands. Organize tests into unit, integration, and e2e directories. Use `conftest.py` for shared pytest fixtures. Sphinx generates professional documentation. `MANIFEST.in` controls which files are included in distributions. Version numbers in one place (DRY principle). Use GitHub Actions for CI/CD. Tag releases trigger automated PyPI publishing. Include README, LICENSE, and CHANGELOG in all projects.

---

## References

- [Python Packaging User Guide](https://packaging.python.org/)
- [PEP 517 - Build System](https://www.python.org/dev/peps/pep-0517/)
- [PEP 518 - pyproject.toml](https://www.python.org/dev/peps/pep-0518/)
- [Setuptools Documentation](https://setuptools.pypa.io/)
- [Sphinx Documentation](https://www.sphinx-doc.org/)
- [Real Python - Packaging](https://realpython.com/pypi-publish-python-package/)
- [Python Application Layouts](https://realpython.com/python-application-layouts/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
