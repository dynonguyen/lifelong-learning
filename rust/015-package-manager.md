# Package Manager

Cargo is Rust's build system and package manager.

## Cargo Basics

### Creating Projects

```bash
# Create binary project
cargo new my_project
cd my_project

# Create library project
cargo new --lib my_library

# Create in current directory
cargo init

# Create library in current directory
cargo init --lib
```

### Project Structure

```
my_project/
  Cargo.toml      # Package manifest
  Cargo.lock      # Dependency lock file
  src/
    main.rs       # Binary entry point
    lib.rs        # Library entry point (if both)
  tests/          # Integration tests
  benches/        # Benchmarks
  examples/       # Example code
```

### Cargo.toml

```toml
[package]
name = "my_project"
version = "0.1.0"
edition = "2021"
authors = ["Your Name <you@example.com>"]
license = "MIT"
description = "A short description"
repository = "https://github.com/username/repo"
keywords = ["keyword1", "keyword2"]
categories = ["command-line-utilities"]

[dependencies]
serde = "1.0"
tokio = { version = "1.0", features = ["full"] }
rand = "0.8"

[dev-dependencies]
criterion = "0.5"

[build-dependencies]
cc = "1.0"

[features]
default = ["feature1"]
feature1 = []
feature2 = ["dep:optional_dep"]

[profile.release]
opt-level = 3
lto = true
```

## Building and Running

### Build Commands

```bash
# Build in debug mode
cargo build

# Build in release mode (optimized)
cargo build --release

# Build all targets
cargo build --all-targets

# Check if code compiles (faster than build)
cargo check

# Build with specific features
cargo build --features "feature1,feature2"
cargo build --all-features
cargo build --no-default-features
```

### Running

```bash
# Run the binary
cargo run

# Run with arguments
cargo run -- arg1 arg2

# Run in release mode
cargo run --release

# Run specific binary
cargo run --bin binary_name

# Run example
cargo run --example example_name
```

## Dependencies

### Adding Dependencies

```bash
# Add dependency via CLI
cargo add serde

# Add with features
cargo add tokio --features full

# Add dev dependency
cargo add --dev criterion

# Add build dependency
cargo add --build cc

# Add with version
cargo add serde@1.0
```

### Dependency Sources

```toml
[dependencies]
# From crates.io
serde = "1.0"

# From git repository
my_crate = { git = "https://github.com/user/repo" }

# From git with branch/tag/commit
my_crate = { git = "https://github.com/user/repo", branch = "main" }
my_crate = { git = "https://github.com/user/repo", tag = "v1.0" }
my_crate = { git = "https://github.com/user/repo", rev = "abc123" }

# From local path
my_crate = { path = "../my_crate" }

# From crates.io with path override (for local development)
serde = { version = "1.0", path = "../serde" }

# Optional dependency
optional_crate = { version = "1.0", optional = true }
```

### Version Requirements

```toml
[dependencies]
# Exact version
serde = "=1.0.0"

# >= version
serde = ">=1.0.0"

# Compatible version (^)
serde = "^1.0.0"  # >= 1.0.0 and < 2.0.0
serde = "1.0"     # Same as ^1.0.0

# Tilde requirement (~)
serde = "~1.0.5"  # >= 1.0.5 and < 1.1.0

# Wildcard
serde = "1.*"     # >= 1.0.0 and < 2.0.0

# Multiple requirements
serde = ">= 1.0, < 2.0"
```

### Updating Dependencies

```bash
# Update dependencies to latest compatible versions
cargo update

# Update specific dependency
cargo update serde

# Update to latest breaking version
cargo update serde --aggressive
```

## Testing

### Running Tests

```bash
# Run all tests
cargo test

# Run specific test
cargo test test_name

# Run tests matching pattern
cargo test pattern

# Run tests with output
cargo test -- --nocapture

# Run tests single-threaded
cargo test -- --test-threads=1

# Run doc tests only
cargo test --doc

# Run integration tests only
cargo test --test integration_test

# Run lib tests only
cargo test --lib
```

### Test Organization

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
    #[ignore]
    fn expensive_test() {
        // Ignored by default
    }

    #[test]
    #[should_panic]
    fn test_panic() {
        panic!("This should panic");
    }
}
```

```rust
// tests/integration_test.rs
use my_project;

#[test]
fn integration_test() {
    // Test the public API
}
```

## Documentation

### Generating Docs

```bash
# Generate documentation
cargo doc

# Open docs in browser
cargo doc --open

