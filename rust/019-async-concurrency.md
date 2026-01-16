# Async/Concurrency

Rust provides fearless concurrency through its ownership system and both sync and async programming models.

## Threads

### Creating Threads

```rust
use std::thread;
use std::time::Duration;

fn main() {
    // Spawn a thread
    let handle = thread::spawn(|| {
        for i in 1..10 {
            println!("Thread: {}", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 1..5 {
        println!("Main: {}", i);
        thread::sleep(Duration::from_millis(1));
    }

    // Wait for thread to finish
    handle.join().unwrap();
}
```

### Move Closures

```rust
use std::thread;

let v = vec![1, 2, 3];

// Move ownership to thread
let handle = thread::spawn(move || {
    println!("Vector: {:?}", v);
});

handle.join().unwrap();
// v is no longer valid here
```

### Returning Values from Threads

```rust
use std::thread;

let handle = thread::spawn(|| {
    // Compute something
    42
});

let result = handle.join().unwrap();
println!("Result: {}", result);
```

## Message Passing

### Channels

```rust
use std::sync::mpsc;
use std::thread;

// Create channel
let (tx, rx) = mpsc::channel();

thread::spawn(move || {
    let val = String::from("hello");
    tx.send(val).unwrap();
    // val is moved, can't use here
});

// Receive message
let received = rx.recv().unwrap();
println!("Received: {}", received);
```

### Multiple Producers

```rust
use std::sync::mpsc;
use std::thread;

let (tx, rx) = mpsc::channel();

// Clone sender for multiple producers
let tx1 = tx.clone();
thread::spawn(move || {
    tx.send(String::from("from thread 1")).unwrap();
});

thread::spawn(move || {
    tx1.send(String::from("from thread 2")).unwrap();
});

// Receive from both
for received in rx {
    println!("Got: {}", received);
}
```

### Try Recv

```rust
use std::sync::mpsc;
use std::thread;
use std::time::Duration;

let (tx, rx) = mpsc::channel();

thread::spawn(move || {
    thread::sleep(Duration::from_secs(1));
    tx.send("hello").unwrap();
});

// Non-blocking receive
loop {
    match rx.try_recv() {
        Ok(msg) => {
            println!("Received: {}", msg);
            break;
        }
        Err(_) => {
            println!("Not ready yet...");
            thread::sleep(Duration::from_millis(100));
        }
    }
}
```

## Shared State

### Mutex

```rust
use std::sync::Mutex;

let m = Mutex::new(5);

{
    let mut num = m.lock().unwrap();
    *num = 6;
} // Lock released here

println!("m = {:?}", m);
```

### Arc + Mutex Pattern

```rust
use std::sync::{Arc, Mutex};
use std::thread;

let counter = Arc::new(Mutex::new(0));
let mut handles = vec![];

for _ in 0..10 {
    let counter = Arc::clone(&counter);
    let handle = thread::spawn(move || {
        let mut num = counter.lock().unwrap();
        *num += 1;
    });
    handles.push(handle);
}

for handle in handles {
    handle.join().unwrap();
}

println!("Result: {}", *counter.lock().unwrap());
```

### RwLock

```rust
use std::sync::{Arc, RwLock};
use std::thread;

let data = Arc::new(RwLock::new(vec![1, 2, 3]));

// Multiple readers
let data1 = Arc::clone(&data);
let reader1 = thread::spawn(move || {
    let r = data1.read().unwrap();
    println!("Reader 1: {:?}", *r);
});

let data2 = Arc::clone(&data);
let reader2 = thread::spawn(move || {
    let r = data2.read().unwrap();
    println!("Reader 2: {:?}", *r);
});

// One writer
let data3 = Arc::clone(&data);
let writer = thread::spawn(move || {
    let mut w = data3.write().unwrap();
    w.push(4);
});

reader1.join().unwrap();
reader2.join().unwrap();
writer.join().unwrap();

println!("Final: {:?}", *data.read().unwrap());
```

## Async/Await

### Basic Async Function

```rust
async fn say_hello() {
    println!("Hello, async!");
}

// Async function with return value
async fn get_number() -> i32 {
    42
}
```

### Tokio Runtime

```toml
[dependencies]
tokio = { version = "1", features = ["full"] }
```

```rust
use tokio::time::{sleep, Duration};

#[tokio::main]
async fn main() {
    println!("Start");

    say_hello().await;

    let num = get_number().await;
    println!("Number: {}", num);

    sleep(Duration::from_secs(1)).await;
    println!("Done");
}

async fn say_hello() {
    println!("Hello!");
}

async fn get_number() -> i32 {
    42
}
```

### Concurrent Tasks

```rust
use tokio::time::{sleep, Duration};

#[tokio::main]
async fn main() {
    let task1 = tokio::spawn(async {
        sleep(Duration::from_secs(1)).await;
        println!("Task 1 done");
        1
    });

    let task2 = tokio::spawn(async {
        sleep(Duration::from_secs(2)).await;
        println!("Task 2 done");
        2
    });

    // Wait for both
    let result1 = task1.await.unwrap();
    let result2 = task2.await.unwrap();

    println!("Results: {}, {}", result1, result2);
}
```

### Join and Select

```rust
use tokio::time::{sleep, Duration};

#[tokio::main]
async fn main() {
    // Join: wait for all
    let (r1, r2, r3) = tokio::join!(
        async_task(1),
        async_task(2),
        async_task(3),
    );

    println!("Results: {}, {}, {}", r1, r2, r3);

    // Select: wait for first to complete
    tokio::select! {
        r1 = async_task(1) => println!("First: {}", r1),
        r2 = async_task(2) => println!("First: {}", r2),
        r3 = async_task(3) => println!("First: {}", r3),
    }
}

async fn async_task(n: u64) -> u64 {
    sleep(Duration::from_secs(n)).await;
    n
}
```

