# Enum/Tuple

## Enumerations (Enum)

### Basic Enum

```python
from enum import Enum

class Color(Enum):
    RED = 1
    GREEN = 2
    BLUE = 3

# Access members
print(Color.RED)        # Color.RED
print(Color.RED.name)   # 'RED'
print(Color.RED.value)  # 1

# Comparison
if Color.RED == Color.RED:
    print("Same color")

# Iterate
for color in Color:
    print(color)

# From value
color = Color(2)
print(color)  # Color.GREEN
```

### Auto Values

```python
from enum import Enum, auto

class Status(Enum):
    PENDING = auto()    # 1
    APPROVED = auto()   # 2
    REJECTED = auto()   # 3

print(Status.PENDING.value)  # 1
```

### String Enums

```python
from enum import Enum

class Color(str, Enum):
    RED = "red"
    GREEN = "green"
    BLUE = "blue"

# Works like string
print(f"My favorite color is {Color.RED}")
# My favorite color is red

# JSON serializable
import json
print(json.dumps({"color": Color.RED}))
# {"color": "red"}
```

### IntEnum

```python
from enum import IntEnum

class Priority(IntEnum):
    LOW = 1
    MEDIUM = 2
    HIGH = 3

# Can be compared with integers
if Priority.HIGH > 2:
    print("High priority")  # Prints

# Can be used in arithmetic
print(Priority.MEDIUM + 1)  # 3
```

### Flag Enum

For bitwise operations:

```python
from enum import Flag, auto

class Permission(Flag):
    READ = auto()    # 1
    WRITE = auto()   # 2
    EXECUTE = auto() # 4

# Combine permissions
user_perms = Permission.READ | Permission.WRITE
print(user_perms)  # Permission.READ|WRITE

# Check permissions
if Permission.READ in user_perms:
    print("Can read")

if user_perms & Permission.WRITE:
    print("Can write")
```

### Enum Methods

```python
from enum import Enum

class Status(Enum):
    DRAFT = "draft"
    PUBLISHED = "published"
    ARCHIVED = "archived"

    @classmethod
    def from_string(cls, value: str):
        """Get enum from string."""
        try:
            return cls[value.upper()]
        except KeyError:
            raise ValueError(f"Invalid status: {value}")

    def is_published(self):
        """Check if status is published."""
        return self == Status.PUBLISHED

status = Status.from_string("draft")
print(status)  # Status.DRAFT
print(Status.PUBLISHED.is_published())  # True
```

### Unique Values

```python
from enum import Enum, unique

# Ensure all values are unique
@unique
class Color(Enum):
    RED = 1
    GREEN = 2
    # BLUE = 1  # Error: duplicate value
```

## Tuples

### Creating Tuples

```python
# Empty tuple
empty = ()
empty = tuple()

# Single element (comma required!)
single = (1,)
single = 1,

# Multiple elements
point = (10, 20)
person = ("Alice", 30, "NYC")

# Without parentheses (tuple packing)
coords = 10, 20, 30

# From iterable
numbers = tuple([1, 2, 3, 4, 5])
chars = tuple("hello")  # ('h', 'e', 'l', 'l', 'o')
```

### Accessing Tuples

```python
point = (10, 20, 30)

# Indexing
print(point[0])   # 10
print(point[-1])  # 30

# Slicing
print(point[1:])  # (20, 30)

# Unpacking
x, y, z = point

# Unpacking with rest
first, *rest = (1, 2, 3, 4, 5)
# first=1, rest=[2,3,4,5]

first, *middle, last = (1, 2, 3, 4, 5)
# first=1, middle=[2,3,4], last=5
```

### Tuple Operations

```python
# Concatenation
t1 = (1, 2, 3)
t2 = (4, 5, 6)
combined = t1 + t2  # (1, 2, 3, 4, 5, 6)

# Repetition
repeated = (1, 2) * 3  # (1, 2, 1, 2, 1, 2)

# Membership
print(2 in (1, 2, 3))  # True

# Length
print(len((1, 2, 3)))  # 3

# Count
print((1, 2, 2, 3).count(2))  # 2

# Index
print((1, 2, 3).index(2))  # 1

# Immutable
t = (1, 2, 3)
# t[0] = 10  # Error: tuple object does not support item assignment
```

