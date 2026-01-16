# Functions

## Function Definition

### Basic Functions

```python
# Simple function
def greet():
    print("Hello, World!")

greet()  # Call function

# With parameters
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")

# With return value
def add(a, b):
    return a + b

result = add(5, 3)  # 8

# Multiple return values
def divide_and_remainder(a, b):
    return a // b, a % b

quotient, remainder = divide_and_remainder(10, 3)
```

### Function Parameters

```python
# Positional parameters
def introduce(name, age):
    print(f"I'm {name}, {age} years old")

introduce("Alice", 30)

# Keyword arguments
introduce(age=30, name="Alice")  # Order doesn't matter

# Default parameters
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Alice")              # Hello, Alice!
greet("Bob", "Hi")          # Hi, Bob!
greet("Charlie", greeting="Hey")  # Hey, Charlie!

# Mutable default arguments (GOTCHA!)
def add_item(item, items=[]):  # DON'T DO THIS!
    items.append(item)
    return items

print(add_item(1))  # [1]
print(add_item(2))  # [1, 2] - Unexpected!

# Fix: Use None as default
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

### Variadic Functions

```python
# *args - variable positional arguments
def sum_all(*args):
    total = 0
    for num in args:
        total += num
    return total

print(sum_all(1, 2, 3))        # 6
print(sum_all(1, 2, 3, 4, 5))  # 15

