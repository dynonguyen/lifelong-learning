# Testing/Unit Tests

Rust has built-in testing support with cargo.

## Unit Tests

### Basic Test Structure

```rust
// src/lib.rs
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_add() {
        assert_eq!(add(2, 2), 4);
    }

    #[test]
    fn test_add_negative() {
        assert_eq!(add(-1, 1), 0);
    }
}
```

### Running Tests

```bash
# Run all tests
cargo test

# Run specific test
cargo test test_add

# Run tests matching pattern
cargo test add

# Run with output
cargo test -- --nocapture

# Run tests single-threaded
cargo test -- --test-threads=1

# Run ignored tests
cargo test -- --ignored

# Run all tests including ignored
cargo test -- --include-ignored
```

## Assertions

### Basic Assertions

```rust
#[test]
fn test_assertions() {
    // Assert true
    assert!(true);
    assert!(2 + 2 == 4);

    // Assert equality
    assert_eq!(2 + 2, 4);
    assert_eq!(vec![1, 2, 3], vec![1, 2, 3]);

    // Assert inequality
    assert_ne!(2 + 2, 5);

    // With custom messages
    assert_eq!(2 + 2, 4, "Math is broken!");
    assert!(5 > 3, "5 should be greater than 3");
}
```

### Debug Assertions

```rust
#[test]
fn test_debug_assert() {
    // Only checked in debug builds
    debug_assert_eq!(2 + 2, 4);
    debug_assert!(true);
}
```

## Testing Errors

### Should Panic

```rust
pub fn divide(a: i32, b: i32) -> i32 {
    if b == 0 {
        panic!("Division by zero");
    }
    a / b
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    #[should_panic]
    fn test_divide_by_zero() {
        divide(10, 0);
    }

    #[test]
    #[should_panic(expected = "Division by zero")]
    fn test_divide_by_zero_message() {
        divide(10, 0);
    }
}
```

### Testing Result

```rust
pub fn parse_number(s: &str) -> Result<i32, std::num::ParseIntError> {
    s.parse()
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_parse_valid() -> Result<(), String> {
        let result = parse_number("42")
            .map_err(|e| format!("Parse error: {}", e))?;
        assert_eq!(result, 42);
        Ok(())
    }

    #[test]
    fn test_parse_invalid() {
        assert!(parse_number("not a number").is_err());
    }
}
```

## Test Organization

### Tests Module

```rust
// src/lib.rs
pub fn internal_function() -> i32 {
    private_helper()
}

fn private_helper() -> i32 {
    42
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_internal() {
        // Can test private functions
        assert_eq!(private_helper(), 42);
    }
}
```

### Separate Test Files

```
src/
  lib.rs
  module1.rs
  module2.rs
  module1/
    tests.rs  # Tests for module1
```

## Integration Tests

### Tests Directory

```
my_project/
  Cargo.toml
  src/
    lib.rs
  tests/
    integration_test.rs
    common/
      mod.rs
```

```rust
// tests/integration_test.rs
use my_project;

#[test]
fn test_public_api() {
    assert_eq!(my_project::add(2, 2), 4);
}
```

### Common Test Code

```rust
// tests/common/mod.rs
pub fn setup() {
    // Common setup code
}

// tests/integration_test.rs
mod common;

#[test]
fn test_with_setup() {
    common::setup();
    // Test code
}
```

## Test Attributes

### Ignore Tests

```rust
#[test]
#[ignore]
fn expensive_test() {
    // Run only when explicitly requested
    // cargo test -- --ignored
}

#[test]
#[ignore = "not yet implemented"]
fn unfinished_test() {
    // Will be skipped
}
```

### Test Only on Specific Platforms

```rust
#[test]
#[cfg(target_os = "linux")]
fn linux_only_test() {
    // Only runs on Linux
}

#[test]
#[cfg(not(target_os = "windows"))]
fn non_windows_test() {
    // Runs everywhere except Windows
}
```

## Benchmark Tests

### Using Criterion

```toml
[dev-dependencies]
criterion = "0.5"

[[bench]]
name = "my_benchmark"
harness = false
```

```rust
// benches/my_benchmark.rs
use criterion::{black_box, criterion_group, criterion_main, Criterion};
use my_project::add;

fn criterion_benchmark(c: &mut Criterion) {
    c.bench_function("add 2 + 2", |b| {
        b.iter(|| add(black_box(2), black_box(2)))
    });
}

criterion_group!(benches, criterion_benchmark);
criterion_main!(benches);
```

```bash
# Run benchmarks
cargo bench
```

## Mocking

### Using mockall

```toml
[dev-dependencies]
mockall = "0.12"
```

```rust
use mockall::*;

#[automock]
trait Database {
    fn get_user(&self, id: u32) -> Option<String>;
    fn save_user(&mut self, id: u32, name: String) -> Result<(), String>;
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_with_mock() {
        let mut mock = MockDatabase::new();

        mock.expect_get_user()
            .with(eq(1))
            .times(1)
            .returning(|_| Some(String::from("Alice")));

        let result = mock.get_user(1);
        assert_eq!(result, Some(String::from("Alice")));
    }
}
```

