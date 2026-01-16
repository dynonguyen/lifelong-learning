# OOP/Interfaces/Methods

Rust doesn't have traditional OOP inheritance, but it provides powerful alternatives through traits, methods, and composition.

## Traits (Interfaces)

Traits define shared behavior similar to interfaces in other languages.

### Defining Traits

```rust
// Basic trait definition
trait Printable {
    fn print(&self);
}

// Trait with multiple methods
trait Shape {
    fn area(&self) -> f64;
    fn perimeter(&self) -> f64;

    // Default implementation
    fn describe(&self) {
        println!("Area: {}, Perimeter: {}", self.area(), self.perimeter());
    }
}
```

### Implementing Traits

```rust
struct Circle {
    radius: f64,
}

impl Shape for Circle {
    fn area(&self) -> f64 {
        std::f64::consts::PI * self.radius * self.radius
    }

    fn perimeter(&self) -> f64 {
        2.0 * std::f64::consts::PI * self.radius
    }

    // Can override default implementation
    fn describe(&self) {
        println!("Circle: area={:.2}, perimeter={:.2}",
                 self.area(), self.perimeter());
    }
}

struct Rectangle {
    width: f64,
    height: f64,
}

impl Shape for Rectangle {
    fn area(&self) -> f64 {
        self.width * self.height
    }

    fn perimeter(&self) -> f64 {
        2.0 * (self.width + self.height)
    }
    // Uses default describe implementation
}
```

## Methods vs Associated Functions

```rust
struct Counter {
    count: i32,
}

impl Counter {
    // Associated function (no self) - like static methods
    fn new() -> Counter {
        Counter { count: 0 }
    }

    // Method with immutable self
    fn value(&self) -> i32 {
        self.count
    }

    // Method with mutable self
    fn increment(&mut self) {
        self.count += 1;
    }

    // Method that consumes self
    fn consume(self) -> i32 {
        self.count
    }
}

// Usage
let mut counter = Counter::new();  // Associated function with ::
counter.increment();                // Method with .
println!("{}", counter.value());
```

## Trait Bounds

```rust
// Generic function with trait bound
fn print_area<T: Shape>(shape: &T) {
    println!("Area: {}", shape.area());
}

// Multiple trait bounds
fn print_and_clone<T: Shape + Clone>(shape: &T) {
    let cloned = shape.clone();
    println!("Area: {}", shape.area());
}

// where clause (cleaner for multiple bounds)
fn complex<T, U>(t: &T, u: &U) -> f64
where
    T: Shape + Clone,
    U: Shape + std::fmt::Display,
{
    t.area() + u.area()
}
```

## Trait Objects (Dynamic Dispatch)

```rust
// Box<dyn Trait> for trait objects
fn draw_shape(shape: &dyn Shape) {
    shape.describe();
}

// Vector of trait objects
let shapes: Vec<Box<dyn Shape>> = vec![
    Box::new(Circle { radius: 5.0 }),
    Box::new(Rectangle { width: 10.0, height: 20.0 }),
];

for shape in &shapes {
    shape.describe();
}
```

## Returning Trait Objects

```rust
// Return impl Trait (static dispatch, faster)
fn create_shape(is_circle: bool) -> impl Shape {
    // ERROR: all branches must return same type
    // if is_circle {
    //     Circle { radius: 5.0 }
    // } else {
    //     Rectangle { width: 10.0, height: 20.0 }
    // }
    Circle { radius: 5.0 }
}

// Return Box<dyn Trait> (dynamic dispatch)
fn create_any_shape(is_circle: bool) -> Box<dyn Shape> {
    if is_circle {
        Box::new(Circle { radius: 5.0 })
    } else {
        Box::new(Rectangle { width: 10.0, height: 20.0 })
    }
}
```

## Deriving Common Traits

```rust
// Auto-implement standard traits
#[derive(Debug, Clone, PartialEq, Eq, Hash)]
struct Point {
    x: i32,
    y: i32,
}

// Debug: for {:?} formatting
// Clone: for .clone()
// PartialEq/Eq: for == comparison
// Hash: for use in HashMap/HashSet
```

## Common Standard Traits

### Display and Debug

```rust
use std::fmt;

struct Person {
    name: String,
    age: u32,
}

// Display trait for user-facing output
impl fmt::Display for Person {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "{} (age {})", self.name, self.age)
    }
}

// Debug trait (can also derive)
impl fmt::Debug for Person {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        f.debug_struct("Person")
            .field("name", &self.name)
            .field("age", &self.age)
            .finish()
    }
}
```

### Clone and Copy

