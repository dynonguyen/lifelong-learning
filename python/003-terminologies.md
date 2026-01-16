# Terminologies

## Core Concepts

### **Interpreter**

Python is an interpreted language - code is executed line by line:

```python
# No compilation step needed
print("Hello")  # Executed immediately
```

### **CPython**

The default and most widely used Python implementation written in C. Other implementations include PyPy (JIT compiler), Jython (Java), and IronPython (.NET).

### **PEP (Python Enhancement Proposal)**

Official design documents providing information or describing new features:

- **PEP 8:** Style Guide for Python Code
- **PEP 20:** The Zen of Python
- **PEP 484:** Type Hints
- **PEP 517/518:** Build system specifications

```python
import this  # Prints The Zen of Python (PEP 20)
```

### **REPL (Read-Eval-Print Loop)**

Interactive shell for executing Python code:

```bash
python3
>>> x = 5
>>> x + 3
8
>>> exit()
```

### **Module**

A single Python file containing functions, classes, and variables:

```python
# math_utils.py
def add(a, b):
    return a + b

PI = 3.14159
```

### **Package**

A directory containing Python modules and an `__init__.py` file:

```
mypackage/
├── __init__.py
├── module1.py
└── module2.py
```

### **Standard Library**

Built-in modules that come with Python (no installation needed):

```python
import os         # Operating system interface
import sys        # System-specific parameters
import json       # JSON encoder/decoder
import datetime   # Date and time
```

### **PyPI (Python Package Index)**

Repository of third-party Python packages:

```bash
pip install requests  # Install from PyPI
```

### **Virtual Environment (venv)**

Isolated Python environment for project dependencies:

```bash
python3 -m venv myenv
source myenv/bin/activate
```

### **pip**

Package installer for Python:

```bash
pip install numpy      # Install package
pip list               # List installed packages
pip freeze             # Show installed packages with versions
```

## Data Types & Structures

### **Mutable vs Immutable**

- **Mutable:** Can be changed after creation (list, dict, set)
- **Immutable:** Cannot be changed after creation (int, str, tuple)

```python
# Immutable
x = "hello"
x[0] = "H"  # Error: str object does not support item assignment

# Mutable
lst = [1, 2, 3]
lst[0] = 10  # OK: [10, 2, 3]
```

### **Duck Typing**

"If it walks like a duck and quacks like a duck, it's a duck" - type is determined by behavior, not inheritance:

```python
def process(item):
    item.save()  # Works with anything that has a save() method

# Both work even if unrelated
class Database:
    def save(self): pass

class File:
    def save(self): pass
```

### **Iterable**

Any object that can return its elements one at a time:

```python
# All iterables
for char in "hello": pass      # str
for num in [1, 2, 3]: pass     # list
for key in {"a": 1}: pass      # dict
for line in open("file.txt"): pass  # file
```

### **Iterator**

An object that implements `__iter__()` and `__next__()`:

```python
nums = iter([1, 2, 3])
print(next(nums))  # 1
print(next(nums))  # 2
```

### **Generator**

A function that yields values lazily using `yield`:

```python
def count_up_to(n):
    i = 1
    while i <= n:
        yield i
        i += 1

for num in count_up_to(5):
    print(num)  # 1, 2, 3, 4, 5
```

### **List Comprehension**

Concise way to create lists:

```python
# Traditional
squares = []
for x in range(10):
    squares.append(x ** 2)

# Comprehension
squares = [x ** 2 for x in range(10)]
```

### **Lambda**

Anonymous, single-expression function:

```python
add = lambda x, y: x + y
print(add(2, 3))  # 5

# Often used with map, filter, sorted
squared = list(map(lambda x: x**2, [1, 2, 3]))
```

## Object-Oriented Programming

### **Class**

Blueprint for creating objects:

```python
class Dog:
    def __init__(self, name):
        self.name = name

    def bark(self):
        print(f"{self.name} barks!")
```

### **Instance**

A specific object created from a class:

```python
my_dog = Dog("Buddy")  # Instance of Dog
```

### **self**

Reference to the current instance:

```python
class Counter:
    def __init__(self):
        self.count = 0  # Instance variable

    def increment(self):
        self.count += 1  # Access via self
```

### **Dunder Methods (Magic Methods)**

Special methods with double underscores (`__method__`):

```python
class Point:
    def __init__(self, x, y):      # Constructor
        self.x, self.y = x, y

    def __str__(self):             # String representation
        return f"Point({self.x}, {self.y})"

    def __add__(self, other):      # + operator
        return Point(self.x + other.x, self.y + other.y)
```

### **Property**

Getter/setter methods as attributes:

```python
class Temperature:
    def __init__(self, celsius):
        self._celsius = celsius

    @property
    def fahrenheit(self):
        return self._celsius * 9/5 + 32

    @fahrenheit.setter
    def fahrenheit(self, value):
        self._celsius = (value - 32) * 5/9

temp = Temperature(25)
print(temp.fahrenheit)  # 77.0
```

### **Decorator**

Function that modifies another function:

```python
def uppercase(func):
    def wrapper():
        return func().upper()
    return wrapper

@uppercase
def greet():
    return "hello"

print(greet())  # "HELLO"
```

## Advanced Concepts

### **GIL (Global Interpreter Lock)**

Mutex that protects access to Python objects, preventing true multi-threading:

```python
# CPU-bound tasks won't run in parallel due to GIL
# Use multiprocessing for parallelism
from multiprocessing import Pool
```

### **Context Manager**

Object that defines `__enter__()` and `__exit__()` for `with` statement:

```python
with open("file.txt") as f:
    data = f.read()
# File automatically closed
```

### **Coroutine**

Function that can pause and resume execution:

```python
async def fetch_data():
    await asyncio.sleep(1)
    return "data"
```

### **Type Hints**

Optional static type annotations (PEP 484):

```python
def greet(name: str) -> str:
    return f"Hello, {name}"

from typing import List, Dict, Optional

def process(items: List[int]) -> Optional[Dict[str, int]]:
    if not items:
        return None
    return {"sum": sum(items)}
```

### **Protocol**

Structural subtyping (duck typing with types):

```python
from typing import Protocol

class Drawable(Protocol):
    def draw(self) -> None: ...

def render(obj: Drawable):  # Works with any object that has draw()
    obj.draw()
```

### **EAFP (Easier to Ask for Forgiveness than Permission)**

Python's preferred coding style - try and handle exceptions:

```python
# EAFP (Pythonic)
try:
    value = mydict["key"]
except KeyError:
    value = None

# LBYL (Look Before You Leap) - less Pythonic
if "key" in mydict:
    value = mydict["key"]
else:
    value = None
```

### **Monkey Patching**

Dynamically modifying a class or module at runtime:

```python
class Dog:
    def bark(self):
        return "Woof!"

# Monkey patch
def new_bark(self):
    return "Meow!"

Dog.bark = new_bark

dog = Dog()
print(dog.bark())  # "Meow!"
```

> **Good to know:** Python follows the "EAFP" principle - it's better to try an operation and handle exceptions than to check conditions beforehand. The `__name__` variable is set to `"__main__"` when a script is run directly. Understanding the difference between `is` (identity) and `==` (equality) is crucial: `is` checks if two variables point to the same object, while `==` checks if the values are equal.

---

## References

- [Python Glossary](https://docs.python.org/3/glossary.html)
- [PEP 0 - Index of Python Enhancement Proposals](https://www.python.org/dev/peps/)
- [Python Language Reference](https://docs.python.org/3/reference/)
- [The Hitchhiker's Guide to Python - Code Style](https://docs.python-guide.org/writing/style/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
