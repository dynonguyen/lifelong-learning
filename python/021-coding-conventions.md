# Coding Conventions

## PEP 8 - Style Guide

### Indentation and Spacing

```python
# Use 4 spaces per indentation level (not tabs)
def my_function():
    if True:
        print("Hello")

# Maximum line length: 79 characters
# For docstrings/comments: 72 characters

# Break long lines
result = some_function_with_long_name(
    argument1, argument2,
    argument3, argument4
)

# Or
result = some_function_with_long_name(
    argument1,
    argument2,
    argument3,
    argument4,
)

# Blank lines
# Two blank lines around top-level functions and classes
def function_one():
    pass


def function_two():
    pass


class MyClass:
    pass


# One blank line around methods
class MyClass:
    def method_one(self):
        pass

    def method_two(self):
        pass
```

### Imports

```python
# Imports at top of file
# Order: standard library, third-party, local
import os
import sys
from typing import List, Dict

import numpy as np
import pandas as pd
import requests

from mypackage import module1
from mypackage.utils import helper

# One import per line
# Good
import os
import sys

# Bad
import os, sys

# Multiple items from same module is OK
from typing import List, Dict, Tuple

# Avoid wildcard imports
# Bad
from module import *

# Good
from module import specific_function

# Absolute imports preferred
from mypackage.subpackage import module

# Relative imports OK within packages
from . import sibling_module
from .. import parent_module
```

### Whitespace

```python
# Around operators
x = 1 + 2
y = x * 3

# Not around = in keyword arguments
function(arg1=value1, arg2=value2)

# After commas
items = [1, 2, 3, 4]
dictionary = {"a": 1, "b": 2}

# No space inside brackets
list[0]
dict["key"]
tuple(1, 2)

# No trailing whitespace
x = 1  # Comment (no trailing spaces)

# Slice operators
x[1:3]
x[1:3:2]
x[::2]

# Multiple statements on one line (avoid)
# Bad
if x: y = 1

# Good
if x:
    y = 1
```

## Naming Conventions

### Variable and Function Names

```python
# Functions and variables: lowercase_with_underscores
def calculate_total():
    pass

user_name = "Alice"
total_count = 42

# Constants: UPPERCASE_WITH_UNDERSCORES
MAX_SIZE = 100
DEFAULT_TIMEOUT = 30
PI = 3.14159

# Private (internal use): _leading_underscore
def _internal_helper():
    pass

_internal_cache = {}

# Name mangling: __double_leading_underscore
class MyClass:
    def __init__(self):
        self.__private = "private"  # Becomes _MyClass__private

# Dunder methods: __double_underscore_both_sides__
def __init__(self):
    pass

def __str__(self):
    pass
```

### Class Names

```python
# Classes: CapWords (PascalCase)
class MyClass:
    pass

class HTTPConnection:
    pass

class UserAccount:
    pass

# Exception classes: end with "Error"
class ValidationError(Exception):
    pass

class DatabaseError(Exception):
    pass
```

### Module and Package Names

```python
# Modules: lowercase, short, underscores if needed
# Good
mymodule.py
my_utils.py
database_handler.py

# Avoid
MyModule.py
my-module.py

# Packages: lowercase, short, no underscores preferred
# Good
mypackage/
utils/
database/

# Avoid
MyPackage/
my_package/
```

## Type Hints

### Function Annotations

```python
from typing import List, Dict, Optional, Union

# Basic type hints
def greet(name: str) -> str:
    return f"Hello, {name}!"

def add(a: int, b: int) -> int:
    return a + b

# Collection types
def process_items(items: List[str]) -> Dict[str, int]:
    return {item: len(item) for item in items}

# Optional (can be None)
def find_user(user_id: int) -> Optional[User]:
    if user_id > 0:
        return User(user_id)
    return None

# Union (multiple types)
def parse_value(value: Union[int, str]) -> int:
    if isinstance(value, str):
        return int(value)
    return value

# Modern syntax (Python 3.10+)
def find_user(user_id: int) -> User | None:
    pass

def parse_value(value: int | str) -> int:
    pass
```

### Variable Annotations

```python
# Variable type hints
name: str = "Alice"
age: int = 30
scores: List[int] = [85, 90, 95]
user: Dict[str, str] = {"name": "Alice"}

# Without initial value
names: List[str]  # Declared but not assigned

# Class attributes
class User:
    name: str
    age: int
    email: Optional[str] = None

    def __init__(self, name: str, age: int):
        self.name = name
        self.age = age
```

### Advanced Type Hints

