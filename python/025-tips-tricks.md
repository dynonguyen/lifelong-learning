# Tips & Tricks

## Python Idioms

### Swapping Variables

```python
# Pythonic way
a, b = b, a

# Works with multiple variables
a, b, c = c, b, a
```

### Chaining Comparisons

```python
# Instead of:
if x > 0 and x < 10:
    pass

# Use:
if 0 < x < 10:
    pass

# Multiple comparisons
if 0 <= x <= 100 and 0 <= y <= 100:
    pass
```

### Conditional Assignment

```python
# Ternary operator
value = "positive" if x > 0 else "negative"

# With multiple conditions
status = "child" if age < 18 else "adult" if age < 65 else "senior"

# Default values
name = user_name or "Guest"
name = user_name if user_name else "Guest"
```

### Unpacking

```python
# Basic unpacking
a, b, c = [1, 2, 3]

# With * operator
first, *middle, last = [1, 2, 3, 4, 5]
# first=1, middle=[2,3,4], last=5

# Ignore values with _
a, _, c = [1, 2, 3]

# Dictionary unpacking
user = {"name": "Alice", "age": 30}
name, age = user["name"], user["age"]

# Or better:
name = user.get("name")
age = user.get("age")

# Function arguments
def func(a, b, c):
    return a + b + c

args = [1, 2, 3]
result = func(*args)

kwargs = {"a": 1, "b": 2, "c": 3}
result = func(**kwargs)
```

### List Operations

```python
# Flatten list
nested = [[1, 2], [3, 4], [5, 6]]
flat = [item for sublist in nested for item in sublist]
# [1, 2, 3, 4, 5, 6]

# Remove duplicates (preserving order)
items = [1, 2, 2, 3, 1, 4]
unique = list(dict.fromkeys(items))

# Reverse list
reversed_list = items[::-1]

# Rotate list
rotated = items[1:] + items[:1]  # Rotate left
rotated = items[-1:] + items[:-1]  # Rotate right

# Zip two lists
names = ["Alice", "Bob"]
ages = [30, 25]
combined = list(zip(names, ages))
# [("Alice", 30), ("Bob", 25)]

# Unzip
names, ages = zip(*combined)
```

### Dictionary Tricks

```python
# Merge dictionaries (Python 3.9+)
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}
merged = dict1 | dict2

# Or (Python 3.5+)
merged = {**dict1, **dict2}

# Invert dictionary
original = {"a": 1, "b": 2, "c": 3}
inverted = {v: k for k, v in original.items()}

# Default value with setdefault
counts = {}
for item in items:
    counts.setdefault(item, 0)
    counts[item] += 1

# Or use defaultdict
from collections import defaultdict
counts = defaultdict(int)
for item in items:
    counts[item] += 1

# Get nested value safely
data = {"user": {"name": "Alice"}}
name = data.get("user", {}).get("name", "Unknown")
```

## Performance Tips

### List Comprehensions vs Loops

```python
# Slow: Append in loop
squares = []
for x in range(1000):
    squares.append(x**2)

# Fast: List comprehension
squares = [x**2 for x in range(1000)]

# Even faster for large data: Generator
squares = (x**2 for x in range(1000))
```

### Set for Membership Testing

```python
# Slow: List membership O(n)
items = [1, 2, 3, 4, 5] * 1000
if 999 in items:
    pass

# Fast: Set membership O(1)
items = set([1, 2, 3, 4, 5] * 1000)
if 999 in items:
    pass
```

### String Building

```python
# Slow: String concatenation in loop
result = ""
for i in range(1000):
    result += str(i)

# Fast: Join
result = "".join(str(i) for i in range(1000))

# Or: List and join
parts = []
for i in range(1000):
    parts.append(str(i))
result = "".join(parts)
```

### Local Variables

```python
# Slow: Global lookups
import math

def calculate():
    for i in range(1000):
        x = math.sqrt(i)

# Fast: Local variable
import math

def calculate():
    sqrt = math.sqrt  # Local reference
    for i in range(1000):
        x = sqrt(i)
```

### Use Built-in Functions

```python
# Slow: Manual sum
total = 0
for x in numbers:
    total += x

# Fast: Built-in sum
total = sum(numbers)

# Slow: Manual max
max_val = numbers[0]
for x in numbers:
    if x > max_val:
        max_val = x

# Fast: Built-in max
max_val = max(numbers)
```

