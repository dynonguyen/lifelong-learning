# Enum/Tuple

## Enums

Enums allow you to define a type by enumerating its possible variants.

### Basic Enums

```rust
// Simple enum
enum Direction {
    North,
    South,
    East,
    West,
}

let dir = Direction::North;

// With data
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);
let loopback = IpAddr::V6(String::from("::1"));
```

### Enums with Different Types

```rust
enum Message {
    Quit,                       // No data
    Move { x: i32, y: i32 },   // Struct-like
    Write(String),              // Tuple-like
    ChangeColor(i32, i32, i32), // Multiple values
}

// Create instances
let msg1 = Message::Quit;
let msg2 = Message::Move { x: 10, y: 20 };
let msg3 = Message::Write(String::from("hello"));
let msg4 = Message::ChangeColor(255, 0, 0);
```

### Pattern Matching with Enums

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

fn process_message(msg: Message) {
    match msg {
        Message::Quit => {
            println!("Quit received");
        }
        Message::Move { x, y } => {
            println!("Move to ({}, {})", x, y);
        }
        Message::Write(text) => {
            println!("Text: {}", text);
        }
        Message::ChangeColor(r, g, b) => {
            println!("Color: rgb({}, {}, {})", r, g, b);
        }
    }
}
```

### Enum Methods

```rust
enum Shape {
    Circle(f64),
    Rectangle(f64, f64),
}

impl Shape {
    fn area(&self) -> f64 {
        match self {
            Shape::Circle(radius) => {
                std::f64::consts::PI * radius * radius
            }
            Shape::Rectangle(width, height) => {
                width * height
            }
        }
    }
}

let circle = Shape::Circle(5.0);
println!("Area: {}", circle.area());
```

## Option Enum

Rust's way of handling optional values (no null).

### Option Basics

```rust
// Option is defined in std as:
// enum Option<T> {
//     Some(T),
//     None,
// }

let some_number: Option<i32> = Some(5);
let no_number: Option<i32> = None;

// Check if Some or None
if some_number.is_some() {
    println!("Has value");
}

if no_number.is_none() {
    println!("No value");
}
```

### Working with Option

```rust
let x: Option<i32> = Some(5);

// Unwrap (panics if None)
let value = x.unwrap();

// Unwrap or default
let value = x.unwrap_or(0);

// Unwrap or else
let value = x.unwrap_or_else(|| {
    // Compute default value
    0
});

// Expect (unwrap with custom panic message)
let value = x.expect("Value should exist");

// Pattern matching
match x {
    Some(value) => println!("Value: {}", value),
    None => println!("No value"),
}

// if let
if let Some(value) = x {
    println!("Value: {}", value);
}
```

### Option Methods

```rust
let x = Some(5);

// Map: transform the value if Some
let y = x.map(|n| n * 2); // Some(10)

// And_then: chain operations
let result = x.and_then(|n| {
    if n > 0 {
        Some(n * 2)
    } else {
        None
    }
});

// Filter
let filtered = x.filter(|&n| n > 3); // Some(5)

// Or: use alternative if None
let a: Option<i32> = None;
let b = a.or(Some(10)); // Some(10)

// Take: takes value, leaving None
let mut x = Some(5);
let y = x.take(); // y = Some(5), x = None
```

## Result Enum

For operations that can fail.

### Result Basics

```rust
// Result is defined in std as:
// enum Result<T, E> {
//     Ok(T),
//     Err(E),
// }

fn divide(a: i32, b: i32) -> Result<i32, String> {
    if b == 0 {
        Err(String::from("Division by zero"))
    } else {
        Ok(a / b)
    }
}

let result = divide(10, 2); // Ok(5)
let error = divide(10, 0);  // Err("Division by zero")
```

### Working with Result

```rust
let result = divide(10, 2);

// Unwrap (panics if Err)
let value = result.unwrap();

// Unwrap or default
let value = result.unwrap_or(0);

// Expect (unwrap with message)
let value = result.expect("Division failed");

// Pattern matching
match result {
    Ok(value) => println!("Result: {}", value),
    Err(e) => println!("Error: {}", e),
}

