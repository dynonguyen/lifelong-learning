# Generics

Generics enable code reuse by allowing types to be parameters.

## Generic Functions

### Basic Generic Function

```rust
// Single generic type
fn largest<T: PartialOrd>(list: &[T]) -> &T {
    let mut largest = &list[0];
    for item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}

let numbers = vec![34, 50, 25, 100, 65];
let result = largest(&numbers); // 100

let chars = vec!['y', 'm', 'a', 'q'];
let result = largest(&chars); // 'y'
```

### Multiple Type Parameters

```rust
fn mix<T, U>(a: T, b: U) -> (T, U) {
    (a, b)
}

let result = mix(5, "hello");
// result: (i32, &str) = (5, "hello")
```

### Generic Function with Constraints

```rust
use std::fmt::Display;

fn print_pair<T: Display, U: Display>(a: T, b: U) {
    println!("a = {}, b = {}", a, b);
}

print_pair(5, "hello");
```

## Generic Structs

### Basic Generic Struct

```rust
struct Point<T> {
    x: T,
    y: T,
}

let integer_point = Point { x: 5, y: 10 };
let float_point = Point { x: 1.0, y: 4.0 };
```

### Multiple Type Parameters

```rust
struct Point<T, U> {
    x: T,
    y: U,
}

let mixed = Point { x: 5, y: 4.0 };
// x: i32, y: f64
```

### Methods on Generic Structs

```rust
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }

    fn new(x: T, y: T) -> Self {
        Point { x, y }
    }
}

// Methods for specific types
impl Point<f32> {
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}

let p = Point::new(5, 10);
println!("x = {}", p.x());
```

### Mixed Generic Methods

```rust
struct Point<T, U> {
    x: T,
    y: U,
}

impl<T, U> Point<T, U> {
    fn mixup<V, W>(self, other: Point<V, W>) -> Point<T, W> {
        Point {
            x: self.x,
            y: other.y,
        }
    }
}

let p1 = Point { x: 5, y: 10.4 };
let p2 = Point { x: "Hello", y: 'c' };
let p3 = p1.mixup(p2);
// p3: Point<i32, char> = Point { x: 5, y: 'c' }
```

## Generic Enums

```rust
// Option and Result are generic enums
enum Option<T> {
    Some(T),
    None,
}

enum Result<T, E> {
    Ok(T),
    Err(E),
}

// Custom generic enum
enum Either<L, R> {
    Left(L),
    Right(R),
}

let left: Either<i32, String> = Either::Left(42);
let right: Either<i32, String> = Either::Right(String::from("hello"));
```

## Trait Bounds

### Single Trait Bound

```rust
use std::fmt::Display;

fn print_it<T: Display>(item: T) {
    println!("{}", item);
}
```

### Multiple Trait Bounds

```rust
use std::fmt::{Display, Debug};

// Using +
fn print_both<T: Display + Debug>(item: T) {
    println!("Display: {}", item);
    println!("Debug: {:?}", item);
}

// Using where clause (cleaner)
fn print_both_where<T>(item: T)
where
    T: Display + Debug,
{
    println!("Display: {}", item);
    println!("Debug: {:?}", item);
}
```

### Complex Where Clauses

```rust
use std::fmt::Display;

fn complex_function<T, U>(t: &T, u: &U) -> i32
where
    T: Display + Clone,
    U: Clone + Debug,
{
    println!("t = {}", t);
    println!("u = {:?}", u);
    42
}
```

## Returning Generic Types

### Impl Trait

```rust
use std::fmt::Display;

// Return any type that implements Display
fn returns_displayable() -> impl Display {
    42
}

// Works with iterators
fn doubles(v: Vec<i32>) -> impl Iterator<Item = i32> {
    v.into_iter().map(|x| x * 2)
}

// Can't return different types
// fn returns_either(flag: bool) -> impl Display {
//     if flag {
//         42      // ERROR: different types
//     } else {
//         "hello" // ERROR: different types
//     }
// }
```

### Trait Objects

```rust
use std::fmt::Display;

// Return trait object for different types
fn returns_displayable(flag: bool) -> Box<dyn Display> {
    if flag {
        Box::new(42)
    } else {
        Box::new("hello")
    }
}
```

## Generic Traits

```rust
trait Container<T> {
    fn add(&mut self, item: T);
    fn get(&self, index: usize) -> Option<&T>;
}

struct VecContainer<T> {
    items: Vec<T>,
}

impl<T> Container<T> for VecContainer<T> {
    fn add(&mut self, item: T) {
        self.items.push(item);
    }

    fn get(&self, index: usize) -> Option<&T> {
        self.items.get(index)
    }
}
```

