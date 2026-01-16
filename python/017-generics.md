# Generics & Type Parameters

## Type Hints Basics

### Basic Type Annotations

```python
# Variable annotations
name: str = "Alice"
age: int = 30
height: float = 5.9
is_active: bool = True

# Function annotations
def greet(name: str) -> str:
    return f"Hello, {name}!"

def add(a: int, b: int) -> int:
    return a + b

# No return value
def log_message(message: str) -> None:
    print(message)

# Multiple return types (use Union)
from typing import Union

def divide(a: float, b: float) -> Union[float, None]:
    if b == 0:
        return None
    return a / b
```

### Collection Types

```python
from typing import List, Dict, Tuple, Set, Optional

# Lists
names: List[str] = ["Alice", "Bob", "Charlie"]
numbers: List[int] = [1, 2, 3, 4, 5]

def process_items(items: List[str]) -> List[str]:
    return [item.upper() for item in items]

# Dictionaries
user: Dict[str, int] = {"Alice": 30, "Bob": 25}
config: Dict[str, any] = {"host": "localhost", "port": 8080}

def get_user(user_id: int) -> Dict[str, str]:
    return {"id": str(user_id), "name": "Alice"}

# Tuples (fixed length)
point: Tuple[int, int] = (10, 20)
rgb: Tuple[int, int, int] = (255, 128, 0)

# Variable length tuple
numbers: Tuple[int, ...] = (1, 2, 3, 4, 5)

# Sets
unique_ids: Set[int] = {1, 2, 3, 4, 5}

# Optional (can be None)
def find_user(user_id: int) -> Optional[Dict[str, str]]:
    if user_id > 0:
        return {"id": str(user_id), "name": "Alice"}
    return None

# Same as Union[Dict[str, str], None]
```

### Modern Type Hints (Python 3.9+)

```python
# Built-in generic types (no import needed)
def process_names(names: list[str]) -> list[str]:
    return [name.upper() for name in names]

def get_config() -> dict[str, int]:
    return {"timeout": 30, "retries": 3}

def get_point() -> tuple[int, int]:
    return (10, 20)

# Union with | operator (Python 3.10+)
def divide(a: float, b: float) -> float | None:
    if b == 0:
        return None
    return a / b

# Optional shorthand
def find_user(user_id: int) -> dict[str, str] | None:
    return None
```

## Generic Types (TypeVar)

### Basic TypeVar

```python
from typing import TypeVar, List

# Define type variable
T = TypeVar('T')

def first(items: List[T]) -> T:
    """Get first item from list."""
    return items[0]

# Type checker understands:
x: int = first([1, 2, 3])        # T is int
y: str = first(["a", "b", "c"])  # T is str

# Multiple type variables
K = TypeVar('K')
V = TypeVar('V')

def get_first_pair(d: Dict[K, V]) -> Tuple[K, V]:
    """Get first key-value pair."""
    key = next(iter(d))
    return key, d[key]
```

### Constrained TypeVar

```python
from typing import TypeVar

# Constrained to specific types
NumberType = TypeVar('NumberType', int, float)

def add(a: NumberType, b: NumberType) -> NumberType:
    """Add two numbers."""
    return a + b

# Valid
add(1, 2)      # int + int -> int
add(1.5, 2.5)  # float + float -> float

# Invalid (type checker error)
# add("a", "b")  # str not allowed

# Bounded TypeVar (must inherit from base)
from typing import TypeVar

class Animal:
    def speak(self) -> str:
        return "..."

class Dog(Animal):
    def speak(self) -> str:
        return "Woof!"

AnimalType = TypeVar('AnimalType', bound=Animal)

def make_speak(animal: AnimalType) -> AnimalType:
    """Make animal speak and return it."""
    print(animal.speak())
    return animal

dog = Dog()
make_speak(dog)  # OK
# make_speak("not an animal")  # Error
```

## Generic Classes

### Basic Generic Class

```python
from typing import Generic, TypeVar

T = TypeVar('T')

class Stack(Generic[T]):
    """Generic stack implementation."""

    def __init__(self) -> None:
        self._items: List[T] = []

    def push(self, item: T) -> None:
        self._items.append(item)

    def pop(self) -> T:
        return self._items.pop()

    def peek(self) -> T:
        return self._items[-1]

    def is_empty(self) -> bool:
        return len(self._items) == 0

# Usage with type hints
int_stack: Stack[int] = Stack()
int_stack.push(1)
int_stack.push(2)
value: int = int_stack.pop()  # Type checker knows it's int

str_stack: Stack[str] = Stack()
str_stack.push("hello")
str_stack.push("world")
# str_stack.push(123)  # Type error!
```

### Multiple Type Parameters

