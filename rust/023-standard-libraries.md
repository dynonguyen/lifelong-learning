# Standard/Built-in Libraries

Rust's standard library (`std`) provides essential functionality.

## Core Modules

### std::collections

```rust
use std::collections::{HashMap, HashSet, BTreeMap, VecDeque, LinkedList};

// HashMap
let mut map = HashMap::new();
map.insert("key", "value");

// HashSet
let mut set = HashSet::new();
set.insert(1);

// VecDeque (double-ended queue)
let mut deque = VecDeque::new();
deque.push_back(1);
deque.push_front(0);

// BTreeMap (sorted map)
let mut btree = BTreeMap::new();
btree.insert(1, "one");
```

### std::io

```rust
use std::io::{self, Read, Write, BufRead, BufReader};
use std::fs::File;

// Read from stdin
let mut input = String::new();
io::stdin().read_line(&mut input)?;

// Write to stdout
io::stdout().write_all(b"Hello\n")?;

// Buffered reading
let file = File::open("file.txt")?;
let reader = BufReader::new(file);
for line in reader.lines() {
    println!("{}", line?);
}
```

### std::fs

```rust
use std::fs;

// Read entire file
let contents = fs::read_to_string("file.txt")?;

// Write to file
fs::write("output.txt", b"Hello, World!")?;

// Read bytes
let bytes = fs::read("binary.dat")?;

// Copy file
fs::copy("source.txt", "dest.txt")?;

// Remove file
fs::remove_file("temp.txt")?;

// Create directory
fs::create_dir("new_dir")?;
fs::create_dir_all("path/to/nested/dir")?;

// Read directory
for entry in fs::read_dir(".")? {
    let entry = entry?;
    println!("{:?}", entry.path());
}

// Metadata
let metadata = fs::metadata("file.txt")?;
println!("Size: {}", metadata.len());
println!("Is file: {}", metadata.is_file());
```

### std::path

```rust
use std::path::{Path, PathBuf};

// Create path
let path = Path::new("/usr/local/bin");

// PathBuf (owned path)
let mut path_buf = PathBuf::from("/usr");
path_buf.push("local");
path_buf.push("bin");

// Path operations
let file_path = Path::new("/path/to/file.txt");
println!("File name: {:?}", file_path.file_name());
println!("Extension: {:?}", file_path.extension());
println!("Parent: {:?}", file_path.parent());
println!("Is absolute: {}", file_path.is_absolute());

// Join paths
let joined = path_buf.join("program");
```

### std::env

```rust
use std::env;

// Get environment variable
let path = env::var("PATH").unwrap_or_default();

// Set environment variable
env::set_var("MY_VAR", "value");

// Get current directory
let current_dir = env::current_dir()?;

// Get command line arguments
let args: Vec<String> = env::args().collect();

// Get program path
let exe_path = env::current_exe()?;

// Temporary directory
let temp_dir = env::temp_dir();
```

### std::process

```rust
use std::process::{Command, Stdio};

// Execute command
let output = Command::new("ls")
    .arg("-la")
    .output()?;

println!("Status: {}", output.status);
println!("Stdout: {}", String::from_utf8_lossy(&output.stdout));

// Spawn child process
let child = Command::new("sleep")
    .arg("5")
    .spawn()?;

// Wait for completion
let status = child.wait_with_output()?;

// Piping
let child = Command::new("echo")
    .arg("hello")
    .stdout(Stdio::piped())
    .spawn()?;

// Exit program
std::process::exit(0);
```

### std::thread

```rust
use std::thread;
use std::time::Duration;

// Spawn thread
let handle = thread::spawn(|| {
    println!("Hello from thread!");
    42
});

// Wait for thread
let result = handle.join().unwrap();

// Sleep
thread::sleep(Duration::from_secs(1));

// Thread builder
let builder = thread::Builder::new()
    .name("worker".to_string())
    .stack_size(4 * 1024 * 1024);

let handle = builder.spawn(|| {
    // Thread code
})?;
```

### std::sync

```rust
use std::sync::{Arc, Mutex, RwLock, mpsc};

// Mutex
let mutex = Arc::new(Mutex::new(0));
let lock = mutex.lock().unwrap();

// RwLock
let rwlock = Arc::new(RwLock::new(vec![1, 2, 3]));
let read = rwlock.read().unwrap();
let write = rwlock.write().unwrap();

// Channels
let (tx, rx) = mpsc::channel();
tx.send(42).unwrap();
let value = rx.recv().unwrap();

// Once
use std::sync::Once;
static INIT: Once = Once::new();
INIT.call_once(|| {
    // Initialize once
});
```

### std::time

```rust
use std::time::{Duration, Instant, SystemTime};

// Duration
let duration = Duration::from_secs(5);
let duration = Duration::from_millis(500);

// Instant (monotonic time)
let start = Instant::now();
// ... do work ...
let elapsed = start.elapsed();
println!("Time: {:?}", elapsed);

// SystemTime (wall clock)
let now = SystemTime::now();
let since_epoch = now.duration_since(SystemTime::UNIX_EPOCH)?;
```

### std::fmt

