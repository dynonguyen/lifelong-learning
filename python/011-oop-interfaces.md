# OOP/Interfaces/Methods

## Classes and Objects

### Basic Class

```python
class Dog:
    # Class variable
    species = "Canis familiaris"

    def __init__(self, name, age):
        # Instance variables
        self.name = name
        self.age = age

    # Instance method
    def bark(self):
        return f"{self.name} says Woof!"

    def get_info(self):
        return f"{self.name} is {self.age} years old"

# Create instances
dog1 = Dog("Buddy", 3)
dog2 = Dog("Max", 5)

print(dog1.bark())  # Buddy says Woof!
print(Dog.species)  # Canis familiaris
```

## Inheritance

```python
# Parent class
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        raise NotImplementedError("Subclass must implement")

# Child classes
class Dog(Animal):
    def speak(self):
        return f"{self.name} barks!"

class Cat(Animal):
    def speak(self):
        return f"{self.name} meows!"

dog = Dog("Buddy")
cat = Cat("Whiskers")

print(dog.speak())  # Buddy barks!
print(cat.speak())  # Whiskers meows!
```

### Multiple Inheritance

```python
class Flyable:
    def fly(self):
        return "Flying!"

class Swimmable:
    def swim(self):
        return "Swimming!"

class Duck(Flyable, Swimmable):
    def quack(self):
        return "Quack!"

duck = Duck()
print(duck.fly())   # Flying!
print(duck.swim())  # Swimming!
print(duck.quack()) # Quack!

# Method Resolution Order (MRO)
print(Duck.__mro__)
# (<class 'Duck'>, <class 'Flyable'>, <class 'Swimmable'>, <class 'object'>)
```

### super()

```python
class Animal:
    def __init__(self, name):
        self.name = name
        print(f"Animal __init__: {name}")

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)  # Call parent __init__
        self.breed = breed
        print(f"Dog __init__: {breed}")

dog = Dog("Buddy", "Golden Retriever")
# Output:
# Animal __init__: Buddy
# Dog __init__: Golden Retriever
```

## Encapsulation

### Public, Protected, Private

```python
class BankAccount:
    def __init__(self, balance):
        self.public_var = "public"      # Public
        self._protected_var = "protected"  # Protected (convention)
        self.__private_var = "private"  # Private (name mangling)

    def public_method(self):
        return "Public method"

    def _protected_method(self):
        return "Protected method"

    def __private_method(self):
        return "Private method"

    def access_private(self):
        return self.__private_method()

account = BankAccount(1000)
print(account.public_var)     # OK
print(account._protected_var) # OK (but discouraged)
# print(account.__private_var)  # Error!
print(account._BankAccount__private_var)  # Name mangling workaround
```

## Properties

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
            raise ValueError("Below absolute zero!")
        self._celsius = value

    @celsius.deleter
    def celsius(self):
        """Delete temperature."""
        del self._celsius

    @property
    def fahrenheit(self):
        return self._celsius * 9/5 + 32

    @fahrenheit.setter
    def fahrenheit(self, value):
        self._celsius = (value - 32) * 5/9

temp = Temperature(25)
print(temp.celsius)    # 25
temp.fahrenheit = 100
print(temp.celsius)    # 37.77...
```

## Class Methods and Static Methods

```python
class MyClass:
    count = 0

    def __init__(self):
        MyClass.count += 1

    @classmethod
    def get_count(cls):
        """Class method - receives class as first argument."""
        return cls.count

    @classmethod
    def create_default(cls):
        """Factory method."""
        return cls()

    @staticmethod
    def utility_function(x, y):
        """Static method - no self or cls."""
        return x + y

# Usage
obj1 = MyClass()
obj2 = MyClass()
print(MyClass.get_count())  # 2
print(MyClass.utility_function(5, 3))  # 8
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

    def describe(self):
        """Concrete method."""
        return f"Area: {self.area()}, Perimeter: {self.perimeter()}"

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

Duck typing with type checking:

```python
from typing import Protocol

class Drawable(Protocol):
    def draw(self) -> None:
        ...

class Circle:
    def draw(self) -> None:
        print("Drawing circle")

class Square:
    def draw(self) -> None:
        print("Drawing square")

def render(obj: Drawable) -> None:
    """Works with any object that has draw()."""
    obj.draw()

render(Circle())  # OK
render(Square())  # OK
```

## Magic Methods (Dunder Methods)

### Object Initialization and Representation

