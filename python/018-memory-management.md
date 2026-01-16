# Memory Management

## Python Memory Model

### Memory Architecture

```python
# Python uses:
# 1. Private heap for all objects and data structures
# 2. Reference counting for memory management
# 3. Garbage collector for cyclic references

import sys

# Object size in bytes
x = 42
print(sys.getsizeof(x))  # 28 bytes (on 64-bit Python)

# Different types have different sizes
print(sys.getsizeof(True))        # 28 bytes
print(sys.getsizeof(1))           # 28 bytes
print(sys.getsizeof(1.0))         # 24 bytes
print(sys.getsizeof(""))          # 49 bytes
print(sys.getsizeof("hello"))     # 54 bytes
print(sys.getsizeof([]))          # 56 bytes
print(sys.getsizeof([1, 2, 3]))   # 80 bytes
print(sys.getsizeof({}))          # 232 bytes
```

### Object Identity and References

```python
# id() returns object's memory address
x = 42
print(id(x))  # e.g., 140234567890

# is - checks identity (same object)
# == - checks value equality
a = [1, 2, 3]
b = [1, 2, 3]
c = a

print(a == b)  # True (same values)
print(a is b)  # False (different objects)
print(a is c)  # True (same object)

print(id(a))   # e.g., 140234567891
print(id(b))   # e.g., 140234567892
print(id(c))   # e.g., 140234567891 (same as a)
```

### Small Integer Caching

```python
# Python caches small integers (-5 to 256)
a = 42
b = 42
print(a is b)  # True (same cached object)

a = 1000
b = 1000
print(a is b)  # False (different objects)

# String interning (for small strings)
s1 = "hello"
s2 = "hello"
print(s1 is s2)  # True (interned)

s1 = "hello world with spaces"
s2 = "hello world with spaces"
print(s1 is s2)  # False (not interned)
```

## Reference Counting

### How Reference Counting Works

```python
import sys

# Get reference count
x = []
print(sys.getrefcount(x))  # 2 (x + temp reference in getrefcount)

# Creating references
y = x
print(sys.getrefcount(x))  # 3

z = x
print(sys.getrefcount(x))  # 4

# Removing references
del y
print(sys.getrefcount(x))  # 3

del z
print(sys.getrefcount(x))  # 2

# When refcount reaches 0, memory is freed immediately
```

### Reference Count Examples

```python
import sys

# Function parameters increase refcount
def process(obj):
    print(sys.getrefcount(obj))  # +1 for parameter

data = []
print(sys.getrefcount(data))  # 2
process(data)                  # 3 during call
print(sys.getrefcount(data))  # 2 after call

# Collections hold references
a = []
b = [a]  # b holds reference to a
print(sys.getrefcount(a))  # 3 (a, b[0], getrefcount temp)

# Dictionary keys hold references
obj = object()
d = {obj: "value"}
print(sys.getrefcount(obj))  # 3 (obj, key, getrefcount temp)
```

### Circular References

```python
import sys

# Create circular reference
class Node:
    def __init__(self):
        self.ref = None

a = Node()
b = Node()
a.ref = b
b.ref = a  # Circular reference

# Reference counts won't reach 0 even if we del a and b
# Garbage collector handles this

print(sys.getrefcount(a))  # References exist
del a, b  # Circular references remain in memory until GC runs
```

## Garbage Collection

### Manual Garbage Collection

```python
import gc

# Garbage collection is automatic
# But can be controlled manually

# Disable automatic GC
gc.disable()

# Enable automatic GC
gc.enable()

# Check if GC is enabled
print(gc.isenabled())  # True

# Force garbage collection
gc.collect()  # Returns number of objects collected

# Get GC statistics
print(gc.get_count())    # (threshold1, threshold2, threshold3)
print(gc.get_stats())    # Collection stats

# Get all tracked objects
objects = gc.get_objects()
print(f"Tracked objects: {len(objects)}")
```

### Garbage Collection Generations

```python
import gc

# Python uses generational GC (3 generations)
# Generation 0: Youngest objects
# Generation 1: Middle-aged objects
# Generation 2: Oldest objects

# Get generation thresholds
print(gc.get_threshold())  # (700, 10, 10)
# Meaning: Collect gen0 every 700 allocations
#          Collect gen1 every 10 gen0 collections
#          Collect gen2 every 10 gen1 collections

# Set custom thresholds
gc.set_threshold(800, 15, 15)

# Get collection counts
print(gc.get_count())  # (collections since last, ...)

# Collect specific generation
gc.collect(0)  # Collect generation 0
gc.collect(1)  # Collect generation 1
gc.collect(2)  # Collect all generations
```

### Tracking Objects