```rust
use std::fmt;

// Custom Display
struct Point { x: i32, y: i32 }

impl fmt::Display for Point {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "({}, {})", self.x, self.y)
    }
}

// Custom Debug
impl fmt::Debug for Point {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        f.debug_struct("Point")
            .field("x", &self.x)
            .field("y", &self.y)
            .finish()
    }
}

// Formatting
let s = format!("{:.2}", 3.14159); // "3.14"
let s = format!("{:?}", vec![1, 2, 3]); // "[1, 2, 3]"
let s = format!("{:#?}", vec![1, 2, 3]); // Pretty print
```

### std::mem

```rust
use std::mem;

// Size and alignment
println!("Size: {}", mem::size_of::<i32>());
println!("Align: {}", mem::align_of::<i32>());

// Replace value
let mut x = 5;
let old = mem::replace(&mut x, 10);

// Take value, leaving default
let mut x = Some(5);
let taken = mem::take(&mut x); // x = None

// Swap values
let mut x = 1;
let mut y = 2;
mem::swap(&mut x, &mut y);

// Drop value
let x = String::from("hello");
mem::drop(x); // Explicit drop

// Forget value (leak memory)
let x = Box::new(42);
mem::forget(x); // Never dropped
```

### std::ptr

```rust
use std::ptr;

// Null pointers
let null: *const i32 = ptr::null();
let null_mut: *mut i32 = ptr::null_mut();

// Raw pointer operations (unsafe)
let mut x = 10;
let ptr = &mut x as *mut i32;
unsafe {
    *ptr = 20;
}

// Copy memory
let src = [1, 2, 3];
let mut dst = [0, 0, 0];
unsafe {
    ptr::copy_nonoverlapping(src.as_ptr(), dst.as_mut_ptr(), 3);
}
```

### std::iter

```rust
use std::iter;

// Iterators
let v = vec![1, 2, 3, 4, 5];

// Map
let doubled: Vec<_> = v.iter().map(|x| x * 2).collect();

// Filter
let evens: Vec<_> = v.iter().filter(|&&x| x % 2 == 0).copied().collect();

// Fold
let sum: i32 = v.iter().fold(0, |acc, &x| acc + x);

// Chain
let v1 = vec![1, 2, 3];
let v2 = vec![4, 5, 6];
let chained: Vec<_> = v1.iter().chain(v2.iter()).copied().collect();

// Zip
let names = vec!["Alice", "Bob"];
let ages = vec![30, 25];
let pairs: Vec<_> = names.iter().zip(ages.iter()).collect();

// Repeat
let fives: Vec<_> = iter::repeat(5).take(10).collect();

// Range
let nums: Vec<_> = (0..10).collect();
```

### std::ops

```rust
use std::ops::{Add, Mul, Index};

// Operator overloading
#[derive(Debug, Copy, Clone)]
struct Point { x: i32, y: i32 }

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

### std::convert

```rust
use std::convert::{From, Into, TryFrom, TryInto};

// From trait
struct Wrapper(i32);

impl From<i32> for Wrapper {
    fn from(value: i32) -> Self {
        Wrapper(value)
    }
}

let w = Wrapper::from(42);
let w: Wrapper = 42.into(); // Into is automatic

// TryFrom (fallible conversion)
impl TryFrom<i32> for PositiveNumber {
    type Error = &'static str;

    fn try_from(value: i32) -> Result<Self, Self::Error> {
        if value > 0 {
            Ok(PositiveNumber(value))
        } else {
            Err("Number must be positive")
        }
    }
}
```

### std::error

```rust
use std::error::Error;
use std::fmt;

// Custom error type
#[derive(Debug)]
struct MyError {
    message: String,
}

impl fmt::Display for MyError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "MyError: {}", self.message)
    }
}

impl Error for MyError {}

// Error chain
fn get_error_chain(err: &dyn Error) {
    let mut current = Some(err);
    while let Some(error) = current {
        println!("Error: {}", error);
        current = error.source();
    }
}
```

### std::cell

```rust
use std::cell::{Cell, RefCell};

// Cell: Copy types only
let cell = Cell::new(5);
cell.set(10);
let value = cell.get();

// RefCell: Runtime borrow checking
let refcell = RefCell::new(vec![1, 2, 3]);
refcell.borrow_mut().push(4);
println!("{:?}", refcell.borrow());
```

### std::rc and std::sync

```rust
use std::rc::Rc;
use std::sync::Arc;

// Rc: single-threaded reference counting
let rc = Rc::new(5);
let rc2 = Rc::clone(&rc);
println!("Count: {}", Rc::strong_count(&rc));

// Arc: thread-safe reference counting
let arc = Arc::new(5);
let arc2 = Arc::clone(&arc);
```

## No Standard Library (no_std)

```rust
#![no_std]

// For embedded systems or bare metal
// Still have access to core library
extern crate alloc; // For heap allocations

use core::fmt::Write;
use alloc::vec::Vec;
```

> **Good to know:** The standard library is automatically imported via the prelude. Use `std::prelude::v1::*` to see what's included. For embedded or WebAssembly, you can use `#![no_std]` with the `core` library. Most std types implement common traits like `Debug`, `Clone`, `Send`, and `Sync`. Check docs.rs for complete std documentation.

---

## References

- [Rust Standard Library Documentation](https://doc.rust-lang.org/std/)
- [The Rust Prelude](https://doc.rust-lang.org/std/prelude/)
- [The Core Library](https://doc.rust-lang.org/core/)
- [Rust By Example - Std Library](https://doc.rust-lang.org/rust-by-example/std.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
