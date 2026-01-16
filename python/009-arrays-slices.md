# Arrays/Slices/Lists/Vectors

## Lists

Mutable, dynamic arrays:

### Creating Lists

```python
# Empty list
empty = []
empty = list()

# With elements
numbers = [1, 2, 3, 4, 5]
mixed = [1, "hello", 3.14, True, None]
nested = [[1, 2], [3, 4], [5, 6]]

# List constructor
from_range = list(range(5))  # [0, 1, 2, 3, 4]
from_string = list("hello")  # ['h', 'e', 'l', 'l', 'o']
from_tuple = list((1, 2, 3))  # [1, 2, 3]

# List with repeated elements
zeros = [0] * 10  # [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
```

### Accessing Elements

```python
numbers = [10, 20, 30, 40, 50]

# Indexing (0-based)
print(numbers[0])   # 10
print(numbers[2])   # 30
print(numbers[-1])  # 50 (last element)
print(numbers[-2])  # 40 (second from last)

# Slicing [start:end:step]
print(numbers[1:4])    # [20, 30, 40] - indices 1, 2, 3
print(numbers[:3])     # [10, 20, 30] - first 3
print(numbers[2:])     # [30, 40, 50] - from index 2 to end
print(numbers[::2])    # [10, 30, 50] - every other element
print(numbers[::-1])   # [50, 40, 30, 20, 10] - reverse

# Negative indices in slicing
print(numbers[-3:])    # [30, 40, 50] - last 3
print(numbers[:-2])    # [10, 20, 30] - all but last 2
```

### Modifying Lists

```python
numbers = [1, 2, 3, 4, 5]

# Modify element
numbers[0] = 10     # [10, 2, 3, 4, 5]

# Modify slice
numbers[1:3] = [20, 30]  # [10, 20, 30, 4, 5]

# Append (add to end)
numbers.append(6)   # [10, 20, 30, 4, 5, 6]

# Insert at index
numbers.insert(0, 0)  # [0, 10, 20, 30, 4, 5, 6]

# Extend (add multiple elements)
numbers.extend([7, 8, 9])  # [..., 7, 8, 9]
numbers += [10, 11]        # Same as extend

# Remove by value
numbers.remove(10)  # Removes first occurrence

# Remove by index
popped = numbers.pop()     # Remove and return last element
popped = numbers.pop(0)    # Remove and return element at index 0

# Delete
del numbers[0]      # Delete element at index
del numbers[1:3]    # Delete slice

# Clear all elements
numbers.clear()     # []
```

### List Operations

```python
# Concatenation
list1 = [1, 2, 3]
list2 = [4, 5, 6]
combined = list1 + list2  # [1, 2, 3, 4, 5, 6]

# Repetition
repeated = [1, 2] * 3  # [1, 2, 1, 2, 1, 2]

# Membership
print(2 in [1, 2, 3])      # True
print(5 not in [1, 2, 3])  # True

# Length
print(len([1, 2, 3]))  # 3

# Count occurrences
numbers = [1, 2, 2, 3, 2, 4]
print(numbers.count(2))  # 3

# Find index
print(numbers.index(3))  # 3
# print(numbers.index(10))  # ValueError if not found
```

### Sorting and Reversing

```python
numbers = [3, 1, 4, 1, 5, 9, 2, 6]

# Sort in place
numbers.sort()  # [1, 1, 2, 3, 4, 5, 6, 9]
numbers.sort(reverse=True)  # [9, 6, 5, 4, 3, 2, 1, 1]

# Return sorted copy
original = [3, 1, 4, 1, 5]
sorted_copy = sorted(original)  # [1, 1, 3, 4, 5]
# original is unchanged

# Sort with key function
words = ["banana", "apple", "cherry"]
words.sort(key=len)  # Sort by length
# ['apple', 'banana', 'cherry']

# Sort complex objects
students = [
    {"name": "Alice", "grade": 85},
    {"name": "Bob", "grade": 92},
    {"name": "Charlie", "grade": 78}
]
students.sort(key=lambda s: s["grade"], reverse=True)

# Reverse in place
numbers = [1, 2, 3, 4, 5]
numbers.reverse()  # [5, 4, 3, 2, 1]

# Return reversed copy
reversed_copy = list(reversed(numbers))
```

## List Comprehensions

Concise way to create lists:

```python
# Basic comprehension
squares = [x**2 for x in range(10)]
# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

# With condition
evens = [x for x in range(10) if x % 2 == 0]
# [0, 2, 4, 6, 8]

# Multiple conditions
nums = [x for x in range(20) if x % 2 == 0 if x % 3 == 0]
# [0, 6, 12, 18]

# if-else in comprehension
labels = ["even" if x % 2 == 0 else "odd" for x in range(5)]
# ['even', 'odd', 'even', 'odd', 'even']

# Nested loops
pairs = [(x, y) for x in range(3) for y in range(3)]
# [(0,0), (0,1), (0,2), (1,0), (1,1), (1,2), (2,0), (2,1), (2,2)]

# Flatten nested list
nested = [[1, 2], [3, 4], [5, 6]]
flat = [item for sublist in nested for item in sublist]
# [1, 2, 3, 4, 5, 6]

# List of tuples to dict
pairs = [("a", 1), ("b", 2), ("c", 3)]
d = {k: v for k, v in pairs}
# {'a': 1, 'b': 2, 'c': 3}
```