## Property-Based Testing

### Using proptest

```toml
[dev-dependencies]
proptest = "1.0"
```

```rust
use proptest::prelude::*;

fn reverse<T: Clone>(xs: &[T]) -> Vec<T> {
    let mut rev = vec![];
    for x in xs {
        rev.insert(0, x.clone())
    }
    rev
}

proptest! {
    #[test]
    fn test_double_reverse(ref xs in prop::collection::vec(any::<i32>(), 0..100)) {
        let reversed_twice = reverse(&reverse(xs));
        assert_eq!(xs, &reversed_twice);
    }

    #[test]
    fn test_reverse_length(ref xs in prop::collection::vec(any::<i32>(), 0..100)) {
        assert_eq!(xs.len(), reverse(xs).len());
    }
}
```

## Test Fixtures

### Setup and Teardown

```rust
struct TestContext {
    temp_dir: std::path::PathBuf,
}

impl TestContext {
    fn new() -> Self {
        let temp_dir = std::env::temp_dir().join("test_fixture");
        std::fs::create_dir_all(&temp_dir).unwrap();
        TestContext { temp_dir }
    }
}

impl Drop for TestContext {
    fn drop(&mut self) {
        std::fs::remove_dir_all(&self.temp_dir).ok();
    }
}

#[test]
fn test_with_fixture() {
    let ctx = TestContext::new();
    // Test code using ctx.temp_dir
    // Directory is cleaned up automatically
}
```

## Doc Tests

### Testing Documentation Examples

````rust
/// Adds two numbers together.
///
/// # Examples
///
/// ```
/// use my_project::add;
/// let result = add(2, 2);
/// assert_eq!(result, 4);
/// ```
///
/// # Panics
///
/// This function will panic if...
///
/// ```should_panic
/// use my_project::add;
/// add(i32::MAX, 1); // Overflow
/// ```
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
````

```bash
# Run doc tests
cargo test --doc
```

### Ignored Doc Tests

````rust
/// ```ignore
/// // This code won't be tested
/// let x = some_undefined_function();
/// ```
pub fn example() {}

/// ```no_run
/// // This compiles but doesn't run
/// std::process::exit(0);
/// ```
pub fn another_example() {}
````

## Test Coverage

### Using tarpaulin

```bash
# Install
cargo install cargo-tarpaulin

# Run with coverage
cargo tarpaulin --out Html

# View coverage report
open tarpaulin-report.html
```

### Using llvm-cov

```bash
# Install
rustup component add llvm-tools-preview
cargo install cargo-llvm-cov

# Run coverage
cargo llvm-cov

# Generate HTML report
cargo llvm-cov --html

# Open report
cargo llvm-cov --open
```

## Snapshot Testing

### Using insta

```toml
[dev-dependencies]
insta = "1.0"
```

```rust
use insta::assert_snapshot;

#[test]
fn test_serialization() {
    let data = vec![1, 2, 3, 4, 5];
    assert_snapshot!(format!("{:?}", data));
}

#[test]
fn test_json_output() {
    let json = serde_json::json!({
        "name": "John",
        "age": 30
    });
    assert_snapshot!(serde_json::to_string_pretty(&json).unwrap());
}
```

```bash
# Review snapshots
cargo insta review
```

## Testing Async Code

```rust
#[tokio::test]
async fn test_async_function() {
    let result = async_add(2, 2).await;
    assert_eq!(result, 4);
}

async fn async_add(a: i32, b: i32) -> i32 {
    a + b
}

// With timeout
#[tokio::test(flavor = "multi_thread", worker_threads = 1)]
async fn test_with_timeout() {
    tokio::time::timeout(
        std::time::Duration::from_secs(5),
        slow_operation()
    ).await.expect("Operation timed out");
}
```

## Test Organization Best Practices

```rust
// Group related tests
#[cfg(test)]
mod user_tests {
    use super::*;

    #[test]
    fn test_user_creation() {
        // Test
    }

    #[test]
    fn test_user_validation() {
        // Test
    }
}

#[cfg(test)]
mod payment_tests {
    use super::*;

    #[test]
    fn test_payment_processing() {
        // Test
    }
}
```

> **Good to know:** Tests are compiled only with `cargo test`, not in release builds. Use `#[cfg(test)]` to exclude test code from production. Doc tests are excellent for keeping documentation up-to-date. Integration tests can only use your crate's public API. Use `Result<T, E>` in tests to propagate errors with `?`. Property-based testing can find edge cases you didn't think of.

---

## References

- [The Rust Book - Testing](https://doc.rust-lang.org/book/ch11-00-testing.html)
- [Cargo Book - Tests](https://doc.rust-lang.org/cargo/guide/tests.html)
- [Criterion.rs](https://bheisler.github.io/criterion.rs/book/)
- [mockall](https://docs.rs/mockall/)
- [proptest](https://docs.rs/proptest/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