```rust
// Clone: explicit deep copy
#[derive(Clone)]
struct Data {
    values: Vec<i32>,
}

let data1 = Data { values: vec![1, 2, 3] };
let data2 = data1.clone();

// Copy: implicit bitwise copy (only for simple types)
#[derive(Copy, Clone)]
struct Point {
    x: i32,
    y: i32,
}

let p1 = Point { x: 1, y: 2 };
let p2 = p1; // Copied, not moved
println!("{}", p1.x); // p1 still valid
```

### Default

```rust
#[derive(Default)]
struct Config {
    timeout: u32,
    retries: u32,
}

let config = Config::default();
// timeout = 0, retries = 0

// Custom Default implementation
impl Default for Config {
    fn default() -> Self {
        Config {
            timeout: 30,
            retries: 3,
        }
    }
}
```

### Iterator

```rust
struct Counter {
    current: u32,
    max: u32,
}

impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        if self.current < self.max {
            self.current += 1;
            Some(self.current)
        } else {
            None
        }
    }
}

let counter = Counter { current: 0, max: 5 };
for num in counter {
    println!("{}", num); // 1, 2, 3, 4, 5
}
```

### From and Into

```rust
// Conversion traits
struct Celsius(f64);
struct Fahrenheit(f64);

impl From<Fahrenheit> for Celsius {
    fn from(f: Fahrenheit) -> Self {
        Celsius((f.0 - 32.0) * 5.0 / 9.0)
    }
}

// Into is automatically implemented
let f = Fahrenheit(98.6);
let c: Celsius = f.into();
```

## Operator Overloading

```rust
use std::ops::Add;

#[derive(Debug, Clone, Copy)]
struct Point {
    x: i32,
    y: i32,
}

impl Add for Point {
    type Output = Point;

    fn add(self, other: Point) -> Point {
        Point {
            x: self.x + other.x,
            y: self.y + other.y,
        }
    }
}

let p1 = Point { x: 1, y: 2 };
let p2 = Point { x: 3, y: 4 };
let p3 = p1 + p2; // Point { x: 4, y: 6 }
```

## Supertraits

```rust
// Trait that depends on another trait
trait Printable {
    fn print(&self);
}

trait PrettyPrintable: Printable {
    fn pretty_print(&self);
}

// Must implement Printable to implement PrettyPrintable
impl Printable for String {
    fn print(&self) {
        println!("{}", self);
    }
}

impl PrettyPrintable for String {
    fn pretty_print(&self) {
        println!("*** {} ***", self);
    }
}
```

## Associated Types

```rust
trait Container {
    type Item;

    fn add(&mut self, item: Self::Item);
    fn get(&self) -> Option<&Self::Item>;
}

struct IntContainer {
    value: Option<i32>,
}

impl Container for IntContainer {
    type Item = i32;

    fn add(&mut self, item: i32) {
        self.value = Some(item);
    }

    fn get(&self) -> Option<&i32> {
        self.value.as_ref()
    }
}
```

## Extension Traits

```rust
// Add methods to existing types
trait StringExt {
    fn first_char(&self) -> Option<char>;
}

impl StringExt for String {
    fn first_char(&self) -> Option<char> {
        self.chars().next()
    }
}

impl StringExt for &str {
    fn first_char(&self) -> Option<char> {
        self.chars().next()
    }
}

let s = String::from("hello");
println!("{:?}", s.first_char()); // Some('h')
```

## Composition over Inheritance

```rust
// Use composition instead of inheritance
struct Engine {
    horsepower: u32,
}

impl Engine {
    fn start(&self) {
        println!("Engine started: {} HP", self.horsepower);
    }
}

struct Car {
    engine: Engine,
    model: String,
}

impl Car {
    fn start(&self) {
        self.engine.start();
        println!("Car {} is ready", self.model);
    }
}

let car = Car {
    engine: Engine { horsepower: 200 },
    model: String::from("Sedan"),
};
car.start();
```

> **Good to know:** Rust uses composition and traits instead of class inheritance. Traits can have default method implementations. Use `impl Trait` for static dispatch (faster) and `Box<dyn Trait>` for dynamic dispatch (flexible). Trait objects require the trait to be "object-safe" - methods can't use generics or return `Self`.

---

## References

- [The Rust Book - Traits](https://doc.rust-lang.org/book/ch10-02-traits.html)
- [The Rust Book - Trait Objects](https://doc.rust-lang.org/book/ch17-02-trait-objects.html)
- [Rust Reference - Traits](https://doc.rust-lang.org/reference/items/traits.html)
- [Rust By Example - Traits](https://doc.rust-lang.org/rust-by-example/trait.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
