# Map/HashMap/Dictionary/Set

## HashMap

Hash map is Rust's key-value store, similar to dictionaries in other languages.

### Creating HashMaps

```rust
use std::collections::HashMap;

// Create empty HashMap
let mut map: HashMap<String, i32> = HashMap::new();

// Insert values
map.insert(String::from("Blue"), 10);
map.insert(String::from("Red"), 25);

// From iterator
let teams = vec![String::from("Blue"), String::from("Red")];
let scores = vec![10, 50];
let map: HashMap<_, _> = teams.iter().zip(scores.iter()).collect();

// From array (Rust 1.56+)
let map = HashMap::from([
    ("Blue", 10),
    ("Red", 50),
]);
```

### Accessing Values

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Red"), 50);

// Get (returns Option)
let score = scores.get("Blue"); // Some(&10)
let missing = scores.get("Green"); // None

// Unwrap or default
let blue_score = scores.get("Blue").unwrap_or(&0);

// Index access (panics if key doesn't exist)
// let score = scores["Blue"]; // Use with caution

// Check if key exists
if scores.contains_key("Blue") {
    println!("Blue team exists");
}
```

### Updating Values

```rust
let mut map = HashMap::new();

// Insert or update
map.insert("key", 10);
map.insert("key", 20); // Overwrites to 20

// Insert only if key doesn't exist
map.entry("key").or_insert(30); // Does nothing, key exists

// Insert if not exists
let value = map.entry("new_key").or_insert(50);
*value += 10; // Modify the value

// Update based on old value
let text = "hello world wonderful world";
let mut word_count = HashMap::new();

for word in text.split_whitespace() {
    let count = word_count.entry(word).or_insert(0);
    *count += 1;
}
// {"hello": 1, "world": 2, "wonderful": 1}
```

### Iterating HashMaps

```rust
let mut map = HashMap::new();
map.insert("a", 1);
map.insert("b", 2);
map.insert("c", 3);

// Iterate over key-value pairs
for (key, value) in &map {
    println!("{}: {}", key, value);
}

// Iterate over keys
for key in map.keys() {
    println!("{}", key);
}

// Iterate over values
for value in map.values() {
    println!("{}", value);
}

// Mutable iteration
for (key, value) in &mut map {
    *value *= 2;
}
```

### Removing Values

```rust
let mut map = HashMap::new();
map.insert("a", 1);
map.insert("b", 2);

// Remove key-value pair
let removed = map.remove("a"); // Some(1)
let missing = map.remove("c"); // None

// Clear all entries
map.clear();
```

### HashMap with Custom Types

```rust
use std::collections::HashMap;

#[derive(Debug, Hash, Eq, PartialEq)]
struct Person {
    name: String,
    age: u32,
}

let mut people = HashMap::new();

let alice = Person {
    name: String::from("Alice"),
    age: 30,
};

people.insert(alice, "Engineer");
```

## BTreeMap

Sorted map based on B-Tree.

```rust
use std::collections::BTreeMap;

let mut map = BTreeMap::new();
map.insert(3, "three");
map.insert(1, "one");
map.insert(2, "two");

// Iterates in sorted order by key
for (key, value) in &map {
    println!("{}: {}", key, value);
}
// Output: 1: one, 2: two, 3: three

// Range queries
let range = map.range(1..=2);
for (key, value) in range {
    println!("{}: {}", key, value);
}
```

## HashSet

Collection of unique values (set).

### Creating HashSets

```rust
use std::collections::HashSet;

// Create empty set
let mut set: HashSet<i32> = HashSet::new();

// From array
let set: HashSet<_> = [1, 2, 3, 4, 5].iter().cloned().collect();

// From Vec
let vec = vec![1, 2, 3, 3, 4];
let set: HashSet<_> = vec.into_iter().collect(); // Removes duplicates
```

### HashSet Operations

```rust
let mut set = HashSet::new();

// Insert
set.insert(1);
set.insert(2);
set.insert(2); // Duplicate, no effect

// Check membership
assert!(set.contains(&1));
assert!(!set.contains(&3));

// Remove
set.remove(&1);

// Length
assert_eq!(set.len(), 1);

// Check if empty
if set.is_empty() {
    println!("Set is empty");
}
```

### Set Operations

```rust
let set1: HashSet<_> = [1, 2, 3, 4].iter().cloned().collect();
let set2: HashSet<_> = [3, 4, 5, 6].iter().cloned().collect();

