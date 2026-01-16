# Data Types

Rust is a statically typed language, meaning all variable types must be known at compile time.

## Scalar Types

### Integer Types

```rust
// Signed integers (can be negative)
let i8_val: i8 = -128;          // -128 to 127
let i16_val: i16 = -32_768;     // -32,768 to 32,767
let i32_val: i32 = -2_147_483_648; // Default for integers
let i64_val: i64 = -9_223_372_036_854_775_808;
let i128_val: i128 = 100;
let isize_val: isize = -100;    // Pointer-sized (32 or 64-bit)

// Unsigned integers (non-negative only)
let u8_val: u8 = 255;           // 0 to 255
let u16_val: u16 = 65_535;
let u32_val: u32 = 4_294_967_295;
let u64_val: u64 = 18_446_744_073_709_551_615;
let u128_val: u128 = 100;
let usize_val: usize = 100;     // Pointer-sized

// Integer literals
let decimal = 98_222;
let hex = 0xff;
let octal = 0o77;
let binary = 0b1111_0000;
let byte = b'A';  // u8 only
```

### Floating-Point Types

```rust
let f32_val: f32 = 3.14; // Single precision
let f64_val: f64 = 2.71828; // Double precision (default)

// Scientific notation
let large = 1e10;  // 10000000000.0
let small = 1e-4;  // 0.0001
```

### Boolean Type

```rust
let t: bool = true;
let f: bool = false;

// From expressions
let is_positive = 5 > 0;
```

### Character Type

```rust
// Single character, 4 bytes (Unicode Scalar Value)
let c: char = 'z';
let emoji: char = '😊';
let chinese: char = '中';

// Note: char is different from &str
```

## Compound Types

### Tuple Type

```rust
// Fixed-size, heterogeneous collection
let tuple: (i32, f64, char) = (500, 6.4, 'x');

// Destructuring
let (x, y, z) = tuple;

// Access by index
let first = tuple.0;
let second = tuple.1;

// Unit type (empty tuple)
let unit: () = ();
```

### Array Type

```rust
// Fixed-size, homogeneous collection
let array: [i32; 5] = [1, 2, 3, 4, 5];

// Initialize with same value
let zeros = [0; 10]; // [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]

// Access elements
let first = array[0];
let last = array[4];

// Type: [T; N] where T is type, N is compile-time size
```

## String Types

### String Slice (&str)

```rust
// String literal (immutable, stored in binary)
let s: &str = "hello world";

// String slices are borrowed references
let slice: &str = &String::from("hello")[0..2]; // "he"
```

### String Type

```rust
// Heap-allocated, growable string
let mut s = String::from("hello");
s.push_str(", world!");

// Create from literal
let s1 = "hello".to_string();
let s2 = String::from("hello");
```

## Pointer Types

### References

```rust
let x = 5;
let reference: &i32 = &x;       // Immutable reference
let mut y = 10;
let mut_ref: &mut i32 = &mut y; // Mutable reference
```

### Raw Pointers

```rust
// Unsafe, can be null
let x = 5;
let raw_ptr: *const i32 = &x as *const i32;
let mut y = 10;
let mut_raw_ptr: *mut i32 = &mut y as *mut i32;
```

### Smart Pointers

```rust
use std::rc::Rc;
use std::sync::Arc;

// Box: heap allocation
let boxed: Box<i32> = Box::new(5);

// Rc: reference counting
let rc: Rc<i32> = Rc::new(5);

// Arc: atomic reference counting (thread-safe)
let arc: Arc<i32> = Arc::new(5);
```

## Collection Types

### Vector

```rust
// Dynamic array
let vec: Vec<i32> = vec![1, 2, 3, 4, 5];
let empty: Vec<String> = Vec::new();
```

### HashMap

```rust
use std::collections::HashMap;

let mut map: HashMap<String, i32> = HashMap::new();
map.insert(String::from("key"), 42);
```

### HashSet

```rust
use std::collections::HashSet;

let set: HashSet<i32> = HashSet::new();
```

## Option and Result

### Option<T>

```rust
// Represents optional value
let some_value: Option<i32> = Some(5);
let no_value: Option<i32> = None;
```

### Result<T, E>

```rust
// Represents success or error
let success: Result<i32, String> = Ok(42);
let failure: Result<i32, String> = Err(String::from("Error message"));
```

## Type Conversion

### As Casting

```rust
let x = 10i32;
let y = x as f64;  // 10.0

let byte = 65u8;
let character = byte as char; // 'A'
```

### From/Into Traits

```rust
// From
let s = String::from("hello");
let num = i32::from(true); // 1

// Into (reciprocal of From)
let s: String = "hello".into();
```

### TryFrom/TryInto

```rust
use std::convert::TryFrom;

// Fallible conversion
let result = i32::try_from(300u32); // Ok(300)
let overflow = i8::try_from(300i32); // Err(...)
```

## Type Aliases

```rust
type Kilometers = i32;
type Result<T> = std::result::Result<T, std::io::Error>;

let distance: Kilometers = 50;
```

## Never Type

```rust
// Type that never returns (!)
fn diverges() -> ! {
    panic!("This function never returns");
}

fn infinite_loop() -> ! {
    loop {
        // Never exits
    }
}
```

## Size of Types

```rust
use std::mem;

println!("i32: {} bytes", mem::size_of::<i32>());      // 4
println!("f64: {} bytes", mem::size_of::<f64>());      // 8
println!("char: {} bytes", mem::size_of::<char>());    // 4
println!("&i32: {} bytes", mem::size_of::<&i32>());    // 8 (on 64-bit)
println!("String: {} bytes", mem::size_of::<String>()); // 24
```

> **Good to know:** Rust's `char` type is 4 bytes because it represents a Unicode Scalar Value, not just ASCII. Use `i32` as default integer type unless you have specific size requirements. The compiler is very good at inferring types, so explicit annotations are often optional. All numeric types can overflow - use checked/saturating/wrapping methods for safety.

---

## References

- [The Rust Book - Data Types](https://doc.rust-lang.org/book/ch03-02-data-types.html)
- [Rust Reference - Types](https://doc.rust-lang.org/reference/types.html)
- [std::primitive](https://doc.rust-lang.org/std/primitive/index.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
