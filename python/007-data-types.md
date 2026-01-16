# Data Types

## Primitive Types

### Integers (int)

Arbitrary precision integers:

```python
# Integers
x = 10
y = -5
z = 0

# Large integers (no overflow)
big = 12345678901234567890

# Different bases
binary = 0b1010      # Binary: 10
octal = 0o12         # Octal: 10
hexadecimal = 0xA    # Hexadecimal: 10

# Underscores for readability (Python 3.6+)
million = 1_000_000
```

### Floating Point (float)

IEEE 754 double-precision:

```python
# Floats
pi = 3.14159
negative = -2.5
scientific = 1.5e-4  # 0.00015

# Float precision issues
print(0.1 + 0.2)  # 0.30000000000000004

# Use Decimal for precision
from decimal import Decimal
x = Decimal('0.1')
y = Decimal('0.2')
print(x + y)  # 0.3
```

### Complex Numbers (complex)

```python
# Complex numbers
z = 3 + 4j
w = complex(3, 4)

# Operations
print(z.real)   # 3.0
print(z.imag)   # 4.0
print(abs(z))   # 5.0 (magnitude)
print(z.conjugate())  # (3-4j)
```

### Boolean (bool)

```python
# Boolean values
is_active = True
is_deleted = False

# Boolean operations
result = True and False  # False
result = True or False   # True
result = not True        # False

# Truthy and Falsy values
# Falsy: False, None, 0, 0.0, 0j, '', [], (), {}, set()
# Everything else is Truthy

if []:  # Empty list is falsy
    print("Won't print")

if [1]:  # Non-empty list is truthy
    print("Will print")

# Convert to boolean
bool(0)        # False
bool(1)        # True
bool("")       # False
bool("text")   # True
```

### None Type

```python
# None - represents absence of value
x = None

# Check for None
if x is None:
    print("x is None")

# Function returns None by default
def do_nothing():
    pass

result = do_nothing()
print(result)  # None
```

## String (str)

Immutable sequence of Unicode characters:

```python
# String literals
single = 'Hello'
double = "World"
multiline = """This is
a multiline
string"""

# Raw strings (ignore escape sequences)
path = r"C:\Users\name"

# f-strings (formatted strings)
name = "Alice"
age = 30
text = f"My name is {name} and I'm {age}"

# String operations
s = "hello"
print(s[0])        # 'h'
print(s[-1])       # 'o'
print(s[1:4])      # 'ell'
print(s.upper())   # 'HELLO'
print(s.split())   # ['hello']
print(len(s))      # 5

# Immutable
# s[0] = 'H'  # Error!
```

## Collection Types

### List

Mutable ordered sequence:

```python
# Create lists
numbers = [1, 2, 3, 4, 5]
mixed = [1, "hello", 3.14, True]
empty = []
nested = [[1, 2], [3, 4]]

# List operations
numbers.append(6)         # Add to end
numbers.insert(0, 0)      # Insert at index
numbers.extend([7, 8])    # Add multiple
numbers.remove(3)         # Remove value
popped = numbers.pop()    # Remove and return last
numbers[0] = 10          # Modify

# Slicing
print(numbers[1:4])    # [2, 3, 4]
print(numbers[:3])     # First 3
print(numbers[-2:])    # Last 2
print(numbers[::2])    # Every other

# List comprehension
squares = [x**2 for x in range(10)]
evens = [x for x in range(10) if x % 2 == 0]
```

### Tuple

Immutable ordered sequence:

```python
# Create tuples
point = (10, 20)
single = (1,)  # Comma needed for single element
empty = ()

# Tuple unpacking
x, y = point

# Operations (immutable, so limited)
print(point[0])     # 10
print(len(point))   # 2
print(point + (30,))  # (10, 20, 30)

# Named tuples
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
p = Point(10, 20)
print(p.x, p.y)  # 10 20
```

### Dictionary (dict)

Mutable key-value mapping:

```python
# Create dictionaries
user = {"name": "Alice", "age": 30}
empty = {}
from_pairs = dict([("a", 1), ("b", 2)])

# Operations
print(user["name"])        # "Alice"
print(user.get("age"))     # 30
print(user.get("city", "Unknown"))  # "Unknown" (default)

user["email"] = "alice@example.com"  # Add
user["age"] = 31                     # Update
del user["age"]                      # Delete

# Dictionary methods
print(user.keys())    # dict_keys(['name', 'email'])
print(user.values())  # dict_values(['Alice', 'alice@example.com'])
print(user.items())   # dict_items([('name', 'Alice'), ...])

# Dict comprehension
squares = {x: x**2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

### Set

Mutable unordered collection of unique elements:

```python
# Create sets
numbers = {1, 2, 3, 4, 5}
empty = set()  # {} creates empty dict!
from_list = set([1, 2, 2, 3])  # {1, 2, 3}

