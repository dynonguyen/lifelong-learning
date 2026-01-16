# Variables & Constants

## Variable Declaration

Python uses dynamic typing - no need to declare variable types:

```python
# Simple assignment
name = "Alice"
age = 30
is_student = True
balance = 1234.56

# Variables can change type
x = 10        # int
x = "hello"   # str - totally valid
x = [1, 2, 3] # list - also valid
```

## Multiple Assignment

```python
# Assign same value to multiple variables
x = y = z = 0

# Multiple assignment (tuple unpacking)
a, b, c = 1, 2, 3

# Swap variables
a, b = b, a

# Unpack sequences
first, second, third = [1, 2, 3]
name, age = ("Alice", 30)

# Extended unpacking (Python 3+)
first, *middle, last = [1, 2, 3, 4, 5]
# first=1, middle=[2,3,4], last=5

# Ignore values with underscore
x, _, z = (1, 2, 3)  # Ignore second value
```

## Type Hints

Optional static type annotations (Python 3.5+):

```python
# Basic type hints
name: str = "Alice"
age: int = 30
price: float = 19.99
is_active: bool = True

# Type hints without initialization
count: int
balance: float

# Function with type hints
def greet(name: str) -> str:
    return f"Hello, {name}!"

# Multiple types (Union)
from typing import Union

def process(value: Union[int, str]) -> str:
    return str(value)

# Optional (can be None)
from typing import Optional

def find_user(id: int) -> Optional[str]:
    if id > 0:
        return "Alice"
    return None

# Modern syntax (Python 3.10+)
def process(value: int | str) -> str:
    return str(value)

def find_user(id: int) -> str | None:
    return "Alice" if id > 0 else None
```

## Type Checking

```python
# Check type at runtime
x = 10
print(type(x))  # <class 'int'>

# Check if instance of type
if isinstance(x, int):
    print("x is an integer")

# Check multiple types
if isinstance(x, (int, float)):
    print("x is a number")

# Static type checking with mypy
# Install: pip install mypy
# Run: mypy script.py
```

## Variable Scope

### Local Variables

```python
def my_function():
    x = 10  # Local variable
    print(x)

my_function()
print(x)  # Error: x is not defined
```

### Global Variables

```python
count = 0  # Global variable

def increment():
    global count  # Declare we're using global variable
    count += 1

increment()
print(count)  # 1

# Without global keyword
def increment_wrong():
    count += 1  # Error: local variable 'count' referenced before assignment

# Reading global variables doesn't need 'global'
def read_global():
    print(count)  # Works fine
```

### Nonlocal Variables

```python
def outer():
    x = 10  # Enclosing scope

    def inner():
        nonlocal x  # Refer to enclosing scope variable
        x += 1
        print(x)  # 11

    inner()
    print(x)  # 11

outer()
```

### LEGB Rule

Python searches for variables in this order:

1. **L**ocal - inside current function
2. **E**nclosing - in enclosing functions
3. **G**lobal - at module level
4. **B**uilt-in - Python built-in names

```python
x = "global"  # Global

def outer():
    x = "enclosing"  # Enclosing

    def inner():
        x = "local"  # Local
        print(x)  # "local"

    inner()
    print(x)  # "enclosing"

outer()
print(x)  # "global"

# Built-in
print(len([1, 2, 3]))  # Built-in function 'len'
```

## Constants

Python has no true constants - use naming convention:

```python
# Constants (convention: UPPER_CASE)
PI = 3.14159
MAX_SIZE = 100
DEFAULT_TIMEOUT = 30
API_KEY = "your-api-key"

# Grouped constants
class Config:
    DEBUG = True
    DATABASE_URL = "postgresql://localhost/mydb"
    MAX_CONNECTIONS = 10

# Usage
if Config.DEBUG:
    print("Debug mode")

# Using Enum for constants
from enum import Enum

class Status(Enum):
    PENDING = "pending"
    APPROVED = "approved"
    REJECTED = "rejected"

# Truly immutable constants (Python 3.8+)
from typing import Final

MAX_SIZE: Final = 100
MAX_SIZE = 200  # Type checker will warn but won't prevent
```

## Variable Naming Rules

