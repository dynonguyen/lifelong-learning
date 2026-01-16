# Objects/Structs

## Classes

### Basic Class Definition

```python
class Dog:
    """A simple Dog class."""

    def __init__(self, name, age):
        """Initialize dog with name and age."""
        self.name = name
        self.age = age

    def bark(self):
        """Make the dog bark."""
        print(f"{self.name} says Woof!")

    def get_age_in_dog_years(self):
        """Calculate age in dog years."""
        return self.age * 7

# Create instance
my_dog = Dog("Buddy", 3)
my_dog.bark()  # Buddy says Woof!
print(my_dog.get_age_in_dog_years())  # 21
```

### Class Variables vs Instance Variables

```python
class Counter:
    # Class variable (shared by all instances)
    total_count = 0

    def __init__(self, name):
        # Instance variable (unique to each instance)
        self.name = name
        self.count = 0
        Counter.total_count += 1

    def increment(self):
        self.count += 1

c1 = Counter("Counter1")
c2 = Counter("Counter2")

print(Counter.total_count)  # 2
print(c1.count)  # 0
print(c2.count)  # 0
```

### Properties

```python
class Temperature:
    def __init__(self, celsius):
        self._celsius = celsius

    @property
    def celsius(self):
        """Get temperature in Celsius."""
        return self._celsius

    @celsius.setter
    def celsius(self, value):
        """Set temperature in Celsius."""
        if value < -273.15:
            raise ValueError("Temperature below absolute zero!")
        self._celsius = value

    @property
    def fahrenheit(self):
        """Get temperature in Fahrenheit."""
        return self._celsius * 9/5 + 32

    @fahrenheit.setter
    def fahrenheit(self, value):
        """Set temperature in Fahrenheit."""
        self._celsius = (value - 32) * 5/9

temp = Temperature(25)
print(temp.fahrenheit)  # 77.0
temp.fahrenheit = 32
print(temp.celsius)  # 0.0
```

### Static Methods and Class Methods

```python
class MathOperations:
    @staticmethod
    def add(a, b):
        """Static method - doesn't access instance or class."""
        return a + b

    @classmethod
    def from_string(cls, string):
        """Class method - receives class as first argument."""
        # Factory method
        return cls()

# Call without instance
result = MathOperations.add(5, 3)  # 8
```

### Magic Methods (Dunder Methods)

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        """String representation for users."""
        return f"Point({self.x}, {self.y})"

    def __repr__(self):
        """String representation for developers."""
        return f"Point(x={self.x}, y={self.y})"

    def __eq__(self, other):
        """Equality comparison."""
        return self.x == other.x and self.y == other.y

    def __add__(self, other):
        """Addition operator."""
        return Point(self.x + other.x, self.y + other.y)

    def __len__(self):
        """Length (magnitude)."""
        return int((self.x**2 + self.y**2)**0.5)

    def __getitem__(self, index):
        """Index access."""
        if index == 0:
            return self.x
        elif index == 1:
            return self.y
        raise IndexError("Point index out of range")

p1 = Point(1, 2)
p2 = Point(3, 4)

print(p1)           # Point(1, 2)
print(p1 + p2)      # Point(4, 6)
print(p1 == p2)     # False
print(len(p1))      # 2
print(p1[0])        # 1
```

## Dataclasses

Simplified class syntax (Python 3.7+):

```python
from dataclasses import dataclass, field
from typing import List

@dataclass
class Person:
    name: str
    age: int
    email: str = "unknown@example.com"  # Default value
    hobbies: List[str] = field(default_factory=list)  # Mutable default

    def greet(self):
        return f"Hello, I'm {self.name}"

# Automatic __init__, __repr__, __eq__
person = Person("Alice", 30)
print(person)  # Person(name='Alice', age=30, email='unknown@example.com', hobbies=[])

# With additional features
@dataclass(frozen=True)  # Immutable
class ImmutablePoint:
    x: float
    y: float

@dataclass(order=True)  # Comparable
class ComparablePerson:
    name: str
    age: int
```

### Dataclass Options

```python
from dataclasses import dataclass, field

@dataclass(
    init=True,      # Generate __init__
    repr=True,      # Generate __repr__
    eq=True,        # Generate __eq__
    order=False,    # Generate comparison methods
    frozen=False,   # Make immutable
    slots=False,    # Use __slots__
)
class Product:
    name: str
    price: float
    quantity: int = 0
    # Exclude from __init__
    total_value: float = field(init=False)

    def __post_init__(self):
        """Called after __init__."""
        self.total_value = self.price * self.quantity
