# Coding Conventions/Styles

Rust has strong community conventions for writing idiomatic code.

## Naming Conventions

### General Rules

```rust
// Types: UpperCamelCase
struct UserAccount {}
enum HttpStatus {}
type UserId = u64;

// Functions, methods, variables: snake_case
fn calculate_total() {}
let user_name = "Alice";
let mut counter = 0;

// Constants and statics: SCREAMING_SNAKE_CASE
const MAX_RETRIES: u32 = 3;
static GLOBAL_CONFIG: &str = "config";

// Lifetimes: lowercase, short
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

// Type parameters: Single uppercase letter or UpperCamelCase
fn print<T: Display>(value: T) {}
fn convert<TInput, TOutput>(input: TInput) -> TOutput {}

// Modules: snake_case
mod user_management {}
mod http_client {}
```

### Getter/Setter Convention

```rust
pub struct User {
    name: String,
    age: u32,
}

impl User {
    // Getter: use field name
    pub fn name(&self) -> &str {
        &self.name
    }

    pub fn age(&self) -> u32 {
        self.age
    }

    // Setter: set_ prefix
    pub fn set_name(&mut self, name: String) {
        self.name = name;
    }

    // Builder pattern (preferred over setters)
    pub fn with_name(mut self, name: String) -> Self {
        self.name = name;
        self
    }
}
```

## Code Formatting

### Using rustfmt

```bash
# Format all code
cargo fmt

# Check formatting without changing
cargo fmt -- --check

# Format specific file
rustfmt src/main.rs
```

### rustfmt.toml

```toml
# rustfmt.toml
max_width = 100
hard_tabs = false
tab_spaces = 4
newline_style = "Unix"
use_small_heuristics = "Default"
reorder_imports = true
reorder_modules = true
remove_nested_parens = true
edition = "2021"
```

### Formatting Examples

```rust
// Good: Clear, readable
fn calculate_sum(numbers: &[i32]) -> i32 {
    numbers.iter().sum()
}

// Good: Breaking long function calls
let result = some_function(
    first_argument,
    second_argument,
    third_argument,
);

// Good: Breaking long chains
let result = data
    .iter()
    .filter(|x| x.is_valid())
    .map(|x| x.value())
    .collect();

// Good: Struct initialization
let user = User {
    name: String::from("Alice"),
    age: 30,
    email: String::from("alice@example.com"),
};
```

## Linting with Clippy

### Using Clippy

```bash
# Install clippy
rustup component add clippy

# Run clippy
cargo clippy

# Fix automatically where possible
cargo clippy --fix

# Strict mode
cargo clippy -- -D warnings
```

### Common Clippy Lints

```rust
// Avoid: Using clone unnecessarily
fn process(s: &String) {
    let owned = s.clone(); // Clippy warns
    // ...
}

// Better: Use reference
fn process(s: &str) {
    // Work with reference directly
}

// Avoid: Manual implementation
let s = format!("{}", value);

// Better: Use to_string
let s = value.to_string();

// Avoid: Comparing with bool
if some_bool == true {  // Clippy warns
    // ...
}

// Better: Direct comparison
if some_bool {
    // ...
}

// Avoid: Using match for simple case
let value = match option {
    Some(v) => v,
    None => default,
};

// Better: Use unwrap_or
let value = option.unwrap_or(default);
```

### Allowing Specific Lints

```rust
// Allow for entire module
#![allow(clippy::too_many_arguments)]

// Allow for specific item
#[allow(clippy::needless_return)]
fn explicit_return() -> i32 {
    return 42;
}

// Warn instead of error
#[warn(clippy::clone_on_copy)]
fn some_function() {}
```

## Error Handling Style

### Prefer Result Over Panic

```rust
// Avoid: Panicking in library code
pub fn divide(a: i32, b: i32) -> i32 {
    if b == 0 {
        panic!("Division by zero"); // Bad for libraries
    }
    a / b
}

// Better: Return Result
pub fn divide(a: i32, b: i32) -> Result<i32, String> {
    if b == 0 {
        Err(String::from("Division by zero"))
    } else {
        Ok(a / b)
    }
}
```

### Use ? Operator

```rust
// Avoid: Manual error handling
fn read_file(path: &str) -> Result<String, std::io::Error> {
    let result = std::fs::read_to_string(path);
    match result {
        Ok(contents) => Ok(contents),
        Err(e) => Err(e),
    }
}

// Better: Use ? operator
fn read_file(path: &str) -> Result<String, std::io::Error> {
    let contents = std::fs::read_to_string(path)?;
    Ok(contents)
}

// Even better: Return directly
fn read_file(path: &str) -> Result<String, std::io::Error> {
    std::fs::read_to_string(path)
}
```

## Documentation Style

### Module-Level Documentation

````rust
//! This module provides utilities for working with users.
//!
//! # Examples
//!
//! ```
//! use my_crate::users::User;
//!
//! let user = User::new("Alice");
//! ```

/// Represents a user in the system.
///
/// # Examples
///
/// ```
/// # use my_crate::User;
/// let user = User::new("Alice");
/// assert_eq!(user.name(), "Alice");
/// ```
pub struct User {
    name: String,
}
````

### Function Documentation