# Generate docs for dependencies too
cargo doc --document-private-items

# Generate docs without dependencies
cargo doc --no-deps
```

### Writing Documentation

````rust
/// This function adds two numbers
///
/// # Examples
///
/// ```
/// let result = my_crate::add(2, 2);
/// assert_eq!(result, 4);
/// ```
///
/// # Panics
///
/// This function will panic if overflow occurs
///
/// # Errors
///
/// Returns error if...
///
/// # Safety
///
/// This function is safe because...
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
````

## Publishing

### Preparing for Publish

```toml
[package]
name = "my_crate"
version = "0.1.0"
edition = "2021"
authors = ["Name <email@example.com>"]
license = "MIT OR Apache-2.0"
description = "A short description"
documentation = "https://docs.rs/my_crate"
homepage = "https://github.com/user/my_crate"
repository = "https://github.com/user/my_crate"
readme = "README.md"
keywords = ["keyword1", "keyword2", "keyword3"]
categories = ["command-line-utilities"]
```

### Publishing to crates.io

```bash
# Login to crates.io
cargo login <api-token>

# Dry run
cargo publish --dry-run

# Publish
cargo publish

# Yank a version (prevents new projects from using it)
cargo yank --vers 0.1.0

# Un-yank
cargo yank --vers 0.1.0 --undo
```

## Workspaces

### Creating Workspace

```toml
# Root Cargo.toml
[workspace]
members = ["crate1", "crate2"]
resolver = "2"

[workspace.dependencies]
serde = "1.0"

[workspace.package]
version = "0.1.0"
edition = "2021"
```

### Workspace Commands

```bash
# Build all workspace members
cargo build --workspace

# Test all workspace members
cargo test --workspace

# Update all workspace dependencies
cargo update --workspace

# Build specific package
cargo build -p crate1

# Run binary from package
cargo run -p crate2
```

## Custom Commands

### Cargo Plugins

```bash
# Install cargo-watch (auto-rebuild on changes)
cargo install cargo-watch
cargo watch -x run

# Install cargo-edit (easier dependency management)
cargo install cargo-edit
cargo add serde
cargo rm serde
cargo upgrade

# Install cargo-expand (expand macros)
cargo install cargo-expand
cargo expand

# Install cargo-clippy (linter)
rustup component add clippy
cargo clippy

# Install cargo-fmt (formatter)
rustup component add rustfmt
cargo fmt

# Install cargo-audit (security audits)
cargo install cargo-audit
cargo audit

# Install cargo-outdated (check outdated deps)
cargo install cargo-outdated
cargo outdated

# Install cargo-tree (dependency tree)
cargo tree
```

## Build Scripts

### build.rs

```rust
// build.rs (in project root)
fn main() {
    // Set environment variables for compile-time
    println!("cargo:rustc-env=BUILD_TIME={}", current_time());

    // Tell cargo to rerun if specific files change
    println!("cargo:rerun-if-changed=build.rs");
    println!("cargo:rerun-if-changed=src/config.rs");

    // Link to C library
    println!("cargo:rustc-link-lib=static=mylib");
    println!("cargo:rustc-link-search=native=/path/to/lib");
}

fn current_time() -> String {
    // Return current time as string
    String::new()
}
```

## Configuration

### .cargo/config.toml

```toml
# .cargo/config.toml
[build]
target = "x86_64-unknown-linux-gnu"
rustflags = ["-C", "target-cpu=native"]

[target.x86_64-unknown-linux-gnu]
linker = "clang"

[alias]
b = "build"
r = "run"
t = "test"
br = "build --release"

[profile.dev]
opt-level = 0

[profile.release]
opt-level = 3
lto = true
```

## Cross-compilation

```bash
# Add target
rustup target add x86_64-pc-windows-gnu

# Build for target
cargo build --target x86_64-pc-windows-gnu

# List installed targets
rustup target list --installed
```

> **Good to know:** Always use `cargo check` during development - it's much faster than `cargo build`. The `Cargo.lock` file should be committed for binaries but not for libraries. Use `cargo clippy` regularly for lint warnings. Use workspaces for managing multiple related crates. Semantic versioning is enforced on crates.io - breaking changes require a major version bump.

---

## References

- [The Cargo Book](https://doc.rust-lang.org/cargo/)
- [Cargo Reference](https://doc.rust-lang.org/cargo/reference/index.html)
- [crates.io](https://crates.io/)
- [Cargo Commands](https://doc.rust-lang.org/cargo/commands/index.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
