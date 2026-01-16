# Modules & Packages

## Modules

### Creating a Module

A module is simply a Python file (`.py`):

```python
# math_utils.py
def add(a, b):
    """Add two numbers."""
    return a + b

def multiply(a, b):
    """Multiply two numbers."""
    return a * b

PI = 3.14159

class Calculator:
    def __init__(self):
        self.result = 0
```

### Importing Modules

```python
# Import entire module
import math_utils

result = math_utils.add(5, 3)
print(math_utils.PI)
calc = math_utils.Calculator()

# Import with alias
import math_utils as mu

result = mu.add(5, 3)

# Import specific items
from math_utils import add, PI

result = add(5, 3)
print(PI)

# Import all (not recommended)
from math_utils import *

# Import with alias
from math_utils import multiply as mult
```

### Module Search Path

```python
import sys

# Python searches in this order:
# 1. Current directory
# 2. PYTHONPATH environment variable
# 3. Standard library directories
# 4. Site-packages directories

print(sys.path)  # Shows all search paths

# Add custom path
sys.path.append('/path/to/modules')
sys.path.insert(0, '/priority/path')  # Add at beginning
```

### Module Attributes

```python
# my_module.py
"""This is my module."""

def greet(name):
    print(f"Hello, {name}!")

# Module metadata
__author__ = "Alice"
__version__ = "1.0.0"

if __name__ == "__main__":
    # Code here runs only when file is executed directly
    print("Running as main program")
    greet("World")
else:
    print("Module imported")
```

```python
# Using module attributes
import my_module

print(my_module.__name__)      # my_module
print(my_module.__file__)      # /path/to/my_module.py
print(my_module.__doc__)       # Module docstring
print(my_module.__version__)   # 1.0.0
print(dir(my_module))          # All module attributes
```

### Reloading Modules

```python
import importlib
import my_module

# Module is cached after first import
# To reload after changes:
importlib.reload(my_module)
```

## Packages

### Creating a Package

A package is a directory containing `__init__.py`:

```
mypackage/
    __init__.py
    module1.py
    module2.py
    subpackage/
        __init__.py
        module3.py
```

```python
# mypackage/module1.py
def func1():
    return "Function from module1"

# mypackage/module2.py
def func2():
    return "Function from module2"

# mypackage/subpackage/module3.py
def func3():
    return "Function from module3"
```

### The `__init__.py` File

```python
# mypackage/__init__.py

# Can be empty, or...

# Import items to package level
from .module1 import func1
from .module2 import func2

# Define __all__ for "from package import *"
__all__ = ['func1', 'func2']

# Package-level initialization
print("Initializing mypackage")

# Package metadata
__version__ = "1.0.0"
__author__ = "Alice"
```

### Importing from Packages

```python
# Import entire module
import mypackage.module1

mypackage.module1.func1()

# Import specific module
from mypackage import module1

module1.func1()

# Import specific function
from mypackage.module1 import func1

func1()

# Import from subpackage
from mypackage.subpackage.module3 import func3

func3()

# Import from __init__.py
import mypackage

mypackage.func1()  # Works if imported in __init__.py
```

### Relative Imports

```python
# Inside mypackage/subpackage/module3.py

# Relative imports (only work inside packages)
from . import module4           # From same directory
from .. import module1          # From parent directory
from ..module2 import func2     # Specific import from parent
from ...other_package import x  # Two levels up

# Absolute imports (preferred)
from mypackage import module1
from mypackage.subpackage import module4
```

### Package Structure Example

```
myproject/
    setup.py
    README.md
    requirements.txt
    mypackage/
        __init__.py
        core/
            __init__.py
            engine.py
            utils.py
        api/
            __init__.py
            rest.py
            graphql.py
        models/
            __init__.py
            user.py
            product.py
    tests/
        __init__.py
        test_core.py
        test_api.py
```

```python
# myproject/mypackage/__init__.py
"""MyPackage - A Python package example."""

from .core import engine
from .api import rest

__version__ = "1.0.0"
__all__ = ['engine', 'rest']

# myproject/mypackage/core/__init__.py
from .engine import Engine
from .utils import helper_function

__all__ = ['Engine', 'helper_function']

# myproject/mypackage/core/engine.py
class Engine:
    def __init__(self):
        self.running = False

    def start(self):
        self.running = True
        return "Engine started"
```

### Namespace Packages

Packages without `__init__.py` (PEP 420):

```
namespace_package/
    subpackage1/
        module1.py
    subpackage2/
        module2.py
```

```python
# Both can be imported
from namespace_package.subpackage1 import module1
from namespace_package.subpackage2 import module2
```

## Import Mechanics

