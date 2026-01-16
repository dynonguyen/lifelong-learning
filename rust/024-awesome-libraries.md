# Awesome Libraries

Popular and useful Rust crates from the ecosystem.

## Web Frameworks

### Actix-web

Fast, powerful web framework

```toml
[dependencies]
actix-web = "4"
```

```rust
use actix_web::{web, App, HttpResponse, HttpServer};

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    HttpServer::new(|| {
        App::new()
            .route("/", web::get().to(|| async { HttpResponse::Ok().body("Hello!") }))
    })
    .bind("127.0.0.1:8080")?
    .run()
    .await
}
```

### Axum

Ergonomic web framework from Tokio team

```toml
[dependencies]
axum = "0.7"
tokio = { version = "1", features = ["full"] }
```

```rust
use axum::{routing::get, Router};

#[tokio::main]
async fn main() {
    let app = Router::new().route("/", get(|| async { "Hello, World!" }));

    let listener = tokio::net::TcpListener::bind("127.0.0.1:3000").await.unwrap();
    axum::serve(listener, app).await.unwrap();
}
```

### Rocket

Type-safe web framework

```toml
[dependencies]
rocket = "0.5"
```

```rust
#[macro_use] extern crate rocket;

#[get("/")]
fn index() -> &'static str {
    "Hello, world!"
}

#[launch]
fn rocket() -> _ {
    rocket::build().mount("/", routes![index])
}
```

## Async Runtime

### Tokio

Most popular async runtime

```toml
[dependencies]
tokio = { version = "1", features = ["full"] }
```

```rust
#[tokio::main]
async fn main() {
    tokio::spawn(async {
        println!("Background task");
    });
}
```

### async-std

Alternative async runtime

```toml
[dependencies]
async-std = { version = "1", features = ["attributes"] }
```

```rust
#[async_std::main]
async fn main() {
    async_std::task::spawn(async {
        println!("Background task");
    });
}
```

## Serialization

### Serde

De-facto standard for serialization

```toml
[dependencies]
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
```

```rust
use serde::{Serialize, Deserialize};

#[derive(Serialize, Deserialize, Debug)]
struct User {
    name: String,
    age: u32,
}

let user = User { name: "Alice".to_string(), age: 30 };
let json = serde_json::to_string(&user)?;
let user: User = serde_json::from_str(&json)?;
```

### bincode

Binary serialization

```toml
[dependencies]
bincode = "1.3"
```

```rust
let encoded = bincode::serialize(&user)?;
let decoded: User = bincode::deserialize(&encoded)?;
```

## HTTP Clients

### reqwest

High-level HTTP client

```toml
[dependencies]
reqwest = { version = "0.11", features = ["json"] }
tokio = { version = "1", features = ["full"] }
```

```rust
let response = reqwest::get("https://api.github.com/repos/rust-lang/rust")
    .await?
    .json::<serde_json::Value>()
    .await?;
```

### hyper

Low-level HTTP implementation

```toml
[dependencies]
hyper = { version = "1", features = ["full"] }
```

## Database

### sqlx

Async SQL toolkit

```toml
[dependencies]
sqlx = { version = "0.7", features = ["runtime-tokio", "postgres"] }
```

```rust
use sqlx::PgPool;

let pool = PgPool::connect("postgres://localhost/db").await?;
let row: (i64,) = sqlx::query_as("SELECT $1")
    .bind(42_i64)
    .fetch_one(&pool)
    .await?;
```

### diesel

ORM and Query Builder

```toml
[dependencies]
diesel = { version = "2.1", features = ["postgres"] }
```

```rust
use diesel::prelude::*;

let results = users
    .filter(published.eq(true))
    .limit(5)
    .load::<User>(&mut conn)?;
```

### sea-orm

Async ORM

```toml
[dependencies]
sea-orm = "0.12"
```

## CLI Tools

### clap

Command line argument parser

```toml
[dependencies]
clap = { version = "4", features = ["derive"] }
```

```rust
use clap::Parser;

#[derive(Parser)]
struct Args {
    #[arg(short, long)]
    name: String,

    #[arg(short, long, default_value_t = 1)]
    count: u8,
}

let args = Args::parse();
```

### colored

Terminal colors

```toml
[dependencies]
colored = "2"
```

```rust
use colored::Colorize;

println!("{}", "Success!".green());
println!("{}", "Error!".red().bold());
```

### indicatif

Progress bars

```toml
[dependencies]
indicatif = "0.17"
```

```rust
use indicatif::ProgressBar;

let bar = ProgressBar::new(100);
for _ in 0..100 {
    bar.inc(1);
}
bar.finish();
```

## Testing

### mockall

Mocking library

```toml
[dev-dependencies]
mockall = "0.12"
```

```rust
use mockall::*;

#[automock]
trait MyTrait {
    fn foo(&self, x: u32) -> u32;
}
```

### proptest

Property-based testing

```toml
[dev-dependencies]
proptest = "1.0"
```

```rust
use proptest::prelude::*;

proptest! {
    #[test]
    fn test_reversing(s in "\\PC*") {
        let reversed: String = s.chars().rev().collect();
        let double_reversed: String = reversed.chars().rev().collect();
        assert_eq!(s, double_reversed);
    }
}
```

### criterion

Benchmarking

