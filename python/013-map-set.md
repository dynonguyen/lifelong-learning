# Map/HashMap/Dictionary/Set

## Dictionaries (dict)

### Creating Dictionaries

```python
# Empty dictionary
empty = {}
empty = dict()

# With initial values
user = {"name": "Alice", "age": 30, "city": "NYC"}

# dict() constructor
user = dict(name="Alice", age=30, city="NYC")

# From list of tuples
pairs = [("a", 1), ("b", 2), ("c", 3)]
d = dict(pairs)  # {'a': 1, 'b': 2, 'c': 3}

# From two lists
keys = ["name", "age", "city"]
values = ["Alice", 30, "NYC"]
user = dict(zip(keys, values))

# Dictionary comprehension
squares = {x: x**2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# With conditions
even_squares = {x: x**2 for x in range(10) if x % 2 == 0}
```

### Accessing Values

```python
user = {"name": "Alice", "age": 30}

# Index access
print(user["name"])  # Alice
# print(user["email"])  # KeyError!

# get() method (with default)
print(user.get("email"))  # None
print(user.get("email", "unknown@example.com"))  # unknown@example.com

# Check if key exists
if "name" in user:
    print(user["name"])

# Get all keys, values, items
print(user.keys())    # dict_keys(['name', 'age'])
print(user.values())  # dict_values(['Alice', 30])
print(user.items())   # dict_items([('name', 'Alice'), ('age', 30)])
```

### Modifying Dictionaries

```python
user = {"name": "Alice", "age": 30}

# Add or update
user["email"] = "alice@example.com"
user["age"] = 31  # Update existing

# update() - merge dictionaries
user.update({"city": "NYC", "country": "USA"})
user.update(phone="555-1234")

# setdefault() - set if key doesn't exist
user.setdefault("role", "user")  # Adds role: user
user.setdefault("name", "Bob")   # Does nothing (key exists)

# Delete
del user["age"]
popped = user.pop("city")  # Remove and return value
popped = user.pop("missing", "default")  # With default

# Remove arbitrary item
key, value = user.popitem()

# Clear all items
user.clear()
```

### Dictionary Methods

```python
user = {"name": "Alice", "age": 30, "city": "NYC"}

# Copy
user_copy = user.copy()  # Shallow copy

import copy
user_deep = copy.deepcopy(user)  # Deep copy

# fromkeys() - create dict with same keys
keys = ["a", "b", "c"]
d = dict.fromkeys(keys, 0)  # {'a': 0, 'b': 0, 'c': 0}

# Iterate
for key in user:
    print(key, user[key])

for key, value in user.items():
    print(f"{key}: {value}")

for value in user.values():
    print(value)
```

### Nested Dictionaries

```python
users = {
    "user1": {"name": "Alice", "age": 30},
    "user2": {"name": "Bob", "age": 25}
}

print(users["user1"]["name"])  # Alice

# Safe access with get()
email = users.get("user1", {}).get("email", "N/A")
```

### DefaultDict

Dictionary with default factory:

```python
from collections import defaultdict

# With list as default
groups = defaultdict(list)
groups["fruits"].append("apple")
groups["fruits"].append("banana")
groups["vegetables"].append("carrot")

print(groups)
# defaultdict(<class 'list'>, {'fruits': ['apple', 'banana'], 'vegetables': ['carrot']})

# With int as default (useful for counting)
counts = defaultdict(int)
for char in "hello":
    counts[char] += 1

print(counts)  # defaultdict(<class 'int'>, {'h': 1, 'e': 1, 'l': 2, 'o': 1})

# With custom default
def default_value():
    return "Unknown"

d = defaultdict(default_value)
print(d["missing"])  # Unknown
```

### Counter

Count hashable objects:

```python
from collections import Counter

# From iterable
counts = Counter("hello world")
print(counts)  # Counter({'l': 3, 'o': 2, 'h': 1, 'e': 1, ' ': 1, 'w': 1, 'r': 1, 'd': 1})

# From list
votes = ["apple", "banana", "apple", "cherry", "apple"]
counter = Counter(votes)
print(counter.most_common(2))  # [('apple', 3), ('banana', 1)]

# Arithmetic on counters
c1 = Counter(a=3, b=1)
c2 = Counter(a=1, b=2)

print(c1 + c2)  # Counter({'a': 4, 'b': 3})
print(c1 - c2)  # Counter({'a': 2})
print(c1 & c2)  # Counter({'a': 1, 'b': 1}) - intersection
print(c1 | c2)  # Counter({'a': 3, 'b': 2}) - union
```

### OrderedDict

Dictionary that remembers insertion order:

```python
from collections import OrderedDict

# Regular dict maintains order (Python 3.7+)
d = {"a": 1, "b": 2, "c": 3}

# OrderedDict for explicit ordering
od = OrderedDict()
od["a"] = 1
od["b"] = 2
od["c"] = 3

# Move to end
od.move_to_end("a")  # {'b': 2, 'c': 3, 'a': 1}
od.move_to_end("b", last=False)  # {'b': 2, 'c': 3, 'a': 1}

# Reverse
reversed_od = OrderedDict(reversed(od.items()))
```