// ? operator (propagate error)
fn calculate() -> Result<i32, String> {
    let x = divide(10, 2)?; // Returns early if Err
    let y = divide(x, 2)?;
    Ok(y)
}
```

### Result Methods

```rust
let result: Result<i32, String> = Ok(5);

// Map: transform Ok value
let doubled = result.map(|n| n * 2); // Ok(10)

// Map_err: transform Err value
let mapped_err = result.map_err(|e| format!("Error: {}", e));

// And_then: chain operations
let chained = result.and_then(|n| {
    if n > 0 {
        Ok(n * 2)
    } else {
        Err(String::from("negative"))
    }
});

// Or: use alternative if Err
let error_result: Result<i32, String> = Err(String::from("error"));
let alternative = error_result.or(Ok(0)); // Ok(0)
```

## Tuples

Fixed-size collections of values of different types.

### Tuple Basics

```rust
// Create tuple
let tuple: (i32, f64, char) = (42, 3.14, 'x');

// Access elements
let first = tuple.0;
let second = tuple.1;
let third = tuple.2;

// Destructuring
let (x, y, z) = tuple;
println!("x: {}, y: {}, z: {}", x, y, z);

// Ignore values with _
let (a, _, c) = tuple;

// Unit tuple (empty)
let unit: () = ();
```

### Tuple as Return Value

```rust
// Return multiple values
fn split_name(full_name: &str) -> (String, String) {
    let parts: Vec<&str> = full_name.split_whitespace().collect();
    (
        parts[0].to_string(),
        parts.get(1).unwrap_or(&"").to_string(),
    )
}

let (first, last) = split_name("John Doe");
```

### Tuple Structs

```rust
// Named tuple types
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);

// Access like tuples
println!("Red: {}", black.0);

// Destructure
let Color(r, g, b) = black;
```

## Advanced Pattern Matching

### Match Guards

```rust
let num = Some(4);

match num {
    Some(x) if x < 5 => println!("Less than 5: {}", x),
    Some(x) => println!("Greater or equal to 5: {}", x),
    None => println!("No value"),
}
```

### Binding Values

```rust
enum Message {
    Hello { id: i32 },
}

let msg = Message::Hello { id: 5 };

match msg {
    Message::Hello { id: id_var @ 3..=7 } => {
        println!("Found id in range: {}", id_var);
    }
    Message::Hello { id: 10..=12 } => {
        println!("Found id in another range");
    }
    Message::Hello { id } => {
        println!("Found other id: {}", id);
    }
}
```

### If Let and While Let

```rust
let some_value = Some(3);

// if let: pattern match with single case
if let Some(3) = some_value {
    println!("three");
}

// while let: loop while pattern matches
let mut stack = vec![1, 2, 3];
while let Some(top) = stack.pop() {
    println!("{}", top);
}
```

### Matching Multiple Patterns

```rust
let x = 1;

match x {
    1 | 2 => println!("one or two"),
    3..=5 => println!("three through five"),
    _ => println!("anything else"),
}
```

## Deriving Traits for Enums

```rust
#[derive(Debug, Clone, Copy, PartialEq)]
enum Status {
    Active,
    Inactive,
    Pending,
}

let status = Status::Active;
println!("{:?}", status);

let status2 = status; // Copy
assert_eq!(status, status2); // PartialEq
```

> **Good to know:** Enums in Rust are more powerful than in many languages - they can hold data of different types. `Option` and `Result` are enums that replace null values and exceptions. Always use pattern matching with enums to ensure all variants are handled. The compiler will warn if you miss a variant. Tuples are useful for returning multiple values without defining a struct.

---

## References

- [The Rust Book - Enums](https://doc.rust-lang.org/book/ch06-00-enums.html)
- [The Rust Book - Pattern Matching](https://doc.rust-lang.org/book/ch18-00-patterns.html)
- [Rust By Example - Enums](https://doc.rust-lang.org/rust-by-example/custom_types/enum.html)
- [std::option::Option](https://doc.rust-lang.org/std/option/enum.Option.html)
- [std::result::Result](https://doc.rust-lang.org/std/result/enum.Result.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