```python
from typing import Callable, TypeVar, Protocol, Generic

# Callable
def apply_function(func: Callable[[int], int], value: int) -> int:
    return func(value)

# Generic types
T = TypeVar('T')

def first(items: List[T]) -> T:
    return items[0]

# Protocol (structural typing)
class Drawable(Protocol):
    def draw(self) -> None:
        ...

def render(obj: Drawable) -> None:
    obj.draw()

# Generic class
class Stack(Generic[T]):
    def __init__(self) -> None:
        self._items: List[T] = []

    def push(self, item: T) -> None:
        self._items.append(item)

    def pop(self) -> T:
        return self._items.pop()
```

## Docstrings

### Function Docstrings

```python
def calculate_area(width: float, height: float) -> float:
    """Calculate the area of a rectangle.

    Args:
        width: The width of the rectangle.
        height: The height of the rectangle.

    Returns:
        The area of the rectangle.

    Raises:
        ValueError: If width or height is negative.

    Example:
        >>> calculate_area(5, 10)
        50.0
    """
    if width < 0 or height < 0:
        raise ValueError("Dimensions must be positive")
    return width * height

# One-line docstring (simple functions)
def square(x: int) -> int:
    """Return the square of x."""
    return x * x
```

### Class Docstrings

```python
class BankAccount:
    """A bank account with balance management.

    This class provides methods for depositing, withdrawing,
    and checking the balance of a bank account.

    Attributes:
        account_number: The unique account identifier.
        balance: The current account balance.
        owner: The account owner's name.

    Example:
        >>> account = BankAccount("123", "Alice")
        >>> account.deposit(100)
        >>> account.balance
        100.0
    """

    def __init__(self, account_number: str, owner: str):
        """Initialize a new bank account.

        Args:
            account_number: Unique account identifier.
            owner: Name of the account owner.
        """
        self.account_number = account_number
        self.owner = owner
        self.balance = 0.0

    def deposit(self, amount: float) -> None:
        """Deposit money into the account.

        Args:
            amount: Amount to deposit (must be positive).

        Raises:
            ValueError: If amount is negative.
        """
        if amount < 0:
            raise ValueError("Amount must be positive")
        self.balance += amount
```

### Module Docstrings

```python
"""Bank account management module.

This module provides classes and functions for managing
bank accounts, including deposits, withdrawals, and
balance inquiries.

Classes:
    BankAccount: Represents a bank account.
    SavingsAccount: Savings account with interest.

Functions:
    calculate_interest: Calculate interest on balance.

Example:
    >>> account = BankAccount("123", "Alice")
    >>> account.deposit(1000)
"""

import datetime
from typing import Optional
```

## Code Structure

### Function Length

```python
# Keep functions short and focused
# Bad: Long function doing multiple things
def process_user_data(user):
    # Validation
    if not user.name:
        raise ValueError()
    if not user.email:
        raise ValueError()

    # Processing
    user.name = user.name.strip()
    user.email = user.email.lower()

    # Database operations
    db.connect()
    db.save(user)
    db.disconnect()

    # Notification
    send_email(user.email, "Welcome")

# Good: Split into focused functions
def validate_user(user: User) -> None:
    """Validate user data."""
    if not user.name:
        raise ValueError("Name is required")
    if not user.email:
        raise ValueError("Email is required")

def normalize_user(user: User) -> User:
    """Normalize user data."""
    user.name = user.name.strip()
    user.email = user.email.lower()
    return user

def save_user(user: User) -> None:
    """Save user to database."""
    with database_connection() as db:
        db.save(user)

def process_user_data(user: User) -> None:
    """Process user data."""
    validate_user(user)
    user = normalize_user(user)
    save_user(user)
    send_welcome_email(user)
```

### Class Organization

```python
class MyClass:
    """Class docstring."""

    # Class variables
    class_var = "class variable"

    def __init__(self, value):
        """Initialize instance."""
        # Instance variables
        self.value = value

    # Public methods
    def public_method(self):
        """Public method."""
        pass

    # Properties
    @property
    def value(self):
        """Get value."""
        return self._value

    @value.setter
    def value(self, val):
        """Set value."""
        self._value = val

    # Private methods (at end)
    def _private_method(self):
        """Private helper method."""
        pass

    # Dunder methods
    def __str__(self):
        """String representation."""
        return f"MyClass({self.value})"

    def __repr__(self):
        """Developer representation."""
        return f"MyClass(value={self.value!r})"
```

## Best Practices

### Pythonic Code

```python
# Use list comprehensions
# Bad
squares = []
for x in range(10):
    squares.append(x ** 2)

# Good
squares = [x ** 2 for x in range(10)]

# Use enumerate
# Bad
for i in range(len(items)):
    print(i, items[i])

# Good
for i, item in enumerate(items):
    print(i, item)

# Use zip
# Bad
for i in range(len(names)):
    print(names[i], ages[i])

# Good
for name, age in zip(names, ages):
    print(name, age)

# Use dict.get() with default
# Bad
if "key" in dictionary:
    value = dictionary["key"]
else:
    value = "default"

# Good
value = dictionary.get("key", "default")

# Use in operator
# Bad
if x == 1 or x == 2 or x == 3:
    pass

# Good
if x in (1, 2, 3):
    pass

# Context managers
# Bad
file = open("data.txt")
data = file.read()
file.close()

# Good
with open("data.txt") as file:
    data = file.read()
```

