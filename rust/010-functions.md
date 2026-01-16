# Functions/Macros

## Function Basics

### Function Declaration

```rust
// Basic function
fn greet() {
    println!("Hello!");
}

// With parameters
fn add(a: i32, b: i32) -> i32 {
    a + b  // No semicolon = return value
}

// Explicit return
fn subtract(a: i32, b: i32) -> i32 {
    return a - b;  // Early return
}

// Multiple parameters and return
fn calculate(x: i32, y: i32) -> (i32, i32) {
    (x + y, x * y)
}
```

### Parameters

```rust
// By value (takes ownership)
fn take_ownership(s: String) {
    println!("{}", s);
} // s dropped here

// By reference (borrowing)
fn borrow(s: &String) {
    println!("{}", s);
}

// Mutable reference
fn modify(s: &mut String) {
    s.push_str(" world");
}

// Multiple parameters
fn multi_params(x: i32, y: i32, z: i32) -> i32 {
    x + y + z
}
```

### Return Values

```rust
// Implicit return (no semicolon)
fn implicit_return() -> i32 {
    42
}

// Explicit return
fn explicit_return() -> i32 {
    return 42;
}

// Early return
fn early_return(x: i32) -> String {
    if x < 0 {
        return String::from("negative");
    }
    String::from("positive")
}

// Unit return type ()
fn no_return() {
    println!("Returns unit type ()");
}

// Multiple return values (tuple)
fn divide(a: i32, b: i32) -> Result<i32, String> {
    if b == 0 {
        Err(String::from("Division by zero"))
    } else {
        Ok(a / b)
    }
}
```

## Closures

Anonymous functions that can capture their environment.

### Closure Syntax

```rust
// Simple closure
let add = |a, b| a + b;
let result = add(2, 3); // 5

// With type annotations
let multiply = |a: i32, b: i32| -> i32 {
    a * b
};

// Multi-line closure
let complex = |x: i32| {
    let y = x * 2;
    let z = y + 1;
    z
};
```

### Capturing Environment

```rust
let x = 10;

// Capture by immutable borrow
let print_x = || println!("x is {}", x);
print_x();

// Capture by mutable borrow
let mut count = 0;
let mut increment = || {
    count += 1;
    count
};
println!("{}", increment()); // 1

// Capture by move (takes ownership)
let s = String::from("hello");
let consume = move || {
    println!("{}", s);
}; // s moved into closure
consume();
// println!("{}", s); // ERROR: s moved
```

### Closure Traits

```rust
// Fn: borrows immutably
fn call_fn<F>(f: F) where F: Fn() {
    f();
}

// FnMut: borrows mutably
fn call_fn_mut<F>(mut f: F) where F: FnMut() {
    f();
}

// FnOnce: takes ownership
fn call_fn_once<F>(f: F) where F: FnOnce() {
    f();
}
```

### Using Closures

```rust
// With iterators
let numbers = vec![1, 2, 3, 4, 5];

let doubled: Vec<i32> = numbers.iter()
    .map(|x| x * 2)
    .collect();

let evens: Vec<i32> = numbers.iter()
    .filter(|&&x| x % 2 == 0)
    .copied()
    .collect();

// As function parameters
fn apply_operation<F>(x: i32, f: F) -> i32
where
    F: Fn(i32) -> i32,
{
    f(x)
}

let result = apply_operation(5, |x| x * 2); // 10
```

## Higher-Order Functions

```rust
// Function returning function
fn multiplier(factor: i32) -> impl Fn(i32) -> i32 {
    move |x| x * factor
}

let times_two = multiplier(2);
println!("{}", times_two(5)); // 10

// Function accepting function
fn apply_twice<F>(f: F, x: i32) -> i32
where
    F: Fn(i32) -> i32,
{
    f(f(x))
}

let double = |x| x * 2;
let result = apply_twice(double, 3); // 12
```

## Function Pointers