### ChainMap

Combine multiple dicts:

```python
from collections import ChainMap

defaults = {"color": "blue", "size": 10}
overrides = {"size": 15}

combined = ChainMap(overrides, defaults)
print(combined["color"])  # blue (from defaults)
print(combined["size"])   # 15 (from overrides)

# Update only affects first map
combined["color"] = "red"
print(defaults)  # {'color': 'blue', 'size': 10} - unchanged
```

## Sets

### Creating Sets

```python
# Empty set (note: {} creates empty dict!)
empty = set()

# With elements
numbers = {1, 2, 3, 4, 5}

# From iterable
chars = set("hello")  # {'h', 'e', 'l', 'o'}
unique = set([1, 2, 2, 3, 3, 3])  # {1, 2, 3}

# Set comprehension
even_squares = {x**2 for x in range(10) if x % 2 == 0}
# {0, 4, 16, 36, 64}
```

### Set Operations

```python
# Add element
numbers = {1, 2, 3}
numbers.add(4)  # {1, 2, 3, 4}

# Add multiple
numbers.update([5, 6, 7])  # {1, 2, 3, 4, 5, 6, 7}

# Remove
numbers.remove(3)    # KeyError if not found
numbers.discard(3)   # No error if not found
popped = numbers.pop()  # Remove and return arbitrary element

# Clear
numbers.clear()

# Membership
print(2 in {1, 2, 3})  # True

# Size
print(len({1, 2, 3}))  # 3
```

### Set Math Operations

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

# Union (elements in either set)
print(a | b)           # {1, 2, 3, 4, 5, 6}
print(a.union(b))

# Intersection (elements in both sets)
print(a & b)           # {3, 4}
print(a.intersection(b))

# Difference (elements in a but not b)
print(a - b)           # {1, 2}
print(a.difference(b))

# Symmetric difference (elements in either but not both)
print(a ^ b)                      # {1, 2, 5, 6}
print(a.symmetric_difference(b))

# Subset and superset
c = {1, 2}
print(c.issubset(a))    # True
print(a.issuperset(c))  # True

# Disjoint (no common elements)
d = {7, 8, 9}
print(a.isdisjoint(d))  # True
```

### Update Operations

```python
a = {1, 2, 3}
b = {3, 4, 5}

# In-place union
a |= b  # a.update(b)
print(a)  # {1, 2, 3, 4, 5}

# In-place intersection
a = {1, 2, 3, 4}
a &= {2, 3, 5}  # a.intersection_update({2, 3, 5})
print(a)  # {2, 3}

# In-place difference
a = {1, 2, 3, 4}
a -= {2, 3}  # a.difference_update({2, 3})
print(a)  # {1, 4}

# In-place symmetric difference
a = {1, 2, 3}
a ^= {2, 3, 4}  # a.symmetric_difference_update({2, 3, 4})
print(a)  # {1, 4}
```

### Frozen Sets

Immutable sets:

```python
# Create frozenset
fs = frozenset([1, 2, 3, 4])

# Can be used as dict keys or in another set
d = {fs: "value"}
s = {frozenset([1, 2]), frozenset([3, 4])}

# All read operations work
print(2 in fs)  # True
print(fs | frozenset([5, 6]))  # frozenset({1, 2, 3, 4, 5, 6})

# Cannot modify
# fs.add(5)  # Error!
```

## Performance

```python
# Dictionary lookup: O(1) average
# Set membership: O(1) average
# List membership: O(n)

# Benchmark
import timeit

# Dict is much faster than list for lookups
d = {i: i for i in range(1000)}
l = list(range(1000))

print(timeit.timeit('999 in d', globals=globals(), number=100000))
# ~0.001 seconds

print(timeit.timeit('999 in l', globals=globals(), number=100000))
# ~0.5 seconds
```

> **Good to know:** Dictionary keys must be hashable (immutable). Dictionaries maintain insertion order since Python 3.7. Use `get()` instead of `[]` to avoid KeyError. `defaultdict` eliminates the need for existence checks. `Counter` is perfect for frequency counting. Sets provide O(1) membership testing. Frozen sets are immutable and hashable. Dictionary comprehensions are as powerful as list comprehensions. Use sets for uniqueness and fast membership testing. ChainMap is useful for layered configurations.

---

## References

- [Python - Dictionaries](https://docs.python.org/3/tutorial/datastructures.html#dictionaries)
- [Python - Sets](https://docs.python.org/3/tutorial/datastructures.html#sets)
- [Python - collections module](https://docs.python.org/3/library/collections.html)
- [Real Python - Dictionaries in Python](https://realpython.com/python-dicts/)
- [Real Python - Sets in Python](https://realpython.com/python-sets/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