## Debugging Tricks

### Print Debugging

```python
# Quick variable inspection
x = 42
print(f"{x=}")  # x=42

# Multiple variables
a, b = 1, 2
print(f"{a=}, {b=}")  # a=1, b=2

# With expressions
print(f"{len(items)=}")  # len(items)=5
```

### Debugging with breakpoint()

```python
def problematic_function():
    x = calculate_something()
    breakpoint()  # Drops into pdb debugger
    y = process(x)
    return y

# pdb commands:
# n - next line
# s - step into
# c - continue
# p variable - print variable
# l - list code
# q - quit
```

### Better Tracebacks

```python
# Install rich for better tracebacks
from rich.traceback import install
install(show_locals=True)

# Or use:
import traceback

try:
    risky_operation()
except Exception:
    traceback.print_exc()
```

### Assertions for Debugging

```python
def calculate(x):
    assert x > 0, f"x must be positive, got {x}"
    return x ** 2

# Assertions are removed with -O flag
# python -O script.py
```

## Advanced Patterns

### Context Manager for Timing

```python
import time
from contextlib import contextmanager

@contextmanager
def timer(name):
    start = time.perf_counter()
    try:
        yield
    finally:
        elapsed = time.perf_counter() - start
        print(f"{name} took {elapsed:.4f}s")

with timer("Operation"):
    # Timed code
    time.sleep(1)
```

### Singleton Pattern

```python
class Singleton:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance

# Or use decorator
def singleton(cls):
    instances = {}

    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]

    return get_instance

@singleton
class Database:
    pass
```

### Memoization (Caching)

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

# Clear cache
fibonacci.cache_clear()

# Get cache info
print(fibonacci.cache_info())

# Custom cache
def memoize(func):
    cache = {}

    def wrapper(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]

    return wrapper

@memoize
def expensive_function(n):
    return n ** 2
```

### Property Caching

```python
from functools import cached_property

class DataProcessor:
    def __init__(self, data):
        self.data = data

    @cached_property
    def processed(self):
        # Expensive computation
        print("Processing...")
        return [x * 2 for x in self.data]

processor = DataProcessor([1, 2, 3])
print(processor.processed)  # Processing... [2, 4, 6]
print(processor.processed)  # [2, 4, 6] (cached)
```

### Descriptor Protocol

```python
class Validator:
    def __init__(self, min_value=None, max_value=None):
        self.min_value = min_value
        self.max_value = max_value

    def __set_name__(self, owner, name):
        self.name = f"_{name}"

    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        return getattr(obj, self.name)

    def __set__(self, obj, value):
        if self.min_value is not None and value < self.min_value:
            raise ValueError(f"{self.name} must be >= {self.min_value}")
        if self.max_value is not None and value > self.max_value:
            raise ValueError(f"{self.name} must be <= {self.max_value}")
        setattr(obj, self.name, value)

class Person:
    age = Validator(min_value=0, max_value=150)

    def __init__(self, age):
        self.age = age

person = Person(30)
# person.age = -5  # ValueError
```

## Code Organization

### Private Conventions

```python
class MyClass:
    def __init__(self):
        self.public = "public"
        self._internal = "internal use"
        self.__private = "name mangled"

    def public_method(self):
        pass

    def _internal_method(self):
        """Single underscore: internal use."""
        pass

    def __private_method(self):
        """Double underscore: name mangled to _MyClass__private_method."""
        pass
```

### Module-level **all**

```python
# mymodule.py
def public_function():
    pass

def _private_function():
    pass

__all__ = ['public_function']

# Now: from mymodule import *
# Only imports public_function
```

### Package Initialization

```python
# mypackage/__init__.py

# Import commonly used items
from .core import Engine
from .utils import helper

# Define what gets imported with *
__all__ = ['Engine', 'helper']

# Package version
__version__ = "1.0.0"

# Initialization code
print("Package initialized")
```

## Testing Shortcuts

### Fixtures

```python
import pytest

@pytest.fixture
def sample_data():
    data = create_test_data()
    yield data
    cleanup(data)

@pytest.fixture(scope="session")
def database():
    """Created once per test session."""
    db = create_database()
    yield db
    db.close()