// Union
let union: HashSet<_> = set1.union(&set2).cloned().collect();
// {1, 2, 3, 4, 5, 6}

// Intersection
let intersection: HashSet<_> = set1.intersection(&set2).cloned().collect();
// {3, 4}

// Difference
let difference: HashSet<_> = set1.difference(&set2).cloned().collect();
// {1, 2}

// Symmetric difference
let sym_diff: HashSet<_> = set1.symmetric_difference(&set2).cloned().collect();
// {1, 2, 5, 6}

// Subset/Superset
assert!(set1.is_subset(&set1));
assert!(set1.is_superset(&set1));
```

### Iterating HashSets

```rust
let set: HashSet<_> = [1, 2, 3, 4, 5].iter().cloned().collect();

// Iterate over values (unordered)
for value in &set {
    println!("{}", value);
}

// Consuming iteration
for value in set {
    println!("{}", value);
} // set is consumed
```

## BTreeSet

Sorted set based on B-Tree.

```rust
use std::collections::BTreeSet;

let mut set = BTreeSet::new();
set.insert(3);
set.insert(1);
set.insert(2);

// Iterates in sorted order
for value in &set {
    println!("{}", value);
}
// Output: 1, 2, 3

// Range queries
let range = set.range(1..=2);
for value in range {
    println!("{}", value);
}
```

## Choosing the Right Collection

```rust
use std::collections::{HashMap, BTreeMap, HashSet, BTreeSet};

// HashMap: Fast lookup, no ordering
let mut hash_map = HashMap::new();
hash_map.insert("key", "value");

// BTreeMap: Sorted by key, range queries
let mut btree_map = BTreeMap::new();
btree_map.insert(1, "one");

// HashSet: Fast membership testing, no ordering
let mut hash_set = HashSet::new();
hash_set.insert(42);

// BTreeSet: Sorted, range queries
let mut btree_set = BTreeSet::new();
btree_set.insert(42);
```

## Performance Considerations

```rust
use std::collections::HashMap;

// Pre-allocate capacity
let mut map = HashMap::with_capacity(100);

// Check capacity
println!("Capacity: {}", map.capacity());

// Shrink to fit
map.shrink_to_fit();

// Reserve more capacity
map.reserve(50);
```

## Custom Hashers

```rust
use std::collections::HashMap;
use std::hash::BuildHasherDefault;
use std::hash::Hasher;

// Using a different hasher (e.g., FxHasher from rustc-hash)
// let mut map = HashMap::with_hasher(BuildHasherDefault::<FxHasher>::default());

// Default hasher (SipHash) is cryptographically secure but slower
// Consider alternatives for non-security-critical use cases
```

## Entry API

```rust
use std::collections::HashMap;

let mut map = HashMap::new();

// Complex update logic
match map.entry("key") {
    std::collections::hash_map::Entry::Occupied(entry) => {
        *entry.into_mut() += 1;
    }
    std::collections::hash_map::Entry::Vacant(entry) => {
        entry.insert(1);
    }
}

// Or use or_insert
*map.entry("key").or_insert(0) += 1;

// Or insert with function
map.entry("key").or_insert_with(|| {
    // Expensive computation
    42
});
```

## Removing Duplicates

```rust
use std::collections::HashSet;

let numbers = vec![1, 2, 3, 2, 4, 3, 5];

// Convert to set and back
let unique: Vec<_> = numbers.into_iter().collect::<HashSet<_>>().into_iter().collect();
// [1, 2, 3, 4, 5] (order may vary)

// Preserve order with Vec and contains
let mut unique = Vec::new();
for num in numbers {
    if !unique.contains(&num) {
        unique.push(num);
    }
}
```

> **Good to know:** `HashMap` and `HashSet` require keys to implement `Hash` and `Eq` traits. Use `BTreeMap`/`BTreeSet` when you need sorted order or range queries. HashMap has O(1) average lookup time, BTreeMap has O(log n). For small collections (<20 elements), a `Vec` with linear search might be faster than a HashMap due to cache locality.

---

## References

- [The Rust Book - Hash Maps](https://doc.rust-lang.org/book/ch08-03-hash-maps.html)
- [std::collections::HashMap](https://doc.rust-lang.org/std/collections/struct.HashMap.html)
- [std::collections::HashSet](https://doc.rust-lang.org/std/collections/struct.HashSet.html)
- [std::collections::BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html)
- [std::collections::BTreeSet](https://doc.rust-lang.org/std/collections/struct.BTreeSet.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