### EAFP vs LBYL

```python
# EAFP: Easier to Ask for Forgiveness than Permission (Pythonic)
try:
    value = dictionary["key"]
except KeyError:
    value = None

# LBYL: Look Before You Leap
if "key" in dictionary:
    value = dictionary["key"]
else:
    value = None

# EAFP is preferred for better performance
# and handling race conditions
```

### String Formatting

```python
name = "Alice"
age = 30

# f-strings (Python 3.6+, preferred)
message = f"My name is {name} and I'm {age} years old"

# format() method
message = "My name is {} and I'm {} years old".format(name, age)
message = "My name is {name} and I'm {age} years old".format(name=name, age=age)

# % formatting (old style, avoid)
message = "My name is %s and I'm %d years old" % (name, age)

# Multi-line f-strings
message = (
    f"Name: {name}\n"
    f"Age: {age}\n"
    f"Status: active"
)
```

### Boolean Comparisons

```python
# Don't compare to True/False/None explicitly
# Bad
if x == True:
    pass
if y == False:
    pass
if z == None:
    pass

# Good
if x:
    pass
if not y:
    pass
if z is None:
    pass

# Check for empty sequences
# Bad
if len(items) == 0:
    pass

# Good
if not items:
    pass

# Check for non-empty
# Bad
if len(items) > 0:
    pass

# Good
if items:
    pass
```

### Return Statements

```python
# Return early to reduce nesting
# Bad
def process_data(data):
    if data is not None:
        if len(data) > 0:
            if validate(data):
                return transform(data)
    return None

# Good
def process_data(data):
    if data is None:
        return None

    if not data:
        return None

    if not validate(data):
        return None

    return transform(data)
```

## Tools

### Code Formatters

```bash
# Black - opinionated formatter
pip install black
black myfile.py
black src/

# Configuration: pyproject.toml
[tool.black]
line-length = 88
target-version = ['py311']
```

### Linters

```bash
# Flake8 - style guide enforcement
pip install flake8
flake8 myfile.py

# Configuration: .flake8
[flake8]
max-line-length = 88
extend-ignore = E203, W503

# Pylint - comprehensive linting
pip install pylint
pylint myfile.py

# Configuration: .pylintrc or pyproject.toml
```

### Type Checkers

```bash
# mypy - static type checker
pip install mypy
mypy myfile.py

# Configuration: mypy.ini
[mypy]
python_version = 3.11
warn_return_any = True
warn_unused_configs = True
disallow_untyped_defs = True
```

### Import Sorting

```bash
# isort - sort imports
pip install isort
isort myfile.py

# Configuration: pyproject.toml
[tool.isort]
profile = "black"
line_length = 88
```

### Pre-commit Hooks

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/psf/black
    rev: 23.3.0
    hooks:
      - id: black

  - repo: https://github.com/PyCQA/flake8
    rev: 6.0.0
    hooks:
      - id: flake8

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.3.0
    hooks:
      - id: mypy

  - repo: https://github.com/PyCQA/isort
    rev: 5.12.0
    hooks:
      - id: isort
```

```bash
# Install pre-commit
pip install pre-commit

# Install hooks
pre-commit install

# Run manually
pre-commit run --all-files
```

> **Good to know:** PEP 8 is the official Python style guide. Use 4 spaces for indentation, never tabs. Maximum line length is 79 characters (88 for Black). Imports should be at the top: stdlib, third-party, then local. Use `snake_case` for functions and variables, `PascalCase` for classes. Type hints improve code clarity and enable static analysis. Docstrings should follow Google, NumPy, or Sphinx style. Keep functions short and focused on one task. Use f-strings for string formatting. Follow EAFP (try-except) over LBYL (if checks). Use list comprehensions and generators for conciseness. Black is the most popular code formatter. Flake8 and Pylint check style. mypy performs static type checking. isort organizes imports. Pre-commit hooks enforce standards before commits.

---

## References

- [PEP 8 - Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/)
- [PEP 257 - Docstring Conventions](https://www.python.org/dev/peps/pep-0257/)
- [PEP 484 - Type Hints](https://www.python.org/dev/peps/pep-0484/)
- [Black - Code Formatter](https://black.readthedocs.io/)
- [Flake8 - Style Guide Enforcement](https://flake8.pycqa.org/)
- [mypy - Static Type Checker](http://mypy-lang.org/)
- [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