### Async Streams

```rust
use tokio_stream::{self as stream, StreamExt};

#[tokio::main]
async fn main() {
    let mut stream = stream::iter(vec![1, 2, 3, 4, 5]);

    while let Some(item) = stream.next().await {
        println!("Item: {}", item);
    }
}
```

### Async I/O

```rust
use tokio::fs::File;
use tokio::io::{AsyncReadExt, AsyncWriteExt};

#[tokio::main]
async fn main() -> std::io::Result<()> {
    // Write file
    let mut file = File::create("output.txt").await?;
    file.write_all(b"Hello, async world!").await?;

    // Read file
    let mut file = File::open("output.txt").await?;
    let mut contents = String::new();
    file.read_to_string(&mut contents).await?;

    println!("Contents: {}", contents);
    Ok(())
}
```

## Async HTTP with Reqwest

```toml
[dependencies]
tokio = { version = "1", features = ["full"] }
reqwest = "0.11"
```

```rust
use reqwest;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let response = reqwest::get("https://api.github.com/repos/rust-lang/rust")
        .await?
        .text()
        .await?;

    println!("Response: {}", response);
    Ok(())
}
```

## Rayon - Data Parallelism

```toml
[dependencies]
rayon = "1.7"
```

```rust
use rayon::prelude::*;

fn main() {
    // Parallel iterator
    let sum: i32 = (0..1000)
        .into_par_iter()
        .map(|x| x * 2)
        .sum();

    println!("Sum: {}", sum);

    // Parallel sort
    let mut data = vec![5, 2, 8, 1, 9];
    data.par_sort();
    println!("Sorted: {:?}", data);
}
```

## Crossbeam - Advanced Concurrency

```toml
[dependencies]
crossbeam = "0.8"
```

```rust
use crossbeam::channel;
use std::thread;

fn main() {
    let (tx, rx) = channel::unbounded();

    // Spawn senders
    for i in 0..5 {
        let tx = tx.clone();
        thread::spawn(move || {
            tx.send(i).unwrap();
        });
    }
    drop(tx);

    // Receive all
    while let Ok(msg) = rx.recv() {
        println!("Received: {}", msg);
    }
}
```

## Atomic Types

```rust
use std::sync::atomic::{AtomicUsize, Ordering};
use std::sync::Arc;
use std::thread;

fn main() {
    let counter = Arc::new(AtomicUsize::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        let counter = Arc::clone(&counter);
        let handle = thread::spawn(move || {
            for _ in 0..1000 {
                counter.fetch_add(1, Ordering::SeqCst);
            }
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", counter.load(Ordering::SeqCst));
}
```

## Thread Pools

```rust
use std::sync::mpsc;
use std::sync::{Arc, Mutex};
use std::thread;

struct ThreadPool {
    workers: Vec<Worker>,
    sender: mpsc::Sender<Job>,
}

type Job = Box<dyn FnOnce() + Send + 'static>;

impl ThreadPool {
    fn new(size: usize) -> ThreadPool {
        let (sender, receiver) = mpsc::channel();
        let receiver = Arc::new(Mutex::new(receiver));

        let mut workers = Vec::with_capacity(size);
        for id in 0..size {
            workers.push(Worker::new(id, Arc::clone(&receiver)));
        }

        ThreadPool { workers, sender }
    }

    fn execute<F>(&self, f: F)
    where
        F: FnOnce() + Send + 'static,
    {
        let job = Box::new(f);
        self.sender.send(job).unwrap();
    }
}

struct Worker {
    id: usize,
    thread: thread::JoinHandle<()>,
}

impl Worker {
    fn new(id: usize, receiver: Arc<Mutex<mpsc::Receiver<Job>>>) -> Worker {
        let thread = thread::spawn(move || loop {
            let job = receiver.lock().unwrap().recv().unwrap();
            println!("Worker {} got a job", id);
            job();
        });

        Worker { id, thread }
    }
}
```

## Async Traits (using async-trait)

```toml
[dependencies]
async-trait = "0.1"
```

```rust
use async_trait::async_trait;

#[async_trait]
trait AsyncService {
    async fn fetch_data(&self) -> String;
}

struct MyService;

#[async_trait]
impl AsyncService for MyService {
    async fn fetch_data(&self) -> String {
        String::from("data")
    }
}
```

## Comparing Sync vs Async

```rust
// Synchronous
use std::thread;
use std::time::Duration;

fn sync_work() {
    thread::sleep(Duration::from_secs(1));
    println!("Sync done");
}

// Asynchronous
use tokio::time::sleep;

async fn async_work() {
    sleep(Duration::from_secs(1)).await;
    println!("Async done");
}

// Async is better for I/O-bound work
// Sync threads are better for CPU-bound work
```

> **Good to know:** Use threads for CPU-intensive work and async for I/O-intensive work. Rust's ownership prevents data races at compile time. The `Arc<Mutex<T>>` pattern is common for shared mutable state across threads. Tokio is the most popular async runtime. Use `rayon` for easy data parallelism. Channels are the preferred way to communicate between threads. Remember that async functions don't run until `.await`ed.

---

## References

- [The Rust Book - Concurrency](https://doc.rust-lang.org/book/ch16-00-concurrency.html)
- [Async Book](https://rust-lang.github.io/async-book/)
- [Tokio Documentation](https://tokio.rs/)
- [Rayon Documentation](https://docs.rs/rayon/)
- [Crossbeam Documentation](https://docs.rs/crossbeam/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
