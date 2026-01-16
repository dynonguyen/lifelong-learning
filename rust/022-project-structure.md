# Project Structure

## Binary Project Structure

### Basic Binary

```
my_binary/
├── Cargo.toml
├── Cargo.lock
├── src/
│   └── main.rs
├── tests/
│   └── integration_test.rs
└── examples/
    └── example.rs
```

```toml
# Cargo.toml
[package]
name = "my_binary"
version = "0.1.0"
edition = "2021"

[[bin]]
name = "my_binary"
path = "src/main.rs"
```

### Binary with Modules

```
my_binary/
├── Cargo.toml
├── src/
│   ├── main.rs
│   ├── config.rs
│   ├── handlers/
│   │   ├── mod.rs
│   │   ├── user.rs
│   │   └── auth.rs
│   └── utils/
│       ├── mod.rs
│       └── logger.rs
```

```rust
// src/main.rs
mod config;
mod handlers;
mod utils;

fn main() {
    let config = config::load();
    handlers::start_server(config);
}
```

## Library Project Structure

### Basic Library

```
my_library/
├── Cargo.toml
├── src/
│   └── lib.rs
├── tests/
│   └── integration_test.rs
├── examples/
│   └── basic_usage.rs
└── benches/
    └── benchmark.rs
```

```toml
# Cargo.toml
[package]
name = "my_library"
version = "0.1.0"
edition = "2021"

[lib]
name = "my_library"
path = "src/lib.rs"
```

### Library with Modules

```
my_library/
├── Cargo.toml
├── src/
│   ├── lib.rs
│   ├── parser.rs
│   ├── executor.rs
│   └── utils/
│       ├── mod.rs
│       ├── string.rs
│       └── file.rs
├── tests/
│   ├── parser_tests.rs
│   ├── executor_tests.rs
│   └── common/
│       └── mod.rs
└── examples/
    ├── basic.rs
    └── advanced.rs
```

```rust
// src/lib.rs
pub mod parser;
pub mod executor;
mod utils;

// Re-export commonly used items
pub use parser::Parser;
pub use executor::Executor;

// Prelude module for convenient imports
pub mod prelude {
    pub use crate::parser::*;
    pub use crate::executor::*;
}
```

## Mixed Binary and Library

### Binary Using Library

```
my_project/
├── Cargo.toml
├── src/
│   ├── main.rs      # Binary entry point
│   ├── lib.rs       # Library entry point
│   ├── core.rs      # Shared logic
│   └── cli.rs       # CLI-specific code
```

```rust
// src/lib.rs
pub mod core;

pub fn library_function() {
    core::shared_logic();
}

// src/main.rs
use my_project::core;

fn main() {
    // Use library code
    my_project::library_function();

    // CLI-specific code
    cli::run();
}

mod cli;
```

## Workspace Structure

### Multi-Crate Workspace

```
my_workspace/
├── Cargo.toml       # Workspace manifest
├── Cargo.lock
├── crates/
│   ├── core/
│   │   ├── Cargo.toml
│   │   └── src/
│   │       └── lib.rs
│   ├── api/
│   │   ├── Cargo.toml
│   │   └── src/
│   │       ├── main.rs
│   │       └── handlers.rs
│   └── cli/
│       ├── Cargo.toml
│       └── src/
│           └── main.rs
├── tests/
│   └── integration/
└── docs/
```

```toml
# Root Cargo.toml
[workspace]
members = [
    "crates/core",
    "crates/api",
    "crates/cli",
]
resolver = "2"

[workspace.dependencies]
tokio = { version = "1", features = ["full"] }
serde = { version = "1.0", features = ["derive"] }

[workspace.package]
version = "0.1.0"
edition = "2021"
authors = ["Your Name <you@example.com>"]
license = "MIT"
```

```toml
# crates/api/Cargo.toml
[package]
name = "my-api"
version.workspace = true
edition.workspace = true

[dependencies]
my-core = { path = "../core" }
tokio.workspace = true
serde.workspace = true
```

## Large Project Structure

### Complex Application

```
my_app/
├── Cargo.toml
├── .cargo/
│   └── config.toml
├── src/
│   ├── main.rs
│   ├── lib.rs
│   ├── config/
│   │   ├── mod.rs
│   │   ├── app.rs
│   │   └── database.rs
│   ├── domain/
│   │   ├── mod.rs
│   │   ├── user.rs
│   │   ├── product.rs
│   │   └── order.rs
│   ├── services/
│   │   ├── mod.rs
│   │   ├── user_service.rs
│   │   └── order_service.rs
│   ├── repository/
│   │   ├── mod.rs
│   │   ├── user_repo.rs
│   │   └── order_repo.rs
│   ├── api/
│   │   ├── mod.rs
│   │   ├── routes.rs
│   │   └── handlers/
│   │       ├── mod.rs
│   │       ├── user.rs
│   │       └── order.rs
│   └── utils/
│       ├── mod.rs
│       ├── error.rs
│       └── logger.rs
├── migrations/
│   ├── 001_init.sql
│   └── 002_users.sql
├── tests/
│   ├── api_tests.rs
│   ├── service_tests.rs
│   └── common/
│       └── mod.rs
├── benches/
│   └── performance.rs
├── examples/
│   ├── basic.rs
│   └── advanced.rs
├── docs/
│   └── architecture.md
└── scripts/
    ├── build.sh
    └── deploy.sh
```

