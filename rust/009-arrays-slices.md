# Arrays/Slices/Lists/Vectors

## Arrays

Fixed-size, stack-allocated collections of elements of the same type.

### Array Declaration

```rust
// Type annotation: [T; N]
let arr: [i32; 5] = [1, 2, 3, 4, 5];

// Type inference
let numbers = [1, 2, 3, 4, 5];

// Initialize with same value
let zeros = [0; 10]; // [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
let threes = [3; 5]; // [3, 3, 3, 3, 3]
```

### Array Access

```rust
let arr = [10, 20, 30, 40, 50];

// Index access
let first = arr[0];  // 10
let last = arr[4];   // 50

// Runtime panic if index out of bounds
// let invalid = arr[10]; // panic!

// Safe access
let safe = arr.get(10); // None (Option<&i32>)
```

### Array Iteration

```rust
let arr = [1, 2, 3, 4, 5];

// By reference
for element in &arr {
    println!("{}", element);
}

// By value (if T implements Copy)
for element in arr {
    println!("{}", element);
}

// With index
for (i, element) in arr.iter().enumerate() {
    println!("arr[{}] = {}", i, element);
}
```

## Slices

Dynamically-sized view into a contiguous sequence. Does not own data.

### Slice Creation

```rust
let arr = [1, 2, 3, 4, 5];

// Slice of entire array
let slice: &[i32] = &arr;

// Slice with range
let partial = &arr[1..4];  // [2, 3, 4]
let first_three = &arr[..3]; // [1, 2, 3]
let last_two = &arr[3..];    // [4, 5]
let all = &arr[..];          // [1, 2, 3, 4, 5]
```

### Mutable Slices

```rust
let mut arr = [1, 2, 3, 4, 5];

// Mutable slice
let slice: &mut [i32] = &mut arr[..];
slice[0] = 10;
slice[1] = 20;

println!("{:?}", arr); // [10, 20, 3, 4, 5]
```

### Slice Methods

```rust
let arr = [1, 2, 3, 4, 5];
let slice = &arr[..];

// Length
assert_eq!(slice.len(), 5);

// First and last
assert_eq!(slice.first(), Some(&1));
assert_eq!(slice.last(), Some(&5));

// Check if empty
assert!(!slice.is_empty());

// Split slice
let (left, right) = slice.split_at(2);
// left = [1, 2], right = [3, 4, 5]

// Contains
assert!(slice.contains(&3));
```

## Vectors

Dynamically-sized, heap-allocated arrays.

### Vector Creation

```rust
// Empty vector
let v: Vec<i32> = Vec::new();

// Using vec! macro
let v = vec![1, 2, 3, 4, 5];

// With capacity
let mut v = Vec::with_capacity(10);

// From iterator
let v: Vec<i32> = (0..5).collect();
```

### Adding Elements

```rust
let mut v = Vec::new();

// Push to end
v.push(1);
v.push(2);
v.push(3);

// Extend with iterator
v.extend([4, 5, 6]);
v.extend(vec![7, 8, 9]);

// Insert at index
v.insert(0, 0); // Insert 0 at beginning
```

### Removing Elements

```rust
let mut v = vec![1, 2, 3, 4, 5];

// Pop from end (returns Option)
let last = v.pop(); // Some(5)

// Remove at index (returns element)
let removed = v.remove(1); // 2, v is now [1, 3, 4]

// Swap and remove (faster, changes order)
v.swap_remove(0); // Removes 1, v is now [4, 3]

// Clear all elements
v.clear();
```

### Vector Access

```rust
let v = vec![10, 20, 30, 40, 50];

// Index access (panics if out of bounds)
let third = v[2]; // 30

// Safe access with get
let third = v.get(2); // Some(&30)
let invalid = v.get(10); // None

// First and last
let first = v.first();  // Some(&10)
let last = v.last();    // Some(&50)
```

### Vector Iteration

```rust
let v = vec![1, 2, 3, 4, 5];

// Immutable iteration
for element in &v {
    println!("{}", element);
}

// Mutable iteration
let mut v = vec![1, 2, 3];
for element in &mut v {
    *element *= 2;
}
// v is now [2, 4, 6]

// Consuming iteration (takes ownership)
for element in v {
    println!("{}", element);
}
// v is no longer accessible
```