```python
import gc
import weakref

# Track specific object
obj = []
gc.collect()  # Clean up first

# Get objects referring to obj
referrers = gc.get_referrers(obj)
print(f"Objects referring to obj: {len(referrers)}")

# Get objects referred by obj
referents = gc.get_referents(obj)
print(f"Objects referred by obj: {len(referents)}")

# Check if object is tracked by GC
print(gc.is_tracked(obj))  # True for containers

# Immutable objects aren't tracked
num = 42
print(gc.is_tracked(num))  # False
```

### Weak References

```python
import weakref

# Weak references don't prevent garbage collection

class Data:
    def __init__(self, value):
        self.value = value

    def __del__(self):
        print(f"Data({self.value}) deleted")

# Strong reference
obj = Data(42)

# Weak reference
weak_ref = weakref.ref(obj)

print(weak_ref())  # <Data object>

# Delete strong reference
del obj  # "Data(42) deleted" printed

print(weak_ref())  # None (object was collected)

# WeakValueDictionary - values are weak references
cache = weakref.WeakValueDictionary()

obj = Data(100)
cache['key'] = obj

print(cache['key'])  # <Data object>

del obj  # Object is deleted
# print(cache['key'])  # KeyError (object was collected)
```

## Memory Optimization

### Slots

```python
# __slots__ reduces memory usage for instances

class PointWithDict:
    """Regular class with __dict__."""
    def __init__(self, x, y):
        self.x = x
        self.y = y

class PointWithSlots:
    """Class with __slots__."""
    __slots__ = ['x', 'y']

    def __init__(self, x, y):
        self.x = x
        self.y = y

import sys

# Compare memory usage
p1 = PointWithDict(1, 2)
p2 = PointWithSlots(1, 2)

print(sys.getsizeof(p1) + sys.getsizeof(p1.__dict__))  # ~152 bytes
print(sys.getsizeof(p2))  # ~48 bytes

# __slots__ prevents arbitrary attributes
# p2.z = 3  # AttributeError

# But allows defined attributes
p2.x = 10  # OK
```

### Generators and Iterators

```python
import sys

# Lists store all items in memory
list_data = [x for x in range(1000000)]
print(f"List size: {sys.getsizeof(list_data)} bytes")

# Generators compute items on-demand
gen_data = (x for x in range(1000000))
print(f"Generator size: {sys.getsizeof(gen_data)} bytes")

# Use generators for large sequences
def read_large_file(filename):
    """Memory-efficient file reading."""
    with open(filename) as f:
        for line in f:  # Doesn't load entire file
            yield line.strip()

# Iterator protocol
class Counter:
    def __init__(self, max_count):
        self.max_count = max_count
        self.count = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.count >= self.max_count:
            raise StopIteration
        self.count += 1
        return self.count

# Use iterator instead of list
for i in Counter(1000000):
    if i > 10:
        break  # Can stop early without creating all items
```

### Memory Views

```python
# Memory views provide zero-copy access to buffers

# Regular slicing copies data
data = bytearray(b"Hello World")
slice_copy = data[0:5]  # Creates copy

# Memory view - no copy
data = bytearray(b"Hello World")
view = memoryview(data)
slice_view = view[0:5]  # No copy, just a view

# Modify through view
view[0] = ord('J')
print(data)  # bytearray(b'Jello World')

# Memory views for numpy-like operations
import array

numbers = array.array('i', [1, 2, 3, 4, 5])
view = memoryview(numbers)

# Cast view to different type
byte_view = view.cast('B')  # View as bytes
print(list(byte_view))
```

### Interning Strings

```python
import sys

# Manual string interning
s1 = "hello" * 100
s2 = "hello" * 100

print(s1 is s2)  # False (not automatically interned)

# Intern strings manually
s1 = sys.intern("hello" * 100)
s2 = sys.intern("hello" * 100)

print(s1 is s2)  # True (same interned object)

# Useful for strings used as dict keys repeatedly
# Saves memory and speeds up comparisons
```

## Memory Profiling

### Using tracemalloc

```python
import tracemalloc

# Start tracing memory allocations
tracemalloc.start()

# Your code here
data = [x for x in range(1000000)]

# Take snapshot
snapshot = tracemalloc.take_snapshot()

# Display top memory consumers
top_stats = snapshot.statistics('lineno')

print("[ Top 10 ]")
for stat in top_stats[:10]:
    print(stat)

# Get current memory usage
current, peak = tracemalloc.get_traced_memory()
print(f"Current: {current / 1024 / 1024:.2f} MB")
print(f"Peak: {peak / 1024 / 1024:.2f} MB")

# Stop tracing
tracemalloc.stop()
```

### Comparing Snapshots

```python
import tracemalloc

tracemalloc.start()

# Snapshot 1
snapshot1 = tracemalloc.take_snapshot()

# Allocate memory
data = [x for x in range(1000000)]

# Snapshot 2
snapshot2 = tracemalloc.take_snapshot()

# Compare snapshots
top_stats = snapshot2.compare_to(snapshot1, 'lineno')

print("[ Top 10 Differences ]")
for stat in top_stats[:10]:
    print(stat)
```