```rust
// Function pointer type: fn
fn add_one(x: i32) -> i32 {
    x + 1
}

let f: fn(i32) -> i32 = add_one;
let result = f(5); // 6

// Pass function pointer
fn apply(f: fn(i32) -> i32, x: i32) -> i32 {
    f(x)
}

apply(add_one, 10); // 11
```

## Macros

Code that writes code, identified by `!` suffix.

### Declarative Macros

```rust
// Define a macro
macro_rules! say_hello {
    () => {
        println!("Hello!");
    };
}

say_hello!(); // Hello!

// With arguments
macro_rules! create_function {
    ($func_name:ident) => {
        fn $func_name() {
            println!("Function {:?} called", stringify!($func_name));
        }
    };
}

create_function!(foo);
foo(); // Function "foo" called

// Pattern matching in macros
macro_rules! vec_of_strings {
    ($($element:expr),*) => {
        {
            let mut v = Vec::new();
            $(v.push($element.to_string());)*
            v
        }
    };
}

let v = vec_of_strings!["hello", "world"];
```

### Common Built-in Macros

```rust
// println! / print! / eprintln!
println!("Hello, {}!", "world");

// vec! - create vector
let v = vec![1, 2, 3, 4, 5];

// format! - format string
let s = format!("The answer is {}", 42);

// dbg! - debug print with location
let x = dbg!(5 * 2); // Prints: [src/main.rs:2] 5 * 2 = 10

// panic! - panic with message
panic!("Something went wrong!");

// assert! / assert_eq! / assert_ne!
assert!(true);
assert_eq!(2 + 2, 4);
assert_ne!(1, 2);

// matches! - pattern matching
let x = Some(5);
assert!(matches!(x, Some(5)));

// todo! - mark unimplemented code
fn not_yet_implemented() {
    todo!("Implement this later");
}

// unimplemented! - similar to todo
fn also_not_done() {
    unimplemented!();
}

// unreachable! - mark unreachable code
fn never_reaches() {
    unreachable!();
}
```

### Procedural Macros

```rust
// Custom derive
#[derive(Debug, Clone)]
struct Point {
    x: i32,
    y: i32,
}

// Attribute-like macros
#[route(GET, "/")]
fn index() {}

// Function-like macros
sql!(SELECT * FROM users);
```

## Method Syntax

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    // Associated function (no self)
    fn new(width: u32, height: u32) -> Rectangle {
        Rectangle { width, height }
    }

    // Method (&self)
    fn area(&self) -> u32 {
        self.width * self.height
    }

    // Mutable method (&mut self)
    fn resize(&mut self, width: u32, height: u32) {
        self.width = width;
        self.height = height;
    }

    // Consuming method (self)
    fn into_square(self) -> Rectangle {
        let size = self.width.max(self.height);
        Rectangle::new(size, size)
    }
}

let mut rect = Rectangle::new(10, 20);
println!("Area: {}", rect.area());
rect.resize(30, 40);
```

## Generic Functions

```rust
// Generic function
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
let result = largest(&numbers);

// Multiple type parameters
fn pair<T, U>(first: T, second: U) -> (T, U) {
    (first, second)
}
```

> **Good to know:** Closures can capture variables from their environment, while regular functions cannot. Use `move` keyword to force closures to take ownership of captured variables. Macros are expanded at compile time, making them zero-cost abstractions. The `!` suffix distinguishes macro invocations from function calls. Functions are expressions in Rust - omitting the semicolon on the last line returns that value.

---

## References

- [The Rust Book - Functions](https://doc.rust-lang.org/book/ch03-03-how-functions-work.html)
- [The Rust Book - Closures](https://doc.rust-lang.org/book/ch13-01-closures.html)
- [The Rust Book - Macros](https://doc.rust-lang.org/book/ch19-06-macros.html)
- [Rust By Example - Functions](https://doc.rust-lang.org/rust-by-example/fn.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