```toml
[dev-dependencies]
criterion = "0.5"
```

## Error Handling

### anyhow

Easy error handling for applications

```toml
[dependencies]
anyhow = "1.0"
```

```rust
use anyhow::{Context, Result};

fn main() -> Result<()> {
    let content = std::fs::read_to_string("file.txt")
        .context("Failed to read file")?;
    Ok(())
}
```

### thiserror

Error derive macros for libraries

```toml
[dependencies]
thiserror = "1.0"
```

```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum MyError {
    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),

    #[error("Invalid value: {0}")]
    Invalid(String),
}
```

## Logging

### log

Logging facade

```toml
[dependencies]
log = "0.4"
env_logger = "0.11"
```

```rust
use log::{info, warn, error};

env_logger::init();
info!("Application started");
```

### tracing

Structured logging and diagnostics

```toml
[dependencies]
tracing = "0.1"
tracing-subscriber = "0.3"
```

```rust
use tracing::{info, instrument};

#[instrument]
fn my_function(arg: u32) {
    info!("Processing");
}
```

## Parsing

### nom

Parser combinators

```toml
[dependencies]
nom = "7"
```

```rust
use nom::{
    bytes::complete::tag,
    IResult,
};

fn parse_hello(input: &str) -> IResult<&str, &str> {
    tag("hello")(input)
}
```

### pest

PEG parser

```toml
[dependencies]
pest = "2"
pest_derive = "2"
```

### syn

Rust syntax parser

```toml
[dependencies]
syn = { version = "2", features = ["full"] }
```

## Concurrency

### rayon

Data parallelism

```toml
[dependencies]
rayon = "1.7"
```

```rust
use rayon::prelude::*;

let sum: i32 = (0..1000)
    .into_par_iter()
    .map(|x| x * 2)
    .sum();
```

### crossbeam

Advanced concurrency primitives

```toml
[dependencies]
crossbeam = "0.8"
```

```rust
use crossbeam::channel;

let (tx, rx) = channel::unbounded();
tx.send(42).unwrap();
```

## Crypto

### ring

Cryptographic operations

```toml
[dependencies]
ring = "0.17"
```

### sha2

SHA-2 hashing

```toml
[dependencies]
sha2 = "0.10"
```

```rust
use sha2::{Sha256, Digest};

let mut hasher = Sha256::new();
hasher.update(b"hello world");
let result = hasher.finalize();
```

## Date/Time

### chrono

Date and time

```toml
[dependencies]
chrono = "0.4"
```

```rust
use chrono::{DateTime, Utc};

let now: DateTime<Utc> = Utc::now();
println!("{}", now.format("%Y-%m-%d %H:%M:%S"));
```

### time

Modern date/time library

```toml
[dependencies]
time = "0.3"
```

## Configuration

### config

Configuration management

```toml
[dependencies]
config = "0.13"
```

### dotenv

Load environment from .env

```toml
[dependencies]
dotenv = "0.15"
```

```rust
dotenv::dotenv().ok();
let api_key = std::env::var("API_KEY")?;
```

## Templating

### tera

Jinja2-like templates

```toml
[dependencies]
tera = "1"
```

### handlebars

Handlebars templates

```toml
[dependencies]
handlebars = "5"
```

## Image Processing

### image

Image processing

```toml
[dependencies]
image = "0.24"
```

```rust
use image::ImageBuffer;

let img = image::open("input.png")?;
let resized = img.resize(100, 100, image::imageops::FilterType::Lanczos3);
resized.save("output.png")?;
```

## Regular Expressions

### regex

Regular expressions

```toml
[dependencies]
regex = "1"
```

```rust
use regex::Regex;

let re = Regex::new(r"^\d{4}-\d{2}-\d{2}$")?;
assert!(re.is_match("2023-12-25"));
```

## Compression

### flate2

DEFLATE compression

```toml
[dependencies]
flate2 = "1"
```

## UUID

### uuid

UUID generation

```toml
[dependencies]
uuid = { version = "1", features = ["v4"] }
```

```rust
use uuid::Uuid;

let id = Uuid::new_v4();
println!("{}", id);
```

## JSON

### serde_json

JSON support

```toml
[dependencies]
serde_json = "1.0"
```

```rust
let json = serde_json::json!({
    "name": "John",
    "age": 30
});
```

## GraphQL

### async-graphql

GraphQL server library

```toml
[dependencies]
async-graphql = "7"
```

## WebSocket

### tokio-tungstenite

WebSocket implementation

```toml
[dependencies]
tokio-tungstenite = "0.21"
```

## Game Development

### bevy

Game engine

```toml
[dependencies]
bevy = "0.12"
```

### ggez

Lightweight game framework

```toml
[dependencies]
ggez = "0.9"
```

> **Good to know:** Check crates.io for the latest versions and documentation. Most popular crates follow semantic versioning. Use `cargo add <crate>` to easily add dependencies. Read the docs.rs documentation for each crate. Many crates are feature-gated - only enable features you need. The Rust ecosystem is growing rapidly - new crates appear frequently.

---

## References

- [crates.io](https://crates.io/)
- [docs.rs](https://docs.rs/)
- [Awesome Rust](https://github.com/rust-unofficial/awesome-rust)
- [Lib.rs](https://lib.rs/)
- [This Week in Rust](https://this-week-in-rust.org/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