### Memory Profiler Tool

```bash
# Install memory_profiler
pip install memory_profiler

# Profile function with decorator
```

```python
from memory_profiler import profile

@profile
def memory_intensive_function():
    a = [1] * (10 ** 6)
    b = [2] * (2 * 10 ** 7)
    del b
    return a

# Run with:
# python -m memory_profiler script.py
```

## Best Practices

### Avoid Memory Leaks

```python
# 1. Break circular references
class Node:
    def __init__(self):
        self.children = []
        self.parent = None

    def add_child(self, child):
        self.children.append(child)
        child.parent = self  # Creates cycle

# Solution: Use weak references
import weakref

class Node:
    def __init__(self):
        self.children = []
        self._parent = None

    @property
    def parent(self):
        return self._parent() if self._parent else None

    @parent.setter
    def parent(self, value):
        self._parent = weakref.ref(value) if value else None

# 2. Close resources properly
# Bad
f = open("file.txt")
data = f.read()
# File might not be closed if exception occurs

# Good
with open("file.txt") as f:
    data = f.read()
# File automatically closed

# 3. Clear large collections when done
large_list = [x for x in range(10**7)]
# Use the list...
large_list.clear()  # Or del large_list
```

### Memory-Efficient Data Structures

```python
# Use appropriate data structures

# Bad: List for lookups
items = [1, 2, 3, 4, 5] * 1000
x in items  # O(n) lookup

# Good: Set for lookups
items = set([1, 2, 3, 4, 5] * 1000)
x in items  # O(1) lookup

# Use array for homogeneous numeric data
import array

# Bad: List of integers
numbers = [1, 2, 3, 4, 5] * 1000000

# Good: array (less memory)
numbers = array.array('i', [1, 2, 3, 4, 5] * 1000000)

# Use namedtuple instead of class for simple data
from collections import namedtuple

# Bad
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

# Good (less memory)
Point = namedtuple('Point', ['x', 'y'])

# Use dataclass with slots
from dataclasses import dataclass

@dataclass(slots=True)
class Point:
    x: int
    y: int
```

### Context Managers for Cleanup

```python
from contextlib import contextmanager

@contextmanager
def managed_resource():
    """Ensure resource cleanup."""
    resource = acquire_resource()
    try:
        yield resource
    finally:
        release_resource(resource)

# Usage
with managed_resource() as r:
    use_resource(r)
# Resource automatically released
```

## Debugging Memory Issues

```python
import gc
import sys

# Find objects that weren't collected
def find_memory_leaks():
    gc.collect()

    # Get all objects
    objects = gc.get_objects()

    # Count by type
    type_counts = {}
    for obj in objects:
        obj_type = type(obj).__name__
        type_counts[obj_type] = type_counts.get(obj_type, 0) + 1

    # Sort by count
    sorted_types = sorted(type_counts.items(),
                         key=lambda x: x[1],
                         reverse=True)

    print("Top 10 object types:")
    for obj_type, count in sorted_types[:10]:
        print(f"{obj_type}: {count}")

# Find large objects
def find_large_objects(limit=10):
    objects = gc.get_objects()

    # Get size of each object
    sizes = [(sys.getsizeof(obj), type(obj).__name__, obj)
             for obj in objects]

    # Sort by size
    sizes.sort(reverse=True)

    print(f"Top {limit} largest objects:")
    for size, obj_type, obj in sizes[:limit]:
        print(f"{obj_type}: {size:,} bytes")

find_memory_leaks()
find_large_objects()
```

> **Good to know:** Python uses automatic memory management with reference counting and garbage collection. Small integers (-5 to 256) are cached and reused. Reference counting handles most deallocations immediately. The garbage collector handles circular references. Python uses generational garbage collection with 3 generations. Use `__slots__` to reduce instance memory by 60-70%. Weak references don't prevent garbage collection. Generators are memory-efficient for large sequences. Memory views provide zero-copy buffer access. `tracemalloc` helps profile memory usage. String interning saves memory for repeated strings. Close resources properly to avoid leaks. Use appropriate data structures for efficiency. The `gc` module provides manual control over garbage collection.

---

## References

- [Python - Memory Management](https://docs.python.org/3/c-api/memory.html)
- [Python - gc Module](https://docs.python.org/3/library/gc.html)
- [Python - weakref Module](https://docs.python.org/3/library/weakref.html)
- [Python - tracemalloc](https://docs.python.org/3/library/tracemalloc.html)
- [Real Python - Memory Management](https://realpython.com/python-memory-management/)
- [PEP 412 - Key-Sharing Dictionary](https://www.python.org/dev/peps/pep-0412/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
