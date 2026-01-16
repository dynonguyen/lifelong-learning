# Error Handling/Exceptions

Rust doesn't have exceptions. Instead, it uses `Result<T, E>` for recoverable errors and `panic!` for unrecoverable errors.

## The Result Type

### Basic Result Usage

```rust
fn divide(a: i32, b: i32) -> Result<i32, String> {
    if b == 0 {
        Err(String::from("Division by zero"))
    } else {
        Ok(a / b)
    }
}

// Handle result
match divide(10, 2) {
    Ok(result) => println!("Result: {}", result),
    Err(e) => println!("Error: {}", e),
}
```

### The ? Operator

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_file_contents(path: &str) -> Result<String, io::Error> {
    let mut file = File::open(path)?; // Returns early if Err
    let mut contents = String::new();
    file.read_to_string(&mut contents)?;
    Ok(contents)
}

// ? converts errors automatically if they implement From
fn process_file(path: &str) -> Result<(), Box<dyn std::error::Error>> {
    let contents = read_file_contents(path)?;
    println!("Contents: {}", contents);
    Ok(())
}
```

### Unwrap and Expect

```rust
let result: Result<i32, String> = Ok(42);

// unwrap: panics if Err
let value = result.unwrap(); // 42

// expect: panics with custom message
let value = result.expect("Should have a value");

// unwrap_or: provides default value
let value = result.unwrap_or(0);

// unwrap_or_else: compute default value
let value = result.unwrap_or_else(|err| {
    println!("Error: {}", err);
    0
});
```

## Custom Error Types

### Simple Custom Error

```rust
use std::fmt;

#[derive(Debug)]
enum MyError {
    IoError(String),
    ParseError(String),
    NotFound,
}

impl fmt::Display for MyError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            MyError::IoError(msg) => write!(f, "IO Error: {}", msg),
            MyError::ParseError(msg) => write!(f, "Parse Error: {}", msg),
            MyError::NotFound => write!(f, "Resource not found"),
        }
    }
}

impl std::error::Error for MyError {}

fn might_fail() -> Result<i32, MyError> {
    Err(MyError::NotFound)
}
```

### Using thiserror Crate

```toml
[dependencies]
thiserror = "1.0"
```

```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum DataError {
    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),

    #[error("Parse error: {0}")]
    Parse(#[from] std::num::ParseIntError),

    #[error("Not found: {0}")]
    NotFound(String),

    #[error("Invalid data at line {line}: {msg}")]
    Invalid { line: usize, msg: String },
}

fn process() -> Result<(), DataError> {
    let num: i32 = "abc".parse()?; // Auto-converts ParseIntError
    Ok(())
}
```

### Using anyhow Crate

```toml
[dependencies]
anyhow = "1.0"
```

```rust
use anyhow::{Context, Result};

fn read_config() -> Result<String> {
    let contents = std::fs::read_to_string("config.toml")
        .context("Failed to read config file")?;
    Ok(contents)
}

fn process() -> Result<()> {
    let config = read_config()
        .context("Configuration error")?;

    // Can return any error type
    let num: i32 = config.parse()
        .context("Invalid number in config")?;

    Ok(())
}
```

## Error Propagation

### Manual Propagation

```rust
fn outer() -> Result<i32, String> {
    let result = inner();
    match result {
        Ok(value) => Ok(value * 2),
        Err(e) => Err(format!("outer error: {}", e)),
    }
}

fn inner() -> Result<i32, String> {
    Ok(42)
}
```

### Using ? Operator

```rust
fn outer() -> Result<i32, String> {
    let value = inner()?;
    Ok(value * 2)
}

fn inner() -> Result<i32, String> {
    Ok(42)
}
```

### Combining Different Error Types

```rust
use std::error::Error;

fn complex_operation() -> Result<(), Box<dyn Error>> {
    let file = std::fs::read_to_string("file.txt")?; // io::Error
    let num: i32 = file.trim().parse()?;              // ParseIntError
    println!("Number: {}", num);
    Ok(())
}
```

## Panic

### When to Panic

```rust
// Explicit panic
panic!("Something went wrong!");

// With formatted message
let x = 5;
panic!("Value was: {}", x);

// Assert macros (panic if false)
assert!(x > 0);
assert_eq!(x, 5);
assert_ne!(x, 10);

// Debug assert (only in debug builds)
debug_assert!(x > 0);
```

### Catching Panics

```rust
use std::panic;

fn might_panic() {
    panic!("Oops!");
}

let result = panic::catch_unwind(|| {
    might_panic();
});

match result {
    Ok(_) => println!("No panic"),
    Err(_) => println!("Caught a panic"),
}
```

### Setting Panic Hook

```rust
use std::panic;