```python
from typing import Generic, TypeVar

K = TypeVar('K')
V = TypeVar('V')

class Pair(Generic[K, V]):
    """Generic pair of values."""

    def __init__(self, key: K, value: V) -> None:
        self.key = key
        self.value = value

    def get_key(self) -> K:
        return self.key

    def get_value(self) -> V:
        return self.value

# Usage
pair1: Pair[str, int] = Pair("age", 30)
pair2: Pair[int, str] = Pair(1, "Alice")

# Generic cache
class Cache(Generic[K, V]):
    """Simple cache implementation."""

    def __init__(self) -> None:
        self._data: Dict[K, V] = {}

    def set(self, key: K, value: V) -> None:
        self._data[key] = value

    def get(self, key: K) -> V | None:
        return self._data.get(key)

    def has(self, key: K) -> bool:
        return key in self._data

cache: Cache[str, int] = Cache()
cache.set("count", 42)
value = cache.get("count")  # Type: int | None
```

## Protocol (Structural Subtyping)

### Basic Protocol

```python
from typing import Protocol

class Drawable(Protocol):
    """Protocol for drawable objects."""

    def draw(self) -> None:
        """Draw the object."""
        ...

# Any class with draw() method satisfies the protocol
class Circle:
    def draw(self) -> None:
        print("Drawing circle")

class Square:
    def draw(self) -> None:
        print("Drawing square")

def render(obj: Drawable) -> None:
    """Render any drawable object."""
    obj.draw()

# Both work without explicit inheritance
render(Circle())
render(Square())
```

### Protocol with Properties

```python
from typing import Protocol

class Sized(Protocol):
    """Protocol for objects with size."""

    def __len__(self) -> int:
        ...

class Named(Protocol):
    """Protocol for objects with name."""

    @property
    def name(self) -> str:
        ...

def print_size(obj: Sized) -> None:
    print(f"Size: {len(obj)}")

def print_name(obj: Named) -> None:
    print(f"Name: {obj.name}")

# Works with any matching class
print_size([1, 2, 3])        # List has __len__
print_size("hello")          # str has __len__
print_size({1, 2, 3})        # set has __len__

class Person:
    def __init__(self, name: str):
        self._name = name

    @property
    def name(self) -> str:
        return self._name

print_name(Person("Alice"))  # Works!
```

### Generic Protocol

```python
from typing import Protocol, TypeVar

T = TypeVar('T')

class Comparable(Protocol):
    """Protocol for comparable objects."""

    def __lt__(self, other: 'Comparable') -> bool:
        ...

    def __gt__(self, other: 'Comparable') -> bool:
        ...

def get_min(a: Comparable, b: Comparable) -> Comparable:
    """Get minimum of two comparable objects."""
    return a if a < b else b

print(get_min(5, 10))          # Works with int
print(get_min("apple", "zoo")) # Works with str

# Generic protocol
T_co = TypeVar('T_co', covariant=True)

class SupportsIter(Protocol[T_co]):
    """Protocol for iterable objects."""

    def __iter__(self) -> 'Iterator[T_co]':
        ...

def process_iterable(items: SupportsIter[int]) -> int:
    """Process iterable of integers."""
    return sum(items)
```

## Advanced Generic Patterns

### Generic Factory

```python
from typing import TypeVar, Type, Callable

T = TypeVar('T')

class Factory(Generic[T]):
    """Generic factory pattern."""

    def __init__(self, constructor: Type[T]) -> None:
        self._constructor = constructor

    def create(self, *args, **kwargs) -> T:
        return self._constructor(*args, **kwargs)

# Usage
class User:
    def __init__(self, name: str, age: int):
        self.name = name
        self.age = age

user_factory: Factory[User] = Factory(User)
user1 = user_factory.create("Alice", 30)
user2 = user_factory.create("Bob", 25)
```

### Generic Builder

```python
from typing import Generic, TypeVar, Optional

T = TypeVar('T')

class Builder(Generic[T]):
    """Generic builder pattern."""

    def __init__(self, target_class: Type[T]) -> None:
        self._target_class = target_class
        self._params: Dict[str, any] = {}

    def set(self, key: str, value: any) -> 'Builder[T]':
        self._params[key] = value
        return self

    def build(self) -> T:
        return self._target_class(**self._params)

# Usage
class Config:
    def __init__(self, host: str, port: int, debug: bool = False):
        self.host = host
        self.port = port
        self.debug = debug

config = (Builder(Config)
          .set("host", "localhost")
          .set("port", 8080)
          .set("debug", True)
          .build())
```

### Covariance and Contravariance

