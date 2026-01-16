# Modules/Packages

Rust's module system helps organize code into logical units.

## Modules

### Basic Module Definition

```rust
// Define a module
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}

        fn seat_at_table() {} // Private
    }

    mod serving {
        fn take_order() {}
        fn serve_order() {}
    }
}

// Use module items
pub fn eat_at_restaurant() {
    front_of_house::hosting::add_to_waitlist();
}
```

### Module File Structure

```rust
// src/lib.rs or src/main.rs
mod front_of_house; // Looks for src/front_of_house.rs or src/front_of_house/mod.rs

pub fn eat_at_restaurant() {
    front_of_house::hosting::add_to_waitlist();
}
```

```rust
// src/front_of_house.rs
pub mod hosting {
    pub fn add_to_waitlist() {}
}

pub mod serving {
    pub fn take_order() {}
}
```

### Nested Modules

```rust
// src/front_of_house.rs
pub mod hosting {
    pub fn add_to_waitlist() {}
}

// src/front_of_house/hosting.rs (alternative)
pub fn add_to_waitlist() {}

// For deeper nesting:
// src/
//   front_of_house/
//     mod.rs
//     hosting.rs
//     serving.rs
```

## Visibility

### Public and Private

```rust
mod outer {
    pub fn public_function() {
        println!("Public");
    }

    fn private_function() {
        println!("Private");
    }

    pub mod inner {
        pub fn inner_public() {
            // Can call private function in parent
            super::private_function();
        }
    }
}

// Can call
outer::public_function();
outer::inner::inner_public();

// Cannot call (private)
// outer::private_function();
```

### Pub(crate) and Pub(super)

```rust
mod parent {
    pub(crate) fn crate_visible() {
        // Visible within the crate
    }

    pub(super) fn parent_visible() {
        // Visible to parent module
    }

    pub fn public() {}

    fn private() {}

    mod child {
        pub fn use_functions() {
            super::parent_visible(); // OK
            super::crate_visible();  // OK
            super::public();         // OK
            super::private();        // OK (same module tree)
        }
    }
}
```

## Use Keyword

### Bringing Paths into Scope

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

// Bring into scope
use front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
}

// Or bring function directly
use front_of_house::hosting::add_to_waitlist;

pub fn eat_at_restaurant() {
    add_to_waitlist();
}
```

### Use with As

```rust
use std::io::Result as IoResult;
use std::fmt::Result as FmtResult;

fn function1() -> IoResult<()> {
    Ok(())
}

fn function2() -> FmtResult {
    Ok(())
}
```

### Re-exporting with Pub Use

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

// Re-export for external use
pub use front_of_house::hosting;

// External code can now use:
// my_crate::hosting::add_to_waitlist();
```

### Nested Paths

```rust
// Instead of:
// use std::io;
// use std::io::Write;
// use std::io::Read;

// Use nested path:
use std::io::{self, Write, Read};

// Glob operator (use sparingly)
use std::collections::*;
```

## Paths

### Absolute and Relative Paths

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path (from crate root)
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    front_of_house::hosting::add_to_waitlist();
}
```

### Super Keyword

```rust
mod parent {
    fn parent_function() {}

    mod child {
        fn child_function() {
            // Call parent's function
            super::parent_function();

            // Go up two levels
            super::super::other_function();
        }
    }
}

fn other_function() {}
```

## Crates

### Binary and Library Crates

```
my_project/
  Cargo.toml
  src/
    main.rs    # Binary crate root
    lib.rs     # Library crate root
```

```rust
// src/lib.rs (library)
pub mod modules {
    pub fn library_function() {
        println!("Library function");
    }
}
```

```rust
// src/main.rs (binary)
use my_project::modules;

fn main() {
    modules::library_function();
}
```

### Multiple Binary Crates

```
my_project/
  Cargo.toml
  src/
    main.rs         # Default binary
    lib.rs          # Library
    bin/
      another.rs    # Additional binary
      helper.rs     # Another binary
```

```bash
# Run specific binary
cargo run --bin another
```

## Workspace

### Workspace Structure

```toml
# Root Cargo.toml
[workspace]
members = [
    "crate1",
    "crate2",
    "common",
]
```

```
my_workspace/
  Cargo.toml       # Workspace manifest
  Cargo.lock       # Shared lock file
  crate1/
    Cargo.toml
    src/
      lib.rs
  crate2/
    Cargo.toml
    src/
      main.rs
  common/
    Cargo.toml
    src/
      lib.rs
```

### Using Workspace Members

```toml
# crate2/Cargo.toml
[dependencies]
crate1 = { path = "../crate1" }
common = { path = "../common" }
```

```rust
// crate2/src/main.rs
use crate1::function_from_crate1;
use common::shared_function;

fn main() {
    function_from_crate1();
    shared_function();
}
```

## Module Organization Patterns

### Flat Structure

```
src/
  main.rs
  utils.rs
  config.rs
  handlers.rs
```

```rust
// main.rs
mod utils;
mod config;
mod handlers;

fn main() {
    utils::helper();
}
```

### Nested Structure

```
src/
  main.rs
  utils/
    mod.rs
    string_utils.rs
    file_utils.rs
  config/
    mod.rs
    app_config.rs
```

```rust
// src/utils/mod.rs
pub mod string_utils;
pub mod file_utils;

// Re-export commonly used items
pub use string_utils::trim_whitespace;
```

```rust
// main.rs
mod utils;

fn main() {
    utils::string_utils::trim_whitespace();
    // or if re-exported:
    utils::trim_whitespace();
}
```

## External Crates

### Adding Dependencies

```toml
# Cargo.toml
[dependencies]
serde = "1.0"
tokio = { version = "1.0", features = ["full"] }
rand = "0.8"
```

### Using External Crates

```rust
use serde::{Serialize, Deserialize};
use rand::Rng;

#[derive(Serialize, Deserialize)]
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let mut rng = rand::thread_rng();
    let n: u32 = rng.gen();
    println!("Random: {}", n);
}
```

## Conditional Compilation

```rust
// Only compile for certain platforms
#[cfg(target_os = "linux")]
fn linux_specific() {}

#[cfg(target_os = "windows")]
fn windows_specific() {}

// Feature gates
#[cfg(feature = "advanced")]
mod advanced_features {
    pub fn advanced_function() {}
}

// Test-only code
#[cfg(test)]
mod tests {
    #[test]
    fn test_something() {}
}
```

## Prelude Pattern

```rust
// src/prelude.rs
pub use crate::error::Error;
pub use crate::config::Config;
pub use crate::utils::*;

// Users can import everything with:
// use my_crate::prelude::*;
```

> **Good to know:** Modules are private by default - use `pub` to make them public. The `mod` keyword declares a module, while `use` brings items into scope. For larger projects, organize code into separate files/directories. Use `pub use` to re-export items for a cleaner public API. Workspaces are great for managing multiple related crates with shared dependencies.

---

## References

- [The Rust Book - Modules](https://doc.rust-lang.org/book/ch07-00-managing-growing-projects-with-packages-crates-and-modules.html)
- [The Rust Book - Paths](https://doc.rust-lang.org/book/ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html)
- [The Cargo Book - Workspaces](https://doc.rust-lang.org/cargo/reference/workspaces.html)
- [Rust Reference - Visibility](https://doc.rust-lang.org/reference/visibility-and-privacy.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