```python
# Valid names
name = "Alice"
user_name = "Alice"
userName = "Alice"  # Valid but not Pythonic
_private = "hidden"
__very_private = "very hidden"
age2 = 30
USER_COUNT = 100

# Invalid names
2age = 30        # Cannot start with number
user-name = ""   # Cannot use hyphens
class = ""       # Cannot use reserved keywords
my name = ""     # Cannot have spaces

# Reserved keywords (cannot be used as variable names)
import keyword
print(keyword.kwlist)
# ['False', 'None', 'True', 'and', 'as', 'assert', 'async', 'await',
#  'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except',
#  'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is',
#  'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try',
#  'while', 'with', 'yield']
```

## Naming Conventions (PEP 8)

```python
# Variables and functions: snake_case
user_name = "Alice"
total_count = 100

def calculate_total():
    pass

# Constants: UPPER_SNAKE_CASE
MAX_SIZE = 100
DEFAULT_TIMEOUT = 30

# Classes: PascalCase
class UserAccount:
    pass

class HTTPConnection:
    pass

# Private variables: single underscore prefix
class MyClass:
    def __init__(self):
        self._internal = 10  # Private by convention
        self.__name_mangled = 20  # Name mangling

# Protected (convention in some contexts)
_internal_value = 100

# Special variables: dunder (double underscore)
__name__
__file__
__doc__
```

## Deleting Variables

```python
x = 10
print(x)  # 10

del x
print(x)  # Error: name 'x' is not defined

# Delete multiple variables
a, b, c = 1, 2, 3
del a, b, c
```

## Variable Annotations (PEP 526)

```python
# Variable annotations without assignment
age: int
name: str

# With assignment
age: int = 30
name: str = "Alice"

# Complex types
from typing import List, Dict, Tuple

numbers: List[int] = [1, 2, 3]
user_data: Dict[str, str] = {"name": "Alice"}
coordinates: Tuple[float, float] = (10.5, 20.3)

# Class variable annotations
class User:
    name: str
    age: int

    def __init__(self, name: str, age: int):
        self.name = name
        self.age = age
```

## Mutable vs Immutable

```python
# Immutable types (cannot be changed after creation)
x = 10        # int
x = "hello"   # str
x = (1, 2)    # tuple
x = frozenset([1, 2, 3])  # frozenset

# Mutable types (can be changed in-place)
lst = [1, 2, 3]      # list
lst[0] = 10          # Modify in place

dct = {"a": 1}       # dict
dct["b"] = 2         # Modify in place

s = {1, 2, 3}        # set
s.add(4)             # Modify in place

# Immutable objects can't be modified
text = "hello"
text[0] = "H"  # Error: str object does not support item assignment
```

## Variable References

```python
# Variables are references to objects
a = [1, 2, 3]
b = a  # b references the same list as a

b.append(4)
print(a)  # [1, 2, 3, 4] - both changed!

# Check if same object
print(a is b)  # True

# Create a copy
import copy
c = a.copy()        # Shallow copy
d = copy.deepcopy(a)  # Deep copy

c.append(5)
print(a)  # [1, 2, 3, 4] - not affected
print(c)  # [1, 2, 3, 4, 5]
```

## Memory Management

```python
import sys

# Check reference count
x = [1, 2, 3]
print(sys.getrefcount(x))  # Usually 2 (x + getrefcount parameter)

# Check object ID
a = [1, 2, 3]
b = [1, 2, 3]
print(id(a))  # Memory address
print(id(b))  # Different address

# Small integers are cached
x = 10
y = 10
print(x is y)  # True (same object)

x = 1000
y = 1000
print(x is y)  # False (different objects)
```

> **Good to know:** Python variables are actually references to objects. Assignment doesn't copy values, it creates new references. Use `is` to check object identity and `==` to check value equality. The `global` keyword is needed only when modifying global variables, not when reading them. Use type hints for better code documentation and IDE support, but they're not enforced at runtime. The `_` (underscore) prefix for variables is a convention indicating "private" but doesn't actually enforce privacy.

---

## References

- [Python - Variables](https://docs.python.org/3/tutorial/introduction.html#using-python-as-a-calculator)
- [PEP 8 - Naming Conventions](https://www.python.org/dev/peps/pep-0008/#naming-conventions)
- [PEP 484 - Type Hints](https://www.python.org/dev/peps/pep-0484/)
- [PEP 526 - Variable Annotations](https://www.python.org/dev/peps/pep-0526/)
- [mypy - Static Type Checker](http://mypy-lang.org/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
