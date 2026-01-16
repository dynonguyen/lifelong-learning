# Tips & Tricks

Practical advice and idioms for writing better Rust code.

## Performance Tips

### Avoid Unnecessary Cloning

```rust
// Bad: Unnecessary clone
fn process(data: Vec<String>) {
    for item in data.clone() {
        println!("{}", item);
    }
}

// Good: Use reference
fn process(data: &[String]) {
    for item in data {
        println!("{}", item);
    }
}

// Good: Take ownership if needed
fn process(data: Vec<String>) {
    for item in data {
        println!("{}", item);
    }
}
```

### Use Iterators Instead of Loops

```rust
// Slower: Manual loops
let mut sum = 0;
for i in 0..numbers.len() {
    sum += numbers[i];
}

// Faster: Iterators (optimized by compiler)
let sum: i32 = numbers.iter().sum();

// Chain operations
let result: Vec<_> = numbers
    .iter()
    .filter(|&&x| x > 0)
    .map(|&x| x * 2)
    .collect();
```

### Preallocate Collections

```rust
// Bad: Growing on demand
let mut vec = Vec::new();
for i in 0..1000 {
    vec.push(i);
}

// Good: Preallocate
let mut vec = Vec::with_capacity(1000);
for i in 0..1000 {
    vec.push(i);
}

// Best: Use iterator
let vec: Vec<_> = (0..1000).collect();
```

### Use `&str` Instead of `String` in Parameters

```rust
// Bad: Forces allocation
fn greet(name: String) {
    println!("Hello, {}!", name);
}

// Good: Accept string slice
fn greet(name: &str) {
    println!("Hello, {}!", name);
}

// Works with both
greet("Alice");
greet(&String::from("Bob"));
```

### Avoid Repeated Allocations

```rust
// Bad: Repeated allocations in loop
for i in 0..1000 {
    let s = format!("Item {}", i);
    process(&s);
}

// Good: Reuse buffer
use std::fmt::Write;
let mut buffer = String::with_capacity(20);
for i in 0..1000 {
    buffer.clear();
    write!(&mut buffer, "Item {}", i).unwrap();
    process(&buffer);
}
```

## Memory Tips

### Use Cow for Potential Copies

```rust
use std::borrow::Cow;

fn process<'a>(input: &'a str) -> Cow<'a, str> {
    if input.contains("special") {
        Cow::Owned(input.replace("special", "SPECIAL"))
    } else {
        Cow::Borrowed(input)
    }
}
```

### Stack vs Heap

```rust
// Stack (fast, limited size)
let array = [1, 2, 3, 4, 5];
let small_struct = Point { x: 1, y: 2 };

// Heap (slower, unlimited size)
let vec = vec![1, 2, 3, 4, 5];
let boxed = Box::new(large_struct);
```

## Error Handling Tips

### Use `?` Operator

```rust
// Bad: Manual error handling
fn read_file(path: &str) -> Result<String, std::io::Error> {
    match std::fs::read_to_string(path) {
        Ok(content) => Ok(content),
        Err(e) => Err(e),
    }
}

// Good: Use ?
fn read_file(path: &str) -> Result<String, std::io::Error> {
    Ok(std::fs::read_to_string(path)?)
}

// Better: Return directly
fn read_file(path: &str) -> Result<String, std::io::Error> {
    std::fs::read_to_string(path)
}
```

### Add Context to Errors

```rust
use anyhow::{Context, Result};

fn process_config() -> Result<()> {
    let config = std::fs::read_to_string("config.toml")
        .context("Failed to read config file")?;

    let port: u16 = config.parse()
        .context("Invalid port number in config")?;

    Ok(())
}
```

### Use Custom Error Types in Libraries

```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum MyError {
    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),

    #[error("Invalid configuration: {0}")]
    Config(String),
}
```

## Pattern Matching Tips

### Match with Guards

```rust
let num = Some(4);

match num {
    Some(x) if x < 5 => println!("Less than 5: {}", x),
    Some(x) => println!("Greater or equal to 5: {}", x),
    None => println!("No value"),
}
```

### Use `if let` for Single Pattern

```rust
// Instead of:
match optional {
    Some(value) => do_something(value),
    None => {},
}

// Use:
if let Some(value) = optional {
    do_something(value);
}
```

### Use `matches!` Macro

```rust
let status = HttpStatus::Ok;

// Instead of:
let is_success = match status {
    HttpStatus::Ok | HttpStatus::Created => true,
    _ => false,
};

// Use:
let is_success = matches!(status, HttpStatus::Ok | HttpStatus::Created);
```

## Iterator Tips

### Collect to Specific Type

```rust
// Explicitly specify type
let nums: Vec<i32> = (0..10).collect();
let set: HashSet<i32> = (0..10).collect();

// Use turbofish
let nums = (0..10).collect::<Vec<i32>>();
```

### Chain Iterators

```rust
let v1 = vec![1, 2, 3];
let v2 = vec![4, 5, 6];

let combined: Vec<_> = v1.iter()
    .chain(v2.iter())
    .copied()
    .collect();
```

### Use `filter_map` Instead of `filter` + `map`