# Operations
numbers.add(6)          # Add element
numbers.remove(3)       # Remove (raises error if not found)
numbers.discard(3)      # Remove (no error if not found)
numbers.pop()           # Remove and return arbitrary element

# Set operations
a = {1, 2, 3}
b = {3, 4, 5}

print(a | b)      # Union: {1, 2, 3, 4, 5}
print(a & b)      # Intersection: {3}
print(a - b)      # Difference: {1, 2}
print(a ^ b)      # Symmetric difference: {1, 2, 4, 5}

# Set comprehension
evens = {x for x in range(10) if x % 2 == 0}
```

### Frozenset

Immutable set:

```python
# Create frozenset
immutable = frozenset([1, 2, 3])

# Can be used as dict key or in another set
d = {immutable: "value"}
s = {frozenset([1, 2]), frozenset([3, 4])}
```

## Type Conversion

```python
# To int
int("123")      # 123
int(3.14)       # 3
int("1010", 2)  # 10 (binary)
int("FF", 16)   # 255 (hexadecimal)

# To float
float("3.14")   # 3.14
float(10)       # 10.0

# To str
str(123)        # "123"
str(3.14)       # "3.14"

# To list
list("hello")        # ['h', 'e', 'l', 'l', 'o']
list((1, 2, 3))      # [1, 2, 3]
list(range(5))       # [0, 1, 2, 3, 4]

# To tuple
tuple([1, 2, 3])     # (1, 2, 3)

# To set
set([1, 2, 2, 3])    # {1, 2, 3}

# To dict
dict([("a", 1), ("b", 2)])  # {'a': 1, 'b': 2}
```

## Type Checking

```python
# type() function
x = 10
print(type(x))  # <class 'int'>

# isinstance() function
print(isinstance(x, int))        # True
print(isinstance(x, (int, float)))  # True

# Check for multiple types
value = "hello"
if isinstance(value, (str, bytes)):
    print("It's text")
```

## Type Hints

```python
from typing import List, Dict, Tuple, Set, Optional, Union, Any

# Collection types
numbers: List[int] = [1, 2, 3]
user: Dict[str, str] = {"name": "Alice"}
coords: Tuple[float, float] = (10.5, 20.3)
unique: Set[int] = {1, 2, 3}

# Optional (can be None)
name: Optional[str] = None  # same as Union[str, None]

# Union (multiple types)
value: Union[int, str] = 10

# Any (any type)
data: Any = "anything"

# Python 3.10+ syntax
value: int | str = 10
name: str | None = None
```

## Special Types

### bytes

Immutable sequence of bytes:

```python
# Create bytes
b = b"hello"
b = bytes([72, 101, 108, 108, 111])
b = "hello".encode("utf-8")

# Operations
print(b[0])       # 104 (integer)
print(len(b))     # 5
```

### bytearray

Mutable sequence of bytes:

```python
# Create bytearray
ba = bytearray(b"hello")
ba[0] = 72  # Modify
```

### range

Immutable sequence of numbers:

```python
# Create range
r = range(10)           # 0 to 9
r = range(1, 11)        # 1 to 10
r = range(0, 10, 2)     # 0, 2, 4, 6, 8

# Convert to list
print(list(range(5)))   # [0, 1, 2, 3, 4]
```

## Memory and Performance

```python
import sys

# Check object size
x = 10
print(sys.getsizeof(x))  # Bytes in memory

# Integers
small = 10
large = 10**100
print(sys.getsizeof(small))  # 28 bytes
print(sys.getsizeof(large))  # 64 bytes

# Lists vs Tuples
lst = [1, 2, 3]
tpl = (1, 2, 3)
print(sys.getsizeof(lst))  # 88 bytes
print(sys.getsizeof(tpl))  # 64 bytes
```

> **Good to know:** Python integers have arbitrary precision - no integer overflow. Strings are immutable, so operations like concatenation in loops create new strings (use `join()` instead). Dictionary lookup is O(1) average case. Lists are dynamic arrays with O(1) append and O(n) insert. Use tuples for fixed collections and when you need hashability. Sets provide O(1) membership testing. For financial calculations, use `decimal.Decimal` instead of `float`. The `isinstance()` function is preferred over `type()` for type checking because it respects inheritance.

---

## References

- [Python - Built-in Types](https://docs.python.org/3/library/stdtypes.html)
- [Python - Data Structures](https://docs.python.org/3/tutorial/datastructures.html)
- [typing - Support for type hints](https://docs.python.org/3/library/typing.html)
- [Real Python - Basic Data Types](https://realpython.com/python-data-types/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
