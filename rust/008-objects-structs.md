# Objects/Structs

Rust doesn't have classes like traditional OOP languages. Instead, it uses `structs` to create custom data types.

## Struct Definitions

### Classic Struct

```rust
// Define a struct
struct User {
    username: String,
    email: String,
    age: u32,
    active: bool,
}

// Create an instance
let user = User {
    username: String::from("alice"),
    email: String::from("alice@example.com"),
    age: 30,
    active: true,
};

// Access fields
println!("Username: {}", user.username);
```

### Mutable Structs

```rust
let mut user = User {
    username: String::from("bob"),
    email: String::from("bob@example.com"),
    age: 25,
    active: true,
};

// Modify fields (entire struct must be mutable)
user.age = 26;
user.active = false;
```

### Field Init Shorthand

```rust
fn build_user(email: String, username: String) -> User {
    User {
        email,    // Shorthand: same as email: email
        username, // Shorthand: same as username: username
        age: 30,
        active: true,
    }
}
```

### Struct Update Syntax

```rust
let user1 = User {
    username: String::from("user1"),
    email: String::from("user1@example.com"),
    age: 30,
    active: true,
};

// Create a new user with most fields from user1
let user2 = User {
    email: String::from("user2@example.com"),
    ..user1 // Copy remaining fields from user1
};

// Note: user1.username is moved to user2
// user1 can no longer be used (moved)
```

## Tuple Structs

```rust
// Struct without named fields
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);

// Access by index
println!("Red: {}", black.0);

// Color and Point are different types despite same structure
```

## Unit-Like Structs

```rust
// Struct with no fields (useful for traits)
struct AlwaysEqual;

let subject = AlwaysEqual;

// Used for marker types or implementing traits without data
```

## Methods

### Impl Blocks

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    // Method (takes &self)
    fn area(&self) -> u32 {
        self.width * self.height
    }

    // Method with multiple parameters
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }

    // Mutable method
    fn scale(&mut self, factor: u32) {
        self.width *= factor;
        self.height *= factor;
    }

    // Takes ownership (rare)
    fn consume(self) -> u32 {
        self.width * self.height
    }
}

// Usage
let rect = Rectangle { width: 30, height: 50 };
println!("Area: {}", rect.area());
```

## Associated Functions

```rust
impl Rectangle {
    // Associated function (no self) - like static methods
    fn square(size: u32) -> Rectangle {
        Rectangle {
            width: size,
            height: size,
        }
    }

    fn new(width: u32, height: u32) -> Rectangle {
        Rectangle { width, height }
    }
}

// Call with ::
let sq = Rectangle::square(10);
let rect = Rectangle::new(20, 30);
```

## Multiple Impl Blocks

```rust
struct Circle {
    radius: f64,
}

impl Circle {
    fn area(&self) -> f64 {
        std::f64::consts::PI * self.radius * self.radius
    }
}

impl Circle {
    fn diameter(&self) -> f64 {
        self.radius * 2.0
    }
}
```

## Deriving Traits

```rust
// Auto-implement common traits
#[derive(Debug, Clone, PartialEq)]
struct Point {
    x: i32,
    y: i32,
}

let p1 = Point { x: 10, y: 20 };
let p2 = p1.clone();

// Debug print
println!("{:?}", p1); // Point { x: 10, y: 20 }
println!("{:#?}", p1); // Pretty print

// Equality
assert_eq!(p1, p2);
```

## Common Derived Traits

```rust
// Copy: simple bitwise copy (for small types)
#[derive(Copy, Clone)]
struct Coordinates {
    x: i32,
    y: i32,
}

// Default: provides default values
#[derive(Default)]
struct Config {
    timeout: u32,    // 0
    retries: u32,    // 0
}

let config = Config::default();

// Hash: for use in HashMap/HashSet
#[derive(Hash)]
struct Key {
    id: u64,
}

// Ord, PartialOrd: for sorting
#[derive(PartialOrd, Ord, PartialEq, Eq)]
struct Priority {
    level: u32,
}
```

## Struct Visibility

```rust
// Public struct
pub struct PublicStruct {
    pub public_field: i32,     // Public field
    private_field: String,      // Private field (default)
}

impl PublicStruct {
    // Constructor for private fields
    pub fn new(public: i32, private: String) -> PublicStruct {
        PublicStruct {
            public_field: public,
            private_field: private,
        }
    }

    // Getter for private field
    pub fn get_private(&self) -> &str {
        &self.private_field
    }
}
```

## Structs with Lifetimes

```rust
// Struct holding references needs lifetime annotations
struct Article<'a> {
    title: &'a str,
    content: &'a str,
}

let title = String::from("Rust Guide");
let content = String::from("Content here");

let article = Article {
    title: &title,
    content: &content,
};
```

## Generic Structs

```rust
// Generic over type T
struct Point<T> {
    x: T,
    y: T,
}

let int_point = Point { x: 5, y: 10 };
let float_point = Point { x: 1.0, y: 4.0 };

// Multiple type parameters
struct Mixed<T, U> {
    x: T,
    y: U,
}

let mixed = Mixed { x: 5, y: 4.0 };
```

## Pattern Matching on Structs

```rust
struct Point {
    x: i32,
    y: i32,
}

let point = Point { x: 0, y: 7 };

match point {
    Point { x: 0, y } => println!("On the y axis at {}", y),
    Point { x, y: 0 } => println!("On the x axis at {}", x),
    Point { x, y } => println!("On neither axis: ({}, {})", x, y),
}

// Shorthand
let Point { x, y } = point;

// Ignore fields
let Point { x, .. } = point;
```

> **Good to know:** Unlike other languages, Rust structs don't have inheritance. Instead, use composition and traits for code reuse. All struct fields are private by default when defined in a module. Methods are defined in `impl` blocks, separate from the struct definition. You can have multiple `impl` blocks for the same struct.

---

## References

- [The Rust Book - Structs](https://doc.rust-lang.org/book/ch05-00-structs.html)
- [The Rust Book - Method Syntax](https://doc.rust-lang.org/book/ch05-03-method-syntax.html)
- [Rust By Example - Structs](https://doc.rust-lang.org/rust-by-example/custom_types/structs.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