### Import Statement Execution

```python
# When you import a module:
# 1. Python checks sys.modules cache
# 2. If not cached, searches sys.path
# 3. Loads and executes the module
# 4. Creates module object
# 5. Binds to name in importing namespace
# 6. Caches in sys.modules

import sys

# Check if module is loaded
if 'math' in sys.modules:
    print("math module is loaded")

# Get module reference
import math
math_ref = sys.modules['math']
```

### Circular Imports

```python
# a.py
import b

def func_a():
    return "A"

# b.py
import a  # Circular import!

def func_b():
    return "B"

# Solution 1: Import inside function
# a.py
def func_a():
    import b  # Import when needed
    return "A"

# Solution 2: Restructure code to eliminate circular dependency
```

### Lazy Imports

```python
# Import only when needed (faster startup)
def process_data():
    import pandas as pd  # Imported only when function is called
    return pd.DataFrame()

# Using importlib
import importlib

def get_module(name):
    return importlib.import_module(name)

# Dynamic import
module_name = "math"
math = get_module(module_name)
```

## `__all__` Attribute

```python
# mymodule.py
def public_function():
    pass

def _private_function():
    pass

# Controls "from module import *"
__all__ = ['public_function']

# Without __all__, both would be imported with *
# With __all__, only items listed are imported with *
```

## Module vs Script

```python
# my_script.py

def main():
    """Main function."""
    print("Running main function")

def helper():
    """Helper function."""
    pass

# This runs only when executed as script
if __name__ == "__main__":
    main()
else:
    # This runs when imported
    print("Module imported")

# Usage:
# As script: python my_script.py
# As module: import my_script; my_script.helper()
```

## Built-in Module Functions

```python
import sys
import math

# Get module name
print(math.__name__)  # 'math'

# Check if module has attribute
print(hasattr(math, 'sqrt'))  # True

# Get attribute dynamically
sqrt_func = getattr(math, 'sqrt')
print(sqrt_func(16))  # 4.0

# List all attributes
print(dir(math))

# Get module file location
print(math.__file__)  # Path to math module

# Inspect module
import inspect

print(inspect.getfile(math))
print(inspect.getsource(math.sqrt))  # Won't work for C modules
```

## Import Best Practices

```python
# Good: Standard library, third-party, local
import os
import sys
from typing import List, Dict

import requests
import pandas as pd

from mypackage import module1
from mypackage.utils import helper

# Group imports and separate with blank lines
# Use absolute imports over relative
# Import modules, not specific items (for clarity)
# Use aliases for long names
import numpy as np
import matplotlib.pyplot as plt

# Avoid wildcard imports
# from module import *  # Bad

# Import specific items
from module import func1, func2  # Good
```

## Virtual Environments

```bash
# Create virtual environment
python -m venv venv

# Activate
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate     # Windows

# Install packages
pip install requests

# Freeze dependencies
pip freeze > requirements.txt

# Install from requirements
pip install -r requirements.txt

# Deactivate
deactivate
```

## Package Distribution

```python
# setup.py
from setuptools import setup, find_packages

setup(
    name="mypackage",
    version="1.0.0",
    description="A sample Python package",
    author="Alice",
    author_email="alice@example.com",
    packages=find_packages(),
    install_requires=[
        "requests>=2.28.0",
        "numpy>=1.24.0",
    ],
    python_requires=">=3.8",
    entry_points={
        'console_scripts': [
            'mycommand=mypackage.cli:main',
        ],
    },
)
```

```bash
# Install package in development mode
pip install -e .

# Build distribution
python setup.py sdist bdist_wheel

# Upload to PyPI
pip install twine
twine upload dist/*
```

> **Good to know:** Every Python file is a module. Packages are directories with `__init__.py`. The `__name__` variable equals `"__main__"` when a file is executed directly. Use relative imports only within packages, and prefer absolute imports for clarity. `sys.modules` caches all imported modules. Circular imports can be resolved by importing inside functions or restructuring code. The `__all__` attribute controls what `*` imports. Virtual environments isolate project dependencies. PEP 420 introduced namespace packages without `__init__.py`. The `__init__.py` file runs when a package is imported.

---

## References

- [Python - Modules](https://docs.python.org/3/tutorial/modules.html)
- [Python - Packages](https://docs.python.org/3/tutorial/modules.html#packages)
- [Python - Import System](https://docs.python.org/3/reference/import.html)
- [Real Python - Python Modules and Packages](https://realpython.com/python-modules-packages/)
- [PEP 420 - Implicit Namespace Packages](https://www.python.org/dev/peps/pep-0420/)
- [Python Packaging User Guide](https://packaging.python.org/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