## Associated Types

### Basic Associated Type

```rust
trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;
}

struct Counter {
    count: u32,
}

impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        self.count += 1;
        if self.count < 6 {
            Some(self.count)
        } else {
            None
        }
    }
}
```

### Generic vs Associated Types

```rust
// Generic trait: can implement multiple times
trait From<T> {
    fn from(value: T) -> Self;
}

// Associated type: one implementation per type
trait Into {
    type Output;
    fn into(self) -> Self::Output;
}
```

## Lifetimes with Generics

### Generic Struct with Lifetime

```rust
struct Wrapper<'a, T> {
    reference: &'a T,
}

impl<'a, T> Wrapper<'a, T> {
    fn new(reference: &'a T) -> Self {
        Wrapper { reference }
    }

    fn get(&self) -> &T {
        self.reference
    }
}
```

### Generic Function with Lifetime

```rust
fn longest<'a, T: AsRef<str>>(x: &'a T, y: &'a T) -> &'a str {
    let x_str = x.as_ref();
    let y_str = y.as_ref();

    if x_str.len() > y_str.len() {
        x_str
    } else {
        y_str
    }
}
```

## Const Generics

### Array with Const Generic

```rust
// Const generic for array size
fn print_array<T: std::fmt::Debug, const N: usize>(arr: [T; N]) {
    println!("{:?}", arr);
}

print_array([1, 2, 3]);
print_array([1, 2, 3, 4, 5]);

// Generic struct with const
struct Buffer<T, const SIZE: usize> {
    data: [T; SIZE],
}

impl<T: Default + Copy, const SIZE: usize> Buffer<T, SIZE> {
    fn new() -> Self {
        Buffer {
            data: [T::default(); SIZE],
        }
    }
}

let buffer: Buffer<i32, 10> = Buffer::new();
```

## Advanced Generic Patterns

### Blanket Implementations

```rust
trait Printable {
    fn print(&self);
}

// Implement for all types that implement Display
impl<T: std::fmt::Display> Printable for T {
    fn print(&self) {
        println!("{}", self);
    }
}

// Now all Display types have print()
42.print();
"hello".print();
```

### Turbofish Syntax

```rust
// Explicitly specify type parameters
let numbers = "1 2 3 4"
    .split_whitespace()
    .map(|s| s.parse::<i32>())
    .collect::<Vec<_>>();

// Alternative
let numbers: Vec<i32> = "1 2 3 4"
    .split_whitespace()
    .map(|s| s.parse().unwrap())
    .collect();
```

### PhantomData

```rust
use std::marker::PhantomData;

struct Wrapper<T> {
    _marker: PhantomData<T>,
}

impl<T> Wrapper<T> {
    fn new() -> Self {
        Wrapper {
            _marker: PhantomData,
        }
    }
}

let wrapper: Wrapper<String> = Wrapper::new();
```

## Generic Type Defaults

```rust
trait Add<RHS = Self> {
    type Output;
    fn add(self, rhs: RHS) -> Self::Output;
}

// Default generic parameter
struct Point<T = f64> {
    x: T,
    y: T,
}

let float_point = Point { x: 1.0, y: 2.0 }; // Point<f64>
let int_point = Point::<i32> { x: 1, y: 2 }; // Point<i32>
```

## Performance

```rust
// Generics have zero runtime cost
// This:
fn add_generic<T: std::ops::Add<Output = T>>(a: T, b: T) -> T {
    a + b
}

// Is monomorphized at compile time to:
// fn add_i32(a: i32, b: i32) -> i32 { a + b }
// fn add_f64(a: f64, b: f64) -> f64 { a + b }
// etc.

// Each type gets its own specialized version
```

> **Good to know:** Generics in Rust are zero-cost abstractions - they're resolved at compile time through monomorphization. Use trait bounds to constrain generic types. Associated types are cleaner when there's only one obvious type choice. Const generics allow compile-time array sizes. The compiler generates specialized code for each concrete type, so binary size can increase with many generic instantiations.

---

## References

- [The Rust Book - Generic Types](https://doc.rust-lang.org/book/ch10-00-generics.html)
- [The Rust Book - Traits](https://doc.rust-lang.org/book/ch10-02-traits.html)
- [Rust Reference - Generics](https://doc.rust-lang.org/reference/items/generics.html)
- [Rust By Example - Generics](https://doc.rust-lang.org/rust-by-example/generics.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