````rust
/// Calculates the factorial of a number.
///
/// # Arguments
///
/// * `n` - The number to calculate factorial for
///
/// # Returns
///
/// The factorial of `n`
///
/// # Examples
///
/// ```
/// # use my_crate::factorial;
/// assert_eq!(factorial(5), 120);
/// ```
///
/// # Panics
///
/// Panics if `n` is greater than 20 (overflow)
///
/// # Safety
///
/// (Only for unsafe functions)
pub fn factorial(n: u64) -> u64 {
    match n {
        0 | 1 => 1,
        _ => n * factorial(n - 1),
    }
}
````

## Idiomatic Patterns

### Constructor Pattern

```rust
pub struct Config {
    host: String,
    port: u16,
}

impl Config {
    // Primary constructor
    pub fn new(host: String, port: u16) -> Self {
        Config { host, port }
    }

    // Default constructor
    pub fn default() -> Self {
        Config {
            host: String::from("localhost"),
            port: 8080,
        }
    }

    // From conversion
    pub fn from_env() -> Result<Self, String> {
        // Read from environment
        Ok(Config::default())
    }
}
```

### Builder Pattern

```rust
pub struct User {
    name: String,
    age: Option<u32>,
    email: Option<String>,
}

impl User {
    pub fn builder() -> UserBuilder {
        UserBuilder::default()
    }
}

#[derive(Default)]
pub struct UserBuilder {
    name: Option<String>,
    age: Option<u32>,
    email: Option<String>,
}

impl UserBuilder {
    pub fn name(mut self, name: String) -> Self {
        self.name = Some(name);
        self
    }

    pub fn age(mut self, age: u32) -> Self {
        self.age = Some(age);
        self
    }

    pub fn email(mut self, email: String) -> Self {
        self.email = Some(email);
        self
    }

    pub fn build(self) -> Result<User, String> {
        let name = self.name.ok_or("Name is required")?;
        Ok(User {
            name,
            age: self.age,
            email: self.email,
        })
    }
}

// Usage
let user = User::builder()
    .name(String::from("Alice"))
    .age(30)
    .build()?;
```

### Newtype Pattern

```rust
// Wrap primitive types for type safety
pub struct UserId(u64);
pub struct Email(String);

impl UserId {
    pub fn new(id: u64) -> Self {
        UserId(id)
    }

    pub fn inner(&self) -> u64 {
        self.0
    }
}

// Cannot accidentally mix up UserId and other u64s
fn get_user(id: UserId) -> User {
    // ...
}
```

### Extension Trait Pattern

```rust
// Extend existing types
pub trait StringExt {
    fn first_word(&self) -> &str;
}

impl StringExt for str {
    fn first_word(&self) -> &str {
        self.split_whitespace()
            .next()
            .unwrap_or("")
    }
}

// Usage
let s = "hello world";
println!("{}", s.first_word());
```

## Code Organization

### Import Grouping

```rust
// Standard library
use std::collections::HashMap;
use std::fs::File;
use std::io::Read;

// External crates
use serde::{Deserialize, Serialize};
use tokio::runtime::Runtime;

// Internal modules
use crate::config::Config;
use crate::utils::helper;

// Use self and super
use self::submodule::Item;
use super::parent_module::Other;
```

### Module Structure

```rust
// Prefer flat module structure
mod user;
mod payment;
mod notification;

// Over deep nesting
mod domain {
    mod user {
        mod model;
        mod service;
    }
}
```

## Performance Idioms

### Avoid Unnecessary Clones

```rust
// Avoid
fn process(data: Vec<String>) {
    let copy = data.clone(); // Unnecessary
    for item in copy {
        println!("{}", item);
    }
}

// Better: Use reference
fn process(data: &[String]) {
    for item in data {
        println!("{}", item);
    }
}
```

### Use Iterators

```rust
// Avoid: Index-based loops
let mut sum = 0;
for i in 0..numbers.len() {
    sum += numbers[i];
}

// Better: Iterator
let sum: i32 = numbers.iter().sum();

// Better: Iterator chains
let result: Vec<_> = numbers
    .iter()
    .filter(|&&x| x > 0)
    .map(|&x| x * 2)
    .collect();
```

### Preallocate Collections

```rust
// Avoid: Growing gradually
let mut vec = Vec::new();
for i in 0..1000 {
    vec.push(i);
}

// Better: Preallocate
let mut vec = Vec::with_capacity(1000);
for i in 0..1000 {
    vec.push(i);
}

// Best: Use iterator
let vec: Vec<_> = (0..1000).collect();
```

## Testing Conventions

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_descriptive_name() {
        // Given
        let input = 42;

        // When
        let result = function_under_test(input);

        // Then
        assert_eq!(result, expected);
    }

    #[test]
    fn test_error_case() {
        let result = fallible_function();
        assert!(result.is_err());
    }
}
```

> **Good to know:** Run `cargo fmt` before every commit. Enable `clippy` in CI/CD. Follow the API Guidelines (rust-lang.github.io/api-guidelines). Use descriptive variable names even if they're longer. Prefer explicit types over type inference in public APIs. Write documentation for all public items. The Rust community values explicit, readable code over clever tricks.

---

## References

- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/)
- [The Rust Style Guide](https://doc.rust-lang.org/nightly/style-guide/)
- [rustfmt](https://github.com/rust-lang/rustfmt)
- [Clippy](https://github.com/rust-lang/rust-clippy)
- [Rust Design Patterns](https://rust-unofficial.github.io/patterns/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