## Advanced List Operations

### Unpacking

```python
numbers = [1, 2, 3, 4, 5]

# Basic unpacking
a, b, c, d, e = numbers

# Unpack with rest
first, *rest = numbers       # first=1, rest=[2,3,4,5]
first, *middle, last = numbers  # first=1, middle=[2,3,4], last=5

# Unpacking in function calls
def add_three(a, b, c):
    return a + b + c

nums = [1, 2, 3]
result = add_three(*nums)  # Unpack list as arguments
```

### zip()

Combine multiple lists:

```python
names = ["Alice", "Bob", "Charlie"]
ages = [30, 25, 35]
cities = ["NYC", "LA", "Chicago"]

# Zip lists together
combined = list(zip(names, ages, cities))
# [('Alice', 30, 'NYC'), ('Bob', 25, 'LA'), ('Charlie', 35, 'Chicago')]

# Iterate zipped lists
for name, age, city in zip(names, ages, cities):
    print(f"{name}, {age}, from {city}")

# Unzip
zipped = [(1, 'a'), (2, 'b'), (3, 'c')]
numbers, letters = zip(*zipped)
# numbers = (1, 2, 3), letters = ('a', 'b', 'c')
```

### enumerate()

Get index and value:

```python
fruits = ["apple", "banana", "cherry"]

for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")
# 0: apple
# 1: banana
# 2: cherry

# Start from custom index
for index, fruit in enumerate(fruits, start=1):
    print(f"{index}: {fruit}")
# 1: apple
# 2: banana
# 3: cherry
```

### map() and filter()

```python
# map() - apply function to each element
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, numbers))
# [1, 4, 9, 16, 25]

# filter() - keep elements that match condition
evens = list(filter(lambda x: x % 2 == 0, numbers))
# [2, 4]

# Multiple iterables with map
nums1 = [1, 2, 3]
nums2 = [10, 20, 30]
sums = list(map(lambda x, y: x + y, nums1, nums2))
# [11, 22, 33]
```

### reduce()

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]

# Sum all elements
total = reduce(lambda acc, x: acc + x, numbers)
# 15

# With initial value
total = reduce(lambda acc, x: acc + x, numbers, 100)
# 115

# Find maximum
maximum = reduce(lambda a, b: a if a > b else b, numbers)
# 5
```

## List Copying

```python
original = [1, 2, 3, 4, 5]

# Shallow copy
copy1 = original.copy()
copy2 = original[:]
copy3 = list(original)

# Deep copy (for nested lists)
import copy
nested = [[1, 2], [3, 4]]
deep = copy.deepcopy(nested)
```

## Performance Tips

```python
# Pre-allocate if size is known
# Better: [None] * size than multiple appends

# Use list comprehension instead of loop
# Good:
squares = [x**2 for x in range(1000)]

# Slow:
squares = []
for x in range(1000):
    squares.append(x**2)

# Use generators for large datasets
# Memory efficient:
squares_gen = (x**2 for x in range(1000000))

# Join strings efficiently
# Good:
result = "".join(["a", "b", "c"] * 1000)

# Slow:
result = ""
for _ in range(1000):
    result += "a" + "b" + "c"
```

## Array Module

For homogeneous numeric arrays:

```python
import array

# Create typed array
numbers = array.array('i', [1, 2, 3, 4, 5])  # 'i' = signed int

# More memory efficient than list for numbers
# But less flexible
```

## NumPy Arrays

For numerical computing:

```python
import numpy as np

# Create array
arr = np.array([1, 2, 3, 4, 5])

# Vectorized operations
arr * 2         # [2, 4, 6, 8, 10]
arr + arr       # [2, 4, 6, 8, 10]
arr ** 2        # [1, 4, 9, 16, 25]

# Much faster than lists for numerical operations
```

> **Good to know:** Lists are dynamic arrays implemented as contiguous memory blocks. Appending is O(1) amortized, but inserting at the beginning is O(n). Use list comprehensions instead of loops for better performance and readability. Slicing creates shallow copies. For large numeric arrays, use NumPy instead of lists. The `*` operator for unpacking and the `*rest` syntax for extended unpacking are powerful features. Remember that negative indices count from the end (-1 is last element).

---

## References

- [Python - Lists](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists)
- [Python - List Comprehensions](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions)
- [Python - Sorting HOW TO](https://docs.python.org/3/howto/sorting.html)
- [Real Python - Python Lists and List Comprehensions](https://realpython.com/python-lists-tuples/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