panic::set_hook(Box::new(|panic_info| {
    eprintln!("Custom panic handler: {:?}", panic_info);
}));

// This panic will use the custom hook
panic!("Something bad happened");
```

## Option and Result Conversion

### Option to Result

```rust
let opt: Option<i32> = Some(42);

// Convert Option to Result
let result: Result<i32, &str> = opt.ok_or("No value");

// With closure
let result: Result<i32, String> = opt.ok_or_else(|| {
    String::from("Computed error message")
});
```

### Result to Option

```rust
let result: Result<i32, String> = Ok(42);

// Convert Result to Option (discards error)
let opt: Option<i32> = result.ok();

// Get error as Option
let err_opt: Option<String> = result.err();
```

## Error Handling Patterns

### Early Return Pattern

```rust
fn validate_user(user: &User) -> Result<(), ValidationError> {
    if user.name.is_empty() {
        return Err(ValidationError::EmptyName);
    }

    if user.age < 18 {
        return Err(ValidationError::TooYoung);
    }

    if user.email.is_empty() {
        return Err(ValidationError::EmptyEmail);
    }

    Ok(())
}
```

### Collecting Results

```rust
let numbers = vec!["1", "2", "three", "4"];

// Collect into Result<Vec<_>, _>
let parsed: Result<Vec<i32>, _> = numbers
    .iter()
    .map(|s| s.parse::<i32>())
    .collect();

// Short-circuits on first error
match parsed {
    Ok(nums) => println!("Parsed: {:?}", nums),
    Err(e) => println!("Error: {}", e),
}
```

### Partition Results

```rust
let results = vec![Ok(1), Err("error1"), Ok(2), Err("error2"), Ok(3)];

let (successes, failures): (Vec<_>, Vec<_>) = results
    .into_iter()
    .partition(Result::is_ok);

let successes: Vec<_> = successes.into_iter()
    .map(Result::unwrap)
    .collect();

let failures: Vec<_> = failures.into_iter()
    .map(Result::unwrap_err)
    .collect();

println!("Successes: {:?}", successes); // [1, 2, 3]
println!("Failures: {:?}", failures);   // ["error1", "error2"]
```

### Retry Pattern

```rust
fn retry<F, T, E>(mut f: F, max_attempts: u32) -> Result<T, E>
where
    F: FnMut() -> Result<T, E>,
{
    let mut attempts = 0;
    loop {
        match f() {
            Ok(value) => return Ok(value),
            Err(e) => {
                attempts += 1;
                if attempts >= max_attempts {
                    return Err(e);
                }
                std::thread::sleep(std::time::Duration::from_millis(100));
            }
        }
    }
}

// Usage
let result = retry(|| {
    // Attempt operation
    Ok(42)
}, 3);
```

## Logging Errors

```rust
use log::{error, warn, info};

fn process_file(path: &str) -> Result<(), std::io::Error> {
    match std::fs::read_to_string(path) {
        Ok(contents) => {
            info!("Successfully read file: {}", path);
            Ok(())
        }
        Err(e) => {
            error!("Failed to read file {}: {}", path, e);
            Err(e)
        }
    }
}

// With anyhow
fn process_with_context(path: &str) -> anyhow::Result<()> {
    let contents = std::fs::read_to_string(path)
        .with_context(|| format!("Failed to read file: {}", path))?;

    info!("Read {} bytes", contents.len());
    Ok(())
}
```

## Testing Error Cases

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_divide_by_zero() {
        let result = divide(10, 0);
        assert!(result.is_err());

        match result {
            Err(e) => assert_eq!(e, "Division by zero"),
            Ok(_) => panic!("Expected error"),
        }
    }

    #[test]
    #[should_panic(expected = "Division by zero")]
    fn test_panic() {
        divide(10, 0).unwrap();
    }

    #[test]
    fn test_error_with_matches() {
        let result = divide(10, 0);
        assert!(matches!(result, Err(_)));
    }
}
```

> **Good to know:** Use `Result` for recoverable errors and `panic!` for bugs/unrecoverable errors. The `?` operator is idiomatic for error propagation. For applications, use `anyhow` for convenient error handling. For libraries, use `thiserror` to create custom error types. Always add context to errors to make debugging easier. Never use `unwrap()` or `expect()` in production code unless you're absolutely certain the operation can't fail.

---

## References

- [The Rust Book - Error Handling](https://doc.rust-lang.org/book/ch09-00-error-handling.html)
- [std::result::Result](https://doc.rust-lang.org/std/result/enum.Result.html)
- [thiserror crate](https://docs.rs/thiserror/)
- [anyhow crate](https://docs.rs/anyhow/)
- [Rust Error Handling](https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