```

### Parametrize Tests

```python
import pytest

@pytest.mark.parametrize("input,expected", [
    (1, 2),
    (2, 4),
    (3, 6),
])
def test_double(input, expected):
    assert input * 2 == expected
```

### Mock External Dependencies

```python
from unittest.mock import Mock, patch

@patch('requests.get')
def test_api_call(mock_get):
    mock_get.return_value.json.return_value = {"status": "ok"}

    result = fetch_data()

    assert result["status"] == "ok"
    mock_get.assert_called_once()
```

## One-Liners

```python
# Reverse string
reversed_str = s[::-1]

# Palindrome check
is_palindrome = s == s[::-1]

# Get file extension
ext = filename.split('.')[-1]

# Remove whitespace
clean = ''.join(s.split())

# Title case
title = ' '.join(word.capitalize() for word in s.split())

# Flatten list
flat = sum(nested_list, [])

# Most common item
from collections import Counter
most_common = Counter(items).most_common(1)[0][0]

# Read file lines
lines = open('file.txt').read().splitlines()

# Dictionary from two lists
d = dict(zip(keys, values))

# Remove duplicates preserving order
unique = list(dict.fromkeys(items))

# Transpose matrix
transposed = list(zip(*matrix))

# Filter None values
filtered = [x for x in items if x is not None]

# Chunk list
chunks = [items[i:i+n] for i in range(0, len(items), n)]
```

## Environment-specific Code

```python
import sys
import os

# Check Python version
if sys.version_info >= (3, 10):
    # Use Python 3.10+ features
    pass

# Check platform
if sys.platform == "darwin":
    # macOS specific
    pass
elif sys.platform == "linux":
    # Linux specific
    pass
elif sys.platform == "win32":
    # Windows specific
    pass

# Development vs Production
if os.getenv("ENV") == "production":
    DEBUG = False
else:
    DEBUG = True
```

## Useful Snippets

### Retry Decorator

```python
import time
from functools import wraps

def retry(max_attempts=3, delay=1):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts - 1:
                        raise
                    time.sleep(delay)
        return wrapper
    return decorator

@retry(max_attempts=3, delay=1)
def flaky_function():
    # Might fail
    pass
```

### Rate Limiter

```python
import time
from functools import wraps

def rate_limit(max_calls, period):
    calls = []

    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            now = time.time()
            # Remove old calls
            while calls and calls[0] < now - period:
                calls.pop(0)

            if len(calls) >= max_calls:
                sleep_time = period - (now - calls[0])
                time.sleep(sleep_time)

            calls.append(time.time())
            return func(*args, **kwargs)

        return wrapper
    return decorator

@rate_limit(max_calls=5, period=60)
def api_call():
    pass
```

### Lazy Property

```python
class LazyProperty:
    def __init__(self, func):
        self.func = func

    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        value = self.func(obj)
        setattr(obj, self.func.__name__, value)
        return value

class DataLoader:
    @LazyProperty
    def data(self):
        print("Loading data...")
        return load_expensive_data()

loader = DataLoader()
print(loader.data)  # Loading data...
print(loader.data)  # Cached
```

> **Good to know:** Tuple unpacking with `*` makes code cleaner. Use `any()` and `all()` for iterable checks. List comprehensions are faster than loops. Use sets for O(1) membership testing. `dict.get()` with defaults avoids KeyError. Generator expressions save memory. `enumerate()` is better than manual counters. `zip()` combines iterables elegantly. Context managers (`with`) ensure cleanup. `@lru_cache` adds easy memoization. F-strings with `=` show variable names. `breakpoint()` drops into debugger. Walrus operator `:=` (3.8+) assigns in expressions. `match-case` (3.10+) for pattern matching. Use `__slots__` to reduce memory. Profile before optimizing.

---

## References

- [Python - FAQ](https://docs.python.org/3/faq/)
- [Python - Tips and Tricks](https://realpython.com/python-tips-tricks/)
- [Effective Python - Brett Slatkin](https://effectivepython.com/)
- [Python Patterns](https://python-patterns.guide/)
- [Python - Performance Tips](https://wiki.python.org/moin/PythonSpeed/PerformanceTips)
- [Real Python - Tutorials](https://realpython.com/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
