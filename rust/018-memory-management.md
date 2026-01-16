# Pointers/References/Garbage Collection (Memory Management)

Rust's ownership system provides memory safety without garbage collection.

## Ownership

### Ownership Rules

1. Each value has a single owner
2. When the owner goes out of scope, the value is dropped
3. Values can be moved or borrowed

```rust
// Ownership basics
let s1 = String::from("hello");
let s2 = s1; // s1 moved to s2, s1 no longer valid
// println!("{}", s1); // ERROR: value borrowed after move
println!("{}", s2); // OK

// Copy types (stack-only data)
let x = 5;
let y = x; // x is copied, both valid
println!("{}, {}", x, y); // OK
```

### Move Semantics

```rust
fn take_ownership(s: String) {
    println!("{}", s);
} // s dropped here

let s = String::from("hello");
take_ownership(s);
// s is no longer valid here

// Return ownership
fn give_ownership() -> String {
    String::from("hello")
}

let s = give_ownership(); // s now owns the string
```

## References and Borrowing

### Immutable References

```rust
fn calculate_length(s: &String) -> usize {
    s.len()
} // s goes out of scope, but doesn't drop the String

let s1 = String::from("hello");
let len = calculate_length(&s1); // Borrow s1
println!("Length of '{}' is {}", s1, len); // s1 still valid
```

### Mutable References

```rust
fn change(s: &mut String) {
    s.push_str(", world");
}

let mut s = String::from("hello");
change(&mut s);
println!("{}", s); // "hello, world"
```

### Borrowing Rules

```rust
// Rule 1: Only ONE mutable reference at a time
let mut s = String::from("hello");
let r1 = &mut s;
// let r2 = &mut s; // ERROR: cannot borrow as mutable more than once
r1.push_str(" world");

// Rule 2: Can have multiple immutable references
let s = String::from("hello");
let r1 = &s;
let r2 = &s;
println!("{} and {}", r1, r2); // OK

// Rule 3: Cannot mix mutable and immutable references
let mut s = String::from("hello");
let r1 = &s;     // OK
let r2 = &s;     // OK
// let r3 = &mut s; // ERROR: cannot borrow as mutable
println!("{} and {}", r1, r2);

// Non-Lexical Lifetimes (NLL): references scope ends at last use
let mut s = String::from("hello");
let r1 = &s;
let r2 = &s;
println!("{} and {}", r1, r2);
// r1 and r2 no longer used here

let r3 = &mut s; // OK: immutable borrows are done
r3.push_str(" world");
```

## Lifetimes

### Lifetime Annotations

```rust
// Explicit lifetime annotation
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}

let string1 = String::from("long string");
let result;
{
    let string2 = String::from("short");
    result = longest(&string1, &string2);
    println!("Longest: {}", result);
} // string2 dropped, but result still valid because it refers to string1
```

### Lifetime in Structs

```rust
struct Excerpt<'a> {
    part: &'a str,
}

impl<'a> Excerpt<'a> {
    fn announce_and_return(&self) -> &str {
        println!("Attention!");
        self.part
    }
}

let novel = String::from("Call me Ishmael. Some years ago...");
let first_sentence = novel.split('.').next().unwrap();
let excerpt = Excerpt { part: first_sentence };
```

### Lifetime Elision Rules

```rust
// Compiler infers lifetimes in simple cases

// Input lifetime
fn first_word(s: &str) -> &str {
    // Compiler infers: fn first_word<'a>(s: &'a str) -> &'a str
    s.split_whitespace().next().unwrap_or("")
}

// Multiple inputs: ambiguous, need annotation
fn longest(x: &str, y: &str) -> &str {
    // ERROR: compiler can't infer
    if x.len() > y.len() { x } else { y }
}
```

### Static Lifetime

```rust
// 'static lifetime: lives for entire program
let s: &'static str = "String literals have static lifetime";

// Static variables
static GLOBAL: &str = "Global string";
```

## Smart Pointers

### Box<T> - Heap Allocation

```rust
// Allocate value on heap
let b = Box::new(5);
println!("b = {}", b);

// Use for recursive types
enum List {
    Cons(i32, Box<List>),
    Nil,
}

use List::{Cons, Nil};
let list = Cons(1, Box::new(Cons(2, Box::new(Cons(3, Box::new(Nil))))));

// Deref coercion
fn hello(name: &str) {
    println!("Hello, {}!", name);
}

let boxed_str = Box::new(String::from("Rust"));
hello(&boxed_str); // Box<String> -> &String -> &str
```

### Rc<T> - Reference Counting

```rust
use std::rc::Rc;

// Multiple owners
let a = Rc::new(5);
let b = Rc::clone(&a); // Increment reference count
let c = Rc::clone(&a);

println!("Count: {}", Rc::strong_count(&a)); // 3

// Example with list
#[derive(Debug)]
enum List {
    Cons(i32, Rc<List>),
    Nil,
}

use List::{Cons, Nil};

let a = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));
let b = Cons(3, Rc::clone(&a));
let c = Cons(4, Rc::clone(&a));
```

### Arc<T> - Atomic Reference Counting