```

## Named Tuples

Lightweight alternative to classes:

```python
from collections import namedtuple

# Create named tuple class
Point = namedtuple('Point', ['x', 'y'])
Person = namedtuple('Person', 'name age email')

# Create instances
p = Point(10, 20)
person = Person("Alice", 30, "alice@example.com")

# Access by name or index
print(p.x, p.y)     # 10 20
print(p[0], p[1])   # 10 20

# Immutable
# p.x = 15  # Error!

# Convert to dict
print(p._asdict())  # {'x': 10, 'y': 20}

# With defaults (Python 3.7+)
Person = namedtuple('Person', ['name', 'age'], defaults=['Unknown', 0])
p = Person()  # Person(name='Unknown', age=0)
```

## Attrs

Third-party alternative to dataclasses:

```python
import attr

@attr.s
class Person:
    name = attr.ib()
    age = attr.ib()
    email = attr.ib(default="unknown@example.com")

    @age.validator
    def check_age(self, attribute, value):
        if value < 0:
            raise ValueError("Age cannot be negative")

# Or modern syntax
@attr.define
class Product:
    name: str
    price: float
    quantity: int = 0
```

## Encapsulation

```python
class BankAccount:
    def __init__(self, balance):
        self._balance = balance  # Protected (convention)
        self.__pin = 1234        # Private (name mangling)

    def get_balance(self):
        return self._balance

    def __verify_pin(self, pin):
        """Private method."""
        return pin == self.__pin

    def withdraw(self, amount, pin):
        if self.__verify_pin(pin):
            self._balance -= amount

account = BankAccount(1000)
print(account.get_balance())  # 1000
print(account._balance)       # 1000 (accessible but discouraged)
# print(account.__pin)        # Error!
print(account._BankAccount__pin)  # 1234 (name mangling, not recommended)
```

## Slots

Optimize memory and performance:

```python
class Point:
    __slots__ = ['x', 'y']  # Only these attributes allowed

    def __init__(self, x, y):
        self.x = x
        self.y = y

p = Point(10, 20)
# p.z = 30  # Error! 'Point' object has no attribute 'z'

# Benefits:
# - Faster attribute access
# - Lower memory usage
# - Prevents accidental attribute creation
```

## Abstract Base Classes

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        """Calculate area."""
        pass

    @abstractmethod
    def perimeter(self):
        """Calculate perimeter."""
        pass

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

    def perimeter(self):
        return 2 * (self.width + self.height)

# shape = Shape()  # Error! Can't instantiate abstract class
rect = Rectangle(10, 20)
print(rect.area())  # 200
```

## Protocols (Structural Subtyping)

```python
from typing import Protocol

class Drawable(Protocol):
    """Anything with a draw method."""
    def draw(self) -> None:
        ...

class Circle:
    def draw(self) -> None:
        print("Drawing circle")

class Square:
    def draw(self) -> None:
        print("Drawing square")

def render(shape: Drawable) -> None:
    """Works with any object that has draw()."""
    shape.draw()

render(Circle())  # OK
render(Square())  # OK
```

## Copying Objects

```python
import copy

class Person:
    def __init__(self, name, friends):
        self.name = name
        self.friends = friends

person1 = Person("Alice", ["Bob", "Charlie"])

# Shallow copy
person2 = copy.copy(person1)
person2.name = "Dave"           # Different
person2.friends.append("Eve")   # Shared!

# Deep copy
person3 = copy.deepcopy(person1)
person3.friends.append("Frank")  # Independent
```

> **Good to know:** Use dataclasses for simple data containers - they automatically generate `__init__`, `__repr__`, `__eq__`, and more. Properties allow you to add logic to attribute access without changing the interface. Double underscore prefix (`__private`) triggers name mangling, making attributes harder to access accidentally. Single underscore (`_protected`) is just a convention. Slots (`__slots__`) significantly reduce memory usage but prevent adding new attributes dynamically. Abstract base classes ensure subclasses implement required methods.

---

## References

- [Python - Classes](https://docs.python.org/3/tutorial/classes.html)
- [Python - Data Classes](https://docs.python.org/3/library/dataclasses.html)
- [Python - Abstract Base Classes](https://docs.python.org/3/library/abc.html)
- [PEP 557 - Data Classes](https://www.python.org/dev/peps/pep-0557/)
- [attrs - Classes Without Boilerplate](https://www.attrs.org/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