# **kwargs - variable keyword arguments
def print_info(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_info(name="Alice", age=30, city="NYC")

# Combine all parameter types
def complex_function(a, b, *args, keyword_only, **kwargs):
    print(f"a: {a}, b: {b}")
    print(f"args: {args}")
    print(f"keyword_only: {keyword_only}")
    print(f"kwargs: {kwargs}")

complex_function(1, 2, 3, 4, keyword_only=5, x=6, y=7)
```

### Keyword-Only Arguments

```python
# Parameters after * are keyword-only
def greet(name, *, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Alice")  # OK
greet("Alice", greeting="Hi")  # OK
# greet("Alice", "Hi")  # Error: positional argument not allowed
```

### Positional-Only Arguments

```python
# Parameters before / are positional-only (Python 3.8+)
def divide(a, b, /):
    return a / b

divide(10, 2)      # OK
# divide(a=10, b=2)  # Error: positional-only

# Combine positional-only and keyword-only
def func(pos_only, /, standard, *, kw_only):
    pass

func(1, 2, kw_only=3)           # OK
func(1, standard=2, kw_only=3)  # OK
# func(pos_only=1, standard=2, kw_only=3)  # Error
```

## Lambda Functions

Anonymous, single-expression functions:

```python
# Basic lambda
square = lambda x: x ** 2
print(square(5))  # 25

# Multiple parameters
add = lambda x, y: x + y
print(add(3, 4))  # 7

# Used with map, filter, sorted
numbers = [1, 2, 3, 4, 5]

squared = list(map(lambda x: x**2, numbers))
evens = list(filter(lambda x: x % 2 == 0, numbers))

# Sort with key
pairs = [(1, 'one'), (3, 'three'), (2, 'two')]
sorted_pairs = sorted(pairs, key=lambda x: x[1])
# [(1, 'one'), (3, 'three'), (2, 'two')]
```

## Type Hints

```python
def greet(name: str) -> str:
    return f"Hello, {name}!"

def add(a: int, b: int) -> int:
    return a + b

# Complex type hints
from typing import List, Dict, Tuple, Optional, Union, Callable

def process_items(items: List[str]) -> Dict[str, int]:
    return {item: len(item) for item in items}

def find_user(id: int) -> Optional[str]:
    if id > 0:
        return "Alice"
    return None

def calculate(x: Union[int, float]) -> float:
    return float(x * 2)

# Function type hint
def apply_function(func: Callable[[int, int], int], a: int, b: int) -> int:
    return func(a, b)

# Python 3.10+ syntax
def process(value: int | str) -> str:
    return str(value)
```

## Decorators

Functions that modify other functions:

```python
# Simple decorator
def uppercase_decorator(func):
    def wrapper():
        result = func()
        return result.upper()
    return wrapper

@uppercase_decorator
def greet():
    return "hello, world!"

print(greet())  # HELLO, WORLD!

# Decorator with arguments
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def say_hello():
    print("Hello!")

say_hello()  # Prints "Hello!" 3 times

# Decorator preserving metadata
from functools import wraps

def my_decorator(func):
    @wraps(func)  # Preserves func.__name__, func.__doc__, etc.
    def wrapper(*args, **kwargs):
        print("Before function call")
        result = func(*args, **kwargs)
        print("After function call")
        return result
    return wrapper

# Class-based decorator
class CountCalls:
    def __init__(self, func):
        self.func = func
        self.count = 0

    def __call__(self, *args, **kwargs):
        self.count += 1
        print(f"Call #{self.count}")
        return self.func(*args, **kwargs)

@CountCalls
def say_hello():
    print("Hello!")

say_hello()  # Call #1, Hello!
say_hello()  # Call #2, Hello!
```

### Common Decorators

```python
# @staticmethod - no self or cls
class MathUtils:
    @staticmethod
    def add(a, b):
        return a + b

MathUtils.add(5, 3)  # 8

# @classmethod - receives class as first arg
class Person:
    count = 0

    def __init__(self, name):
        self.name = name
        Person.count += 1

    @classmethod
    def get_count(cls):
        return cls.count

# @property - getter
class Temperature:
    def __init__(self, celsius):
        self._celsius = celsius

    @property
    def fahrenheit(self):
        return self._celsius * 9/5 + 32

# @lru_cache - memoization
from functools import lru_cache

@lru_cache(maxsize=None)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
```

## Closures

Functions that capture enclosing scope:

```python
def make_multiplier(factor):
    def multiplier(x):
        return x * factor
    return multiplier

times_2 = make_multiplier(2)
times_5 = make_multiplier(5)

print(times_2(10))  # 20
print(times_5(10))  # 50

# Closure with state
def make_counter():
    count = 0
    def counter():
        nonlocal count
        count += 1
        return count
    return counter

counter = make_counter()
print(counter())  # 1
print(counter())  # 2
print(counter())  # 3
```

## Generator Functions

Functions that yield values lazily:

```python
def count_up_to(n):
    i = 1
    while i <= n:
        yield i
        i += 1

for num in count_up_to(5):
    print(num)  # 1, 2, 3, 4, 5

# Generator expressions
squares = (x**2 for x in range(10))
print(next(squares))  # 0
print(next(squares))  # 1

# Infinite generator
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

# Use with itertools.islice to limit
from itertools import islice
first_10 = list(islice(fibonacci(), 10))
```

## Recursive Functions

```python
def factorial(n):
    if n <= 1:
        return 1
    return n * factorial(n - 1)

print(factorial(5))  # 120

# Tail recursion (not optimized in Python)
def factorial_tail(n, acc=1):
    if n <= 1:
        return acc
    return factorial_tail(n - 1, n * acc)

# Better: Use iteration for recursion in Python
def factorial_iterative(n):
    result = 1
    for i in range(2, n + 1):
        result *= i
    return result
```

## Function Annotations

```python
def greet(name: str, enthusiastic: bool = False) -> str:
    """
    Greet someone.

    Args:
        name: Person's name
        enthusiastic: Whether to be enthusiastic

    Returns:
        Greeting message
    """
    greeting = f"Hello, {name}!"
    if enthusiastic:
        greeting += " Great to see you!"
    return greeting

# Access annotations
print(greet.__annotations__)
# {'name': <class 'str'>, 'enthusiastic': <class 'bool'>, 'return': <class 'str'>}
```

## Partial Functions

Pre-fill arguments:

```python
from functools import partial

def power(base, exponent):
    return base ** exponent

square = partial(power, exponent=2)
cube = partial(power, exponent=3)

print(square(5))  # 25
print(cube(5))    # 125
```

## Higher-Order Functions

Functions that take/return functions:

```python
def apply_twice(func, value):
    return func(func(value))

def add_5(x):
    return x + 5

result = apply_twice(add_5, 10)  # 20

# Return function
def make_adder(n):
    def adder(x):
        return x + n
    return adder

add_10 = make_adder(10)
print(add_10(5))  # 15
```

> **Good to know:** Avoid mutable default arguments - use `None` and create the object inside the function. Use `*args` and `**kwargs` for flexible function signatures. Decorators modify functions without changing their code. Generators (`yield`) are memory-efficient for large sequences. Python doesn't optimize tail recursion - use iteration instead. Type hints are optional but improve IDE support and catch errors with mypy. The `@functools.wraps` decorator preserves function metadata when creating decorators.

---

## References

- [Python - Defining Functions](https://docs.python.org/3/tutorial/controlflow.html#defining-functions)
- [Python - More on Functions](https://docs.python.org/3/tutorial/controlflow.html#more-on-defining-functions)
- [PEP 3107 - Function Annotations](https://www.python.org/dev/peps/pep-3107/)
- [PEP 484 - Type Hints](https://www.python.org/dev/peps/pep-0484/)
- [Real Python - Defining Your Own Python Function](https://realpython.com/defining-your-own-python-function/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