```rust
use std::sync::Arc;
use std::thread;

// Thread-safe reference counting
let data = Arc::new(vec![1, 2, 3]);

let data1 = Arc::clone(&data);
let handle1 = thread::spawn(move || {
    println!("Thread 1: {:?}", data1);
});

let data2 = Arc::clone(&data);
let handle2 = thread::spawn(move || {
    println!("Thread 2: {:?}", data2);
});

handle1.join().unwrap();
handle2.join().unwrap();
```

### RefCell<T> - Interior Mutability

```rust
use std::cell::RefCell;

// Mutate through immutable reference
let data = RefCell::new(5);

*data.borrow_mut() += 1;
println!("{}", data.borrow()); // 6

// Runtime borrow checking
let a = data.borrow();
// let b = data.borrow_mut(); // PANIC: already borrowed
drop(a); // Release borrow
let b = data.borrow_mut(); // OK now
```

### Combining Rc and RefCell

```rust
use std::rc::Rc;
use std::cell::RefCell;

// Multiple owners with interior mutability
let value = Rc::new(RefCell::new(5));

let a = Rc::clone(&value);
*a.borrow_mut() += 10;

let b = Rc::clone(&value);
*b.borrow_mut() += 20;

println!("value: {}", value.borrow()); // 35
```

### Weak<T> - Weak References

```rust
use std::rc::{Rc, Weak};
use std::cell::RefCell;

#[derive(Debug)]
struct Node {
    value: i32,
    parent: RefCell<Weak<Node>>,
    children: RefCell<Vec<Rc<Node>>>,
}

let leaf = Rc::new(Node {
    value: 3,
    parent: RefCell::new(Weak::new()),
    children: RefCell::new(vec![]),
});

let branch = Rc::new(Node {
    value: 5,
    parent: RefCell::new(Weak::new()),
    children: RefCell::new(vec![Rc::clone(&leaf)]),
});

*leaf.parent.borrow_mut() = Rc::downgrade(&branch);

// Prevents reference cycles
```

## Raw Pointers

### Unsafe Pointers

```rust
let mut num = 5;

// Create raw pointers (safe)
let r1 = &num as *const i32;
let r2 = &mut num as *mut i32;

// Dereference raw pointers (unsafe)
unsafe {
    println!("r1: {}", *r1);
    *r2 = 10;
    println!("r2: {}", *r2);
}
```

## Memory Layout

### Size and Alignment

```rust
use std::mem;

println!("Size of i32: {}", mem::size_of::<i32>());
println!("Size of Box<i32>: {}", mem::size_of::<Box<i32>>());
println!("Size of &i32: {}", mem::size_of::<&i32>());
println!("Size of String: {}", mem::size_of::<String>());
println!("Size of Vec<i32>: {}", mem::size_of::<Vec<i32>>());

// Alignment
println!("Align of i32: {}", mem::align_of::<i32>());
```

### Stack vs Heap

```rust
// Stack allocation (fast, fixed size)
let x = 5;
let y = [1, 2, 3, 4, 5];

// Heap allocation (slower, dynamic size)
let s = String::from("hello");
let v = vec![1, 2, 3];
let b = Box::new(42);
```

## Drop Trait

```rust
struct CustomSmartPointer {
    data: String,
}

impl Drop for CustomSmartPointer {
    fn drop(&mut self) {
        println!("Dropping with data: {}", self.data);
    }
}

{
    let c = CustomSmartPointer {
        data: String::from("my stuff"),
    };
    println!("Created");
} // c.drop() called automatically
println!("Dropped");

// Manual drop
let c = CustomSmartPointer {
    data: String::from("manual drop"),
};
drop(c); // Explicitly drop early
// c no longer valid
```

## Copy vs Clone

```rust
// Copy: implicit bitwise copy
#[derive(Copy, Clone)]
struct Point {
    x: i32,
    y: i32,
}

let p1 = Point { x: 1, y: 2 };
let p2 = p1; // Copied
println!("{}, {}", p1.x, p2.x); // Both valid

// Clone: explicit deep copy
#[derive(Clone)]
struct Data {
    values: Vec<i32>,
}

let d1 = Data { values: vec![1, 2, 3] };
let d2 = d1.clone(); // Explicit clone
// d1 still valid
```

## Common Patterns

### Builder Pattern with Ownership

```rust
struct Config {
    name: String,
    timeout: u64,
}

impl Config {
    fn new() -> Self {
        Config {
            name: String::from("default"),
            timeout: 30,
        }
    }

    fn name(mut self, name: String) -> Self {
        self.name = name;
        self
    }

    fn timeout(mut self, timeout: u64) -> Self {
        self.timeout = timeout;
        self
    }
}

let config = Config::new()
    .name(String::from("app"))
    .timeout(60);
```

> **Good to know:** Rust has no garbage collector - memory is freed deterministically when owners go out of scope. The borrow checker ensures memory safety at compile time. Use `Box<T>` for heap allocation, `Rc<T>` for multiple owners (single-threaded), `Arc<T>` for thread-safe multiple owners, and `RefCell<T>` for interior mutability. Most of the time, you won't need smart pointers - regular references work fine.

---

## References

- [The Rust Book - Ownership](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html)
- [The Rust Book - References and Borrowing](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html)
- [The Rust Book - Lifetimes](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html)
- [The Rust Book - Smart Pointers](https://doc.rust-lang.org/book/ch15-00-smart-pointers.html)

---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