## Common Operations

### Capacity vs Length

```rust
let mut v = Vec::with_capacity(10);
assert_eq!(v.len(), 0);      // Number of elements
assert_eq!(v.capacity(), 10); // Allocated space

v.push(1);
assert_eq!(v.len(), 1);
assert_eq!(v.capacity(), 10); // Still 10

// Reserve more capacity
v.reserve(5);

// Shrink capacity to fit
v.shrink_to_fit();
```

### Sorting

```rust
let mut v = vec![3, 1, 4, 1, 5, 9];

// Sort ascending
v.sort();
// v = [1, 1, 3, 4, 5, 9]

// Sort descending
v.sort_by(|a, b| b.cmp(a));

// Sort by key
let mut people = vec!["Alice", "Bob", "Carol"];
people.sort_by_key(|name| name.len());
```

### Searching

```rust
let v = vec![1, 2, 3, 4, 5];

// Binary search (requires sorted array)
match v.binary_search(&3) {
    Ok(index) => println!("Found at index {}", index),
    Err(index) => println!("Not found, would be at {}", index),
}

// Linear search
assert!(v.contains(&3));
assert_eq!(v.iter().position(|&x| x == 3), Some(2));
```

### Filtering and Mapping

```rust
let v = vec![1, 2, 3, 4, 5];

// Filter
let evens: Vec<i32> = v.iter()
    .filter(|&&x| x % 2 == 0)
    .copied()
    .collect();
// evens = [2, 4]

// Map
let doubled: Vec<i32> = v.iter()
    .map(|&x| x * 2)
    .collect();
// doubled = [2, 4, 6, 8, 10]

// Filter and map
let result: Vec<i32> = v.iter()
    .filter(|&&x| x > 2)
    .map(|&x| x * 2)
    .collect();
// result = [6, 8, 10]
```

### Slicing Vectors

```rust
let v = vec![1, 2, 3, 4, 5];

// Get slice from vector
let slice: &[i32] = &v[1..4]; // [2, 3, 4]

// Vectors deref to slices
fn print_slice(s: &[i32]) {
    println!("{:?}", s);
}

print_slice(&v); // Works!
```

### Chunking

```rust
let v = vec![1, 2, 3, 4, 5, 6, 7, 8];

// Fixed-size chunks
for chunk in v.chunks(3) {
    println!("{:?}", chunk);
}
// [1, 2, 3]
// [4, 5, 6]
// [7, 8]

// Windows (overlapping)
for window in v.windows(3) {
    println!("{:?}", window);
}
// [1, 2, 3]
// [2, 3, 4]
// [3, 4, 5]
// ...
```

### Joining and Splitting

```rust
// Join
let parts = vec!["Hello", "World"];
let joined = parts.join(" "); // "Hello World"

// Split
let s = "one,two,three";
let parts: Vec<&str> = s.split(',').collect();
// ["one", "two", "three"]
```

## Multidimensional Arrays

```rust
// 2D array
let matrix: [[i32; 3]; 2] = [
    [1, 2, 3],
    [4, 5, 6],
];

let element = matrix[0][1]; // 2

// Vector of vectors
let mut grid: Vec<Vec<i32>> = vec![
    vec![1, 2, 3],
    vec![4, 5, 6],
    vec![7, 8, 9],
];

grid[1][2] = 10; // Modify element
```

> **Good to know:** Arrays have fixed size known at compile time and live on the stack. Vectors are heap-allocated and can grow. Slices are views into arrays or vectors without owning the data. When in doubt, use `Vec<T>` for flexibility. Vectors deref to slices, so you can pass `&Vec<T>` to functions expecting `&[T]`.

---

## References

- [The Rust Book - Arrays and Vectors](https://doc.rust-lang.org/book/ch08-01-vectors.html)
- [std::vec::Vec](https://doc.rust-lang.org/std/vec/struct.Vec.html)
- [std::primitive::slice](https://doc.rust-lang.org/std/primitive.slice.html)
- [std::primitive::array](https://doc.rust-lang.org/std/primitive.array.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
