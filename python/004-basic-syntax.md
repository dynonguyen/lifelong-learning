# Basic Syntax/Declarations

## Python Script Structure

```python
#!/usr/bin/env python3
"""
Module docstring: Brief description of the module.
"""

# Imports
import os
import sys
from typing import List

# Constants
MAX_SIZE = 100
DEFAULT_NAME = "Unknown"

# Functions
def main():
    """Main entry point of the script."""
    print("Hello, World!")

# Execute when run as script
if __name__ == "__main__":
    main()
```

## Indentation

Python uses indentation (not braces) to define code blocks:

```python
# Correct - 4 spaces (PEP 8 standard)
def greet(name):
    if name:
        print(f"Hello, {name}!")
    else:
        print("Hello, stranger!")

# WRONG - inconsistent indentation causes IndentationError
def greet(name):
    if name:
      print(f"Hello, {name}!")  # 2 spaces
        print("Welcome!")  # 4 spaces - ERROR
```

## Comments

```python
# Single-line comment

# Multi-line comment
# using multiple
# single-line comments

"""
Multi-line string (often used as multi-line comment)
but technically creates a string object
"""

def function():
    """
    Docstring - documentation string for functions/classes/modules.
    Accessible via __doc__ attribute.
    """
    pass
```

## Statements and Line Continuation

```python
# Simple statement
x = 10

# Multiple statements on one line (discouraged)
x = 1; y = 2; z = 3

# Line continuation with backslash
total = 1 + 2 + 3 + \
        4 + 5 + 6

# Implicit line continuation (preferred)
total = (1 + 2 + 3 +
         4 + 5 + 6)

# Lists, dicts, function calls allow implicit continuation
names = [
    "Alice",
    "Bob",
    "Charlie",
]

# Multiple assignment
x, y, z = 1, 2, 3
```

## Variables

```python
# Assignment
name = "Alice"
age = 30
is_student = True

# Multiple assignment
x = y = z = 0

# Tuple unpacking
a, b = 1, 2
first, *rest = [1, 2, 3, 4]  # first=1, rest=[2,3,4]

# Swapping variables
a, b = b, a

# Type hints (optional)
name: str = "Alice"
age: int = 30
scores: list[int] = [95, 87, 92]
```

## Constants

Python has no true constants, but convention uses UPPER_CASE:

```python
# Constants (by convention only)
PI = 3.14159
MAX_CONNECTIONS = 100
DEFAULT_TIMEOUT = 30

# Can still be modified (not enforced)
PI = 3.14  # Works but violates convention
```

## Naming Conventions

```python
# Variables and functions: snake_case
user_name = "Alice"
def calculate_total(): pass

# Classes: PascalCase
class UserAccount: pass
class HTTPResponse: pass

# Constants: UPPER_SNAKE_CASE
MAX_SIZE = 100
API_KEY = "secret"

# Private (by convention): single underscore prefix
class MyClass:
    def __init__(self):
        self._internal_value = 0  # Private by convention
        self.__truly_private = 0   # Name mangling

# Special methods: double underscore (dunder)
def __init__(self): pass
def __str__(self): pass
```

## Import Statements

```python
# Single import
import math

# Multiple imports (one per line preferred)
import os
import sys

# From import
from collections import defaultdict
from typing import List, Dict

# Import with alias
import numpy as np
import pandas as pd
from datetime import datetime as dt

# Import all (discouraged - pollutes namespace)
from math import *  # Avoid this

# Relative imports (within packages)
from . import module          # Same directory
from .. import module         # Parent directory
from .subpkg import module    # Subdirectory
```

## Operators

```python
# Arithmetic
10 + 5   # 15 - Addition
10 - 5   # 5  - Subtraction
10 * 5   # 50 - Multiplication
10 / 5   # 2.0 - Division (always returns float)
10 // 3  # 3  - Floor division
10 % 3   # 1  - Modulus
10 ** 2  # 100 - Exponentiation

# Comparison
x == y   # Equal
x != y   # Not equal
x > y    # Greater than
x < y    # Less than
x >= y   # Greater than or equal
x <= y   # Less than or equal

# Logical
x and y  # Logical AND
x or y   # Logical OR
not x    # Logical NOT

# Identity
x is y       # Same object
x is not y   # Different object

# Membership
x in y       # x is in y
x not in y   # x is not in y

# Bitwise
x & y    # AND
x | y    # OR
x ^ y    # XOR
~x       # NOT
x << 2   # Left shift
x >> 2   # Right shift

# Assignment operators
x += 5   # x = x + 5
x -= 5   # x = x - 5
x *= 5   # x = x * 5
x /= 5   # x = x / 5
x //= 5  # x = x // 5
x %= 5   # x = x % 5
x **= 5  # x = x ** 5
```

## Conditional Expressions (Ternary)

```python
# Ternary operator
result = "even" if x % 2 == 0 else "odd"

# Equivalent to
if x % 2 == 0:
    result = "even"
else:
    result = "odd"
```

## None

Python's null value:

```python
x = None

# Check for None
if x is None:
    print("x is None")

# Avoid using == with None
if x == None:  # Works but not idiomatic
    pass

# Functions return None by default
def do_something():
    pass

result = do_something()
print(result)  # None
```

## Pass Statement

Null operation placeholder:

```python
# Empty function
def todo():
    pass  # Placeholder for future code

# Empty class
class MyClass:
    pass

# Empty conditional
if condition:
    pass  # Do nothing
else:
    do_something()
```

## Del Statement

Delete references to objects:

```python
x = 10
del x  # Remove variable binding

# Delete list items
lst = [1, 2, 3, 4]
del lst[1]      # [1, 3, 4]
del lst[1:3]    # [1]

# Delete dictionary keys
d = {"a": 1, "b": 2}
del d["a"]      # {"b": 2}
```

## Global and Nonlocal

```python
count = 0

def increment():
    global count  # Modify global variable
    count += 1

def outer():
    x = 10
    def inner():
        nonlocal x  # Modify enclosing scope variable
        x += 1
    inner()
    print(x)  # 11

outer()
```

## Expression vs Statement

```python
# Expression - produces a value
2 + 3
x > 5
"hello".upper()

# Statement - performs an action
x = 10
if x > 5:
    print(x)
def foo(): pass

# Some things are expressions in Python (unlike many languages)
result = (x if x > 0 else 0)  # Ternary is an expression
```

## Semicolons

Optional but discouraged:

```python
# Don't do this
x = 1; y = 2; z = 3

# Do this instead
x = 1
y = 2
z = 3

# Only use semicolons in one-liners if absolutely necessary
if x > 0: print("positive")  # Discouraged but valid
```

> **Good to know:** Python's indentation is not just style - it's syntax. Mixing tabs and spaces causes errors (use 4 spaces per PEP 8). The `pass` statement is useful for creating minimal classes or functions that will be implemented later. Unlike many languages, Python assignment is a statement, not an expression, so you can't do `if (x = 5):`. The walrus operator `:=` (Python 3.8+) allows assignment expressions.

---

## References

- [Python Language Reference - Lexical Analysis](https://docs.python.org/3/reference/lexical_analysis.html)
- [PEP 8 - Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/)
- [Python Tutorial - Control Flow](https://docs.python.org/3/tutorial/controlflow.html)
- [Real Python - Python Naming Conventions](https://realpython.com/python-pep8/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