```python
class Person:
    def __init__(self, name, age):
        """Constructor."""
        self.name = name
        self.age = age

    def __str__(self):
        """User-friendly string representation."""
        return f"{self.name}, {self.age} years old"

    def __repr__(self):
        """Developer-friendly representation."""
        return f"Person(name='{self.name}', age={self.age})"

    def __del__(self):
        """Destructor (rarely used)."""
        print(f"Deleting {self.name}")

p = Person("Alice", 30)
print(str(p))   # Alice, 30 years old
print(repr(p))  # Person(name='Alice', age=30)
```

### Comparison Operators

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __eq__(self, other):
        """Equal (==)."""
        return self.age == other.age

    def __lt__(self, other):
        """Less than (<)."""
        return self.age < other.age

    def __le__(self, other):
        """Less than or equal (<=)."""
        return self.age <= other.age

    def __gt__(self, other):
        """Greater than (>)."""
        return self.age > other.age

    def __ge__(self, other):
        """Greater than or equal (>=)."""
        return self.age >= other.age

p1 = Person("Alice", 25)
p2 = Person("Bob", 30)
print(p1 < p2)  # True
print(p1 == p2) # False
```

### Arithmetic Operators

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    def __sub__(self, other):
        return Vector(self.x - other.x, self.y - other.y)

    def __mul__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)

    def __str__(self):
        return f"Vector({self.x}, {self.y})"

v1 = Vector(1, 2)
v2 = Vector(3, 4)
print(v1 + v2)  # Vector(4, 6)
print(v1 * 2)   # Vector(2, 4)
```

### Container Methods

```python
class CustomList:
    def __init__(self, items):
        self.items = items

    def __len__(self):
        """len() function."""
        return len(self.items)

    def __getitem__(self, index):
        """Index access: obj[index]."""
        return self.items[index]

    def __setitem__(self, index, value):
        """Index assignment: obj[index] = value."""
        self.items[index] = value

    def __delitem__(self, index):
        """Delete item: del obj[index]."""
        del self.items[index]

    def __contains__(self, item):
        """Membership: item in obj."""
        return item in self.items

    def __iter__(self):
        """Iteration: for item in obj."""
        return iter(self.items)

cl = CustomList([1, 2, 3, 4, 5])
print(len(cl))      # 5
print(cl[0])        # 1
print(3 in cl)      # True
for item in cl:
    print(item)
```

### Context Manager

```python
class FileManager:
    def __init__(self, filename, mode):
        self.filename = filename
        self.mode = mode
        self.file = None

    def __enter__(self):
        """Called when entering 'with' block."""
        self.file = open(self.filename, self.mode)
        return self.file

    def __exit__(self, exc_type, exc_val, exc_tb):
        """Called when exiting 'with' block."""
        if self.file:
            self.file.close()
        return False  # Don't suppress exceptions

with FileManager("test.txt", "w") as f:
    f.write("Hello, World!")
# File automatically closed
```

### Callable Objects

```python
class Multiplier:
    def __init__(self, factor):
        self.factor = factor

    def __call__(self, x):
        """Make object callable like a function."""
        return x * self.factor

times_5 = Multiplier(5)
print(times_5(10))  # 50
```

## Composition over Inheritance

```python
class Engine:
    def start(self):
        return "Engine started"

class Wheels:
    def rotate(self):
        return "Wheels rotating"

class Car:
    def __init__(self):
        self.engine = Engine()  # Composition
        self.wheels = Wheels()

    def drive(self):
        return f"{self.engine.start()}, {self.wheels.rotate()}"

car = Car()
print(car.drive())  # Engine started, Wheels rotating
```

## Mixins

```python
class JSONMixin:
    def to_json(self):
        import json
        return json.dumps(self.__dict__)

class XMLMixin:
    def to_xml(self):
        return f"<data>{self.__dict__}</data>"

class User(JSONMixin, XMLMixin):
    def __init__(self, name, email):
        self.name = name
        self.email = email

user = User("Alice", "alice@example.com")
print(user.to_json())  # {"name": "Alice", "email": "alice@example.com"}
```

> **Good to know:** Python supports both class-based and prototype-based OOP. Use `super()` to call parent class methods. Single underscore `_protected` is convention only; double underscore `__private` triggers name mangling. Properties allow you to add logic to attribute access without changing the interface. Abstract base classes enforce interface contracts. Protocols provide structural subtyping (duck typing with type checking). Prefer composition over inheritance for flexibility. Magic methods let you customize object behavior for Python operators and built-in functions.

---

## References

- [Python - Classes](https://docs.python.org/3/tutorial/classes.html)
- [Python - Data Model (Magic Methods)](https://docs.python.org/3/reference/datamodel.html)
- [Python - ABC Module](https://docs.python.org/3/library/abc.html)
- [PEP 544 - Protocols](https://www.python.org/dev/peps/pep-0544/)
- [Real Python - Object-Oriented Programming in Python](https://realpython.com/python3-object-oriented-programming/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