### Named Tuples

Tuples with named fields:

```python
from collections import namedtuple

# Define named tuple
Point = namedtuple('Point', ['x', 'y'])
Person = namedtuple('Person', 'name age email')

# Create instances
p = Point(10, 20)
person = Person("Alice", 30, "alice@example.com")

# Access by name or index
print(p.x, p.y)     # 10 20
print(p[0], p[1])   # 10 20

# Unpacking works
x, y = p

# Immutable
# p.x = 15  # Error!

# Convert to dict
print(p._asdict())  # {'x': 10, 'y': 20}

# Create from dict
data = {'x': 30, 'y': 40}
p2 = Point(**data)

# Replace values (creates new tuple)
p3 = p._replace(x=15)  # Point(x=15, y=20)

# With defaults (Python 3.7+)
Person = namedtuple('Person', ['name', 'age'], defaults=['Unknown', 0])
p = Person()  # Person(name='Unknown', age=0)
```

### Typing Named Tuples

```python
from typing import NamedTuple

class Point(NamedTuple):
    x: float
    y: float

    def distance_from_origin(self) -> float:
        return (self.x ** 2 + self.y ** 2) ** 0.5

class Person(NamedTuple):
    name: str
    age: int
    email: str = "unknown@example.com"  # Default value

p = Point(3.0, 4.0)
print(p.distance_from_origin())  # 5.0

person = Person("Alice", 30)
print(person)  # Person(name='Alice', age=30, email='unknown@example.com')
```

### Tuples vs Lists

```python
# Tuples - immutable, hashable, faster
coordinates = (10, 20)
point_dict = {coordinates: "Origin"}  # Can be dict key

# Lists - mutable, not hashable, more features
numbers = [1, 2, 3]
numbers.append(4)
# {numbers: "value"}  # Error: unhashable type: 'list'

# Memory comparison
import sys
t = (1, 2, 3)
l = [1, 2, 3]
print(sys.getsizeof(t))  # 64 bytes
print(sys.getsizeof(l))  # 88 bytes
```

## Use Cases

### Enum Use Cases

```python
from enum import Enum

# HTTP Status Codes
class HTTPStatus(Enum):
    OK = 200
    NOT_FOUND = 404
    SERVER_ERROR = 500

# Configuration
class Environment(Enum):
    DEVELOPMENT = "dev"
    STAGING = "staging"
    PRODUCTION = "prod"

# State Machine
class OrderStatus(Enum):
    PENDING = "pending"
    PROCESSING = "processing"
    SHIPPED = "shipped"
    DELIVERED = "delivered"
    CANCELLED = "cancelled"
```

### Tuple Use Cases

```python
# Return multiple values
def get_user():
    return ("Alice", 30, "alice@example.com")

name, age, email = get_user()

# Immutable records
COLORS = (
    (255, 0, 0),    # Red
    (0, 255, 0),    # Green
    (0, 0, 255),    # Blue
)

# Dictionary keys
locations = {
    (40.7128, -74.0060): "New York",
    (51.5074, -0.1278): "London",
}

# Swapping
a, b = b, a

# Function arguments
point = (10, 20)
def draw(x, y):
    pass

draw(*point)  # Unpack tuple
```

> **Good to know:** Enums provide type-safe constants with better IDE support than plain strings/integers. Use `auto()` to avoid hardcoding values. String enums are JSON-serializable. Flag enums support bitwise operations for combinations. Named tuples are lightweight alternatives to classes for simple data structures. Tuples are immutable and hashable, making them suitable for dictionary keys. Named tuples with `typing.NamedTuple` support type hints and methods. Tuples use less memory than lists and have faster iteration.

---

## References

- [Python - Enum](https://docs.python.org/3/library/enum.html)
- [Python - Collections - Named Tuple](https://docs.python.org/3/library/collections.html#collections.namedtuple)
- [Python - Tuples](https://docs.python.org/3/tutorial/datastructures.html#tuples-and-sequences)
- [PEP 435 - Adding an Enum type to the Python standard library](https://www.python.org/dev/peps/pep-0435/)
- [typing.NamedTuple](https://docs.python.org/3/library/typing.html#typing.NamedTuple)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