```python
from typing import TypeVar, Generic, List

# Covariant (Producer)
T_co = TypeVar('T_co', covariant=True)

class Producer(Generic[T_co]):
    """Produces values of type T."""

    def __init__(self, value: T_co):
        self._value = value

    def produce(self) -> T_co:
        return self._value

# Contravariant (Consumer)
T_contra = TypeVar('T_contra', contravariant=True)

class Consumer(Generic[T_contra]):
    """Consumes values of type T."""

    def consume(self, value: T_contra) -> None:
        print(f"Consuming: {value}")

# Invariant (Default)
T = TypeVar('T')

class Box(Generic[T]):
    """Holds value of type T."""

    def __init__(self, value: T):
        self._value = value

    def get(self) -> T:
        return self._value

    def set(self, value: T) -> None:
        self._value = value
```

## Type Aliases

```python
from typing import List, Dict, Tuple, Union

# Simple type aliases
UserId = int
UserName = str
Email = str

def create_user(user_id: UserId, name: UserName, email: Email) -> None:
    pass

# Complex type aliases
User = Dict[str, Union[str, int]]
UserList = List[User]

users: UserList = [
    {"id": 1, "name": "Alice", "age": 30},
    {"id": 2, "name": "Bob", "age": 25}
]

# Generic type aliases
from typing import TypeVar

T = TypeVar('T')
Vector = List[T]
Matrix = List[Vector[T]]

# Usage
int_vector: Vector[int] = [1, 2, 3]
int_matrix: Matrix[int] = [[1, 2], [3, 4]]

# Callable type alias
from typing import Callable

Handler = Callable[[str], None]

def register_handler(handler: Handler) -> None:
    pass
```

## NewType

```python
from typing import NewType

# Create distinct type (compile-time only)
UserId = NewType('UserId', int)
ProductId = NewType('ProductId', int)

def get_user(user_id: UserId) -> str:
    return f"User {user_id}"

def get_product(product_id: ProductId) -> str:
    return f"Product {product_id}"

# Usage
user_id = UserId(123)
product_id = ProductId(456)

get_user(user_id)      # OK
get_product(product_id) # OK

# Type checker catches mistakes:
# get_user(product_id)  # Error: Expected UserId, got ProductId
# get_user(123)         # Error: Expected UserId, got int

# Runtime: NewType is just a function that returns its argument
print(type(user_id))  # <class 'int'>
```

## Type Checking

### Using mypy

```bash
# Install mypy
pip install mypy

# Check file
mypy script.py

# Check directory
mypy src/

# With specific Python version
mypy --python-version 3.11 script.py

# Strict mode
mypy --strict script.py
```

```python
# mypy configuration: mypy.ini or setup.cfg
[mypy]
python_version = 3.11
warn_return_any = True
warn_unused_configs = True
disallow_untyped_defs = True
disallow_any_generics = True

# Ignore specific files
[mypy-tests.*]
ignore_errors = True

# Type checking directives
def untyped_function():  # type: ignore
    pass

# Reveal type for debugging
reveal_type(variable)  # Shows inferred type in mypy output
```

### Type Guards

```python
from typing import Union, TypeGuard

def is_string_list(val: List[Union[str, int]]) -> TypeGuard[List[str]]:
    """Check if all items are strings."""
    return all(isinstance(x, str) for x in val)

def process(items: List[Union[str, int]]) -> None:
    if is_string_list(items):
        # Type checker knows items is List[str]
        for item in items:
            print(item.upper())  # OK: item is str
    else:
        print("Mixed types")
```

### Literal Types

```python
from typing import Literal

# Only specific values allowed
def set_mode(mode: Literal["read", "write", "append"]) -> None:
    pass

set_mode("read")   # OK
set_mode("write")  # OK
# set_mode("delete")  # Error

# With generics
T = TypeVar('T')

def repeat(value: T, count: Literal[1, 2, 3]) -> List[T]:
    return [value] * count
```

> **Good to know:** Type hints are optional and don't affect runtime behavior. Use `TypeVar` for generic functions and classes. `Protocol` enables structural subtyping without inheritance. Generic types preserve type information through operations. Use `covariant=True` for read-only generics (producers). Use `contravariant=True` for write-only generics (consumers). Type aliases improve code readability. `NewType` creates distinct types at compile-time only. Use `mypy` or other type checkers to validate type hints. Python 3.9+ allows `list[int]` instead of `List[int]`. Python 3.10+ allows `int | str` instead of `Union[int, str]`. Type hints improve IDE autocomplete and catch bugs early. `reveal_type()` shows inferred types in mypy. Use `# type: ignore` to suppress type checker warnings.

---

## References

- [Python - Type Hints](https://docs.python.org/3/library/typing.html)
- [PEP 484 - Type Hints](https://www.python.org/dev/peps/pep-0484/)
- [PEP 544 - Protocols](https://www.python.org/dev/peps/pep-0544/)
- [PEP 585 - Type Hinting Generics](https://www.python.org/dev/peps/pep-0585/)
- [mypy - Static Type Checker](http://mypy-lang.org/)
- [Real Python - Type Checking](https://realpython.com/python-type-checking/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