## Feature Organization

### Feature-Based Structure

```
my_service/
├── Cargo.toml
├── src/
│   ├── main.rs
│   ├── lib.rs
│   ├── features/
│   │   ├── mod.rs
│   │   ├── users/
│   │   │   ├── mod.rs
│   │   │   ├── model.rs
│   │   │   ├── service.rs
│   │   │   ├── repository.rs
│   │   │   └── handlers.rs
│   │   └── products/
│   │       ├── mod.rs
│   │       ├── model.rs
│   │       ├── service.rs
│   │       ├── repository.rs
│   │       └── handlers.rs
│   └── shared/
│       ├── mod.rs
│       ├── database.rs
│       └── auth.rs
```

## Configuration Files

### Essential Config Files

```
project/
├── Cargo.toml
├── .gitignore
├── .cargo/
│   └── config.toml
├── rustfmt.toml
├── clippy.toml
├── .github/
│   └── workflows/
│       └── ci.yml
└── README.md
```

### .gitignore

```gitignore
# Cargo
/target/
Cargo.lock  # For libraries (include for binaries)

# IDE
.idea/
.vscode/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Environment
.env
.env.local

# Test output
*.profraw
*.profdata
```

### rustfmt.toml

```toml
max_width = 100
hard_tabs = false
tab_spaces = 4
newline_style = "Unix"
use_small_heuristics = "Default"
edition = "2021"
```

### clippy.toml

```toml
cognitive-complexity-threshold = 30
```

## Multiple Binaries

```
project/
├── Cargo.toml
├── src/
│   ├── main.rs          # Default binary
│   ├── lib.rs           # Shared library code
│   └── bin/
│       ├── server.rs    # Additional binary
│       ├── worker.rs    # Additional binary
│       └── cli.rs       # Additional binary
```

```toml
# Cargo.toml
[package]
name = "my-project"

[[bin]]
name = "my-project"
path = "src/main.rs"

[[bin]]
name = "server"
path = "src/bin/server.rs"

[[bin]]
name = "worker"
path = "src/bin/worker.rs"
```

```bash
# Build specific binary
cargo build --bin server

# Run specific binary
cargo run --bin worker
```

## Examples Directory

```
examples/
├── basic.rs
├── advanced.rs
└── custom_config/
    ├── main.rs
    └── config.toml
```

```rust
// examples/basic.rs
use my_library;

fn main() {
    my_library::run_example();
}
```

```bash
# Run example
cargo run --example basic
```

## Tests Organization

### Integration Tests

```
tests/
├── integration_test.rs
├── api_test.rs
├── database_test.rs
└── common/
    ├── mod.rs
    └── fixtures.rs
```

```rust
// tests/common/mod.rs
pub fn setup() {
    // Shared test setup
}

// tests/integration_test.rs
mod common;

#[test]
fn test_something() {
    common::setup();
    // Test code
}
```

## Documentation Structure

```
docs/
├── architecture.md
├── api.md
├── development.md
├── deployment.md
└── images/
    └── diagram.png
```

## Resource Files

```
project/
├── resources/
│   ├── config/
│   │   ├── dev.toml
│   │   ├── prod.toml
│   │   └── test.toml
│   ├── templates/
│   │   └── email.html
│   └── static/
│       ├── css/
│       └── js/
```

## Build Scripts

```
project/
├── build.rs          # Cargo build script
├── scripts/
│   ├── setup.sh
│   ├── test.sh
│   └── deploy.sh
```

```rust
// build.rs
fn main() {
    println!("cargo:rerun-if-changed=build.rs");
    // Build-time code generation
}
```

## Cross-Platform Structure

```rust
// src/platform/mod.rs
#[cfg(target_os = "linux")]
mod linux;

#[cfg(target_os = "windows")]
mod windows;

#[cfg(target_os = "macos")]
mod macos;

#[cfg(target_os = "linux")]
pub use self::linux::*;

#[cfg(target_os = "windows")]
pub use self::windows::*;

#[cfg(target_os = "macos")]
pub use self::macos::*;
```

> **Good to know:** Use workspaces for managing multiple related crates. Keep binaries thin - put logic in libraries. Use `src/bin/` for multiple binaries in one project. Organize by feature for domain-driven design. Keep configuration files at the project root. Use examples to demonstrate library usage. Place shared test utilities in `tests/common/`. Follow the community conventions for familiar project layouts.

---

## References

- [The Cargo Book - Project Layout](https://doc.rust-lang.org/cargo/guide/project-layout.html)
- [Cargo Book - Workspaces](https://doc.rust-lang.org/cargo/reference/workspaces.html)
- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/)
- [Project Structure Examples](https://github.com/rust-unofficial/patterns)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