```rust
// Less efficient
let results: Vec<_> = data
    .iter()
    .filter(|x| x.is_some())
    .map(|x| x.unwrap())
    .collect();

// More efficient
let results: Vec<_> = data
    .iter()
    .filter_map(|x| x.as_ref())
    .collect();
```

## Lifetime Tips

### Lifetime Elision

```rust
// Compiler infers lifetimes
fn first<'a>(data: &'a [i32]) -> &'a i32 {
    &data[0]
}

// Can be written as:
fn first(data: &[i32]) -> &i32 {
    &data[0]
}
```

### Static Lifetime for String Literals

```rust
const NAME: &'static str = "Rust";

// Or just:
const NAME: &str = "Rust";
```

## Trait Tips

### Blanket Implementations

```rust
// Implement for all types that satisfy bounds
trait MyTrait {
    fn my_method(&self);
}

impl<T: Display> MyTrait for T {
    fn my_method(&self) {
        println!("{}", self);
    }
}
```

### Use `AsRef` and `AsMut`

```rust
fn process<P: AsRef<Path>>(path: P) {
    let path = path.as_ref();
    // Works with &Path, PathBuf, &str, String, etc.
}
```

### Use `Into` in Parameters

```rust
fn greet(name: impl Into<String>) {
    let name = name.into();
    println!("Hello, {}!", name);
}

// Works with both
greet("Alice");
greet(String::from("Bob"));
```

## Macro Tips

### Debug Print with Location

```rust
let x = 5;
dbg!(x); // [src/main.rs:2] x = 5

// Returns the value
let y = dbg!(x * 2); // y = 10
```

### Use `todo!` and `unimplemented!`

```rust
fn future_feature() -> Result<(), Error> {
    todo!("Implement this later")
}

fn not_needed_yet() {
    unimplemented!("Not implemented")
}
```

## Testing Tips

### Use `assert_eq!` with Message

```rust
#[test]
fn test_calculation() {
    let result = calculate(5, 3);
    assert_eq!(result, 8, "5 + 3 should equal 8, got {}", result);
}
```

### Test Panics

```rust
#[test]
#[should_panic(expected = "Division by zero")]
fn test_divide_by_zero() {
    divide(10, 0);
}
```

### Use Result in Tests

```rust
#[test]
fn test_file_read() -> Result<(), Box<dyn std::error::Error>> {
    let content = std::fs::read_to_string("test.txt")?;
    assert_eq!(content, "expected");
    Ok(())
}
```

## Documentation Tips

### Link to Items

```rust
/// See [`other_function`] for details.
/// Also check [`MyStruct::method`].
pub fn my_function() {}
```

### Code Examples in Docs

````rust
/// # Examples
///
/// ```
/// use my_crate::function;
///
/// let result = function(42);
/// assert_eq!(result, 84);
/// ```
pub fn function(x: i32) -> i32 {
    x * 2
}
````

## Cargo Tips

### Cargo Aliases

```toml
# .cargo/config.toml
[alias]
b = "build"
br = "build --release"
t = "test"
r = "run"
c = "check"
```

### Feature Flags

```toml
# Cargo.toml
[features]
default = ["feature1"]
feature1 = []
feature2 = ["dep:optional_crate"]
```

```bash
cargo build --features feature2
cargo build --all-features
cargo build --no-default-features
```

## Debugging Tips

### Print Types

```rust
fn print_type_of<T>(_: &T) {
    println!("{}", std::any::type_name::<T>());
}

let x = 42;
print_type_of(&x); // i32
```

### Use `dbg!` Macro

```rust
let result = dbg!(complex_calculation());
// Prints: [src/main.rs:2] complex_calculation() = 42
```

### Compile-time Type Errors

```rust
// Force a compile error to see the type
let x = 5;
let _: () = x; // ERROR: expected `()`, found `i32`
```

## Common Pitfalls

### Integer Overflow in Debug

```rust
// Panics in debug mode
let x: u8 = 255;
// let y = x + 1; // panic!

// Use checked operations
let y = x.checked_add(1); // None

// Or wrapping
let y = x.wrapping_add(1); // 0

// Or saturating
let y = x.saturating_add(1); // 255
```

### Lifetime Issues

```rust
// Bad: Dangling reference
fn bad() -> &str {
    let s = String::from("hello");
    &s // ERROR: s dropped
}

// Good: Return owned value
fn good() -> String {
    String::from("hello")
}
```

### Borrow Checker

```rust
// Bad: Trying to use after move
let v = vec![1, 2, 3];
process(v);
// println!("{:?}", v); // ERROR: moved

// Good: Use reference
let v = vec![1, 2, 3];
process(&v);
println!("{:?}", v); // OK
```

> **Good to know:** Use `cargo clippy` regularly to catch common mistakes. Profile before optimizing - don't guess where bottlenecks are. Read compiler error messages carefully - they're usually very helpful. Use `cargo-expand` to see macro expansions. Enable all warnings in CI with `RUSTFLAGS="-D warnings"`. Use `cargo-outdated` to check for dependency updates.

---

## References

- [Rust Performance Book](https://nnethercote.github.io/perf-book/)
- [Rust Design Patterns](https://rust-unofficial.github.io/patterns/)
- [Effective Rust](https://www.lurklurk.org/effective-rust/)
- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/)
- [The Little Book of Rust Macros](https://veykril.github.io/tlborm/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
