# Async & Concurrency

## asyncio - Asynchronous Programming

### Basic Async/Await

```python
import asyncio

# Define coroutine with async def
async def greet(name):
    print(f"Hello, {name}!")
    await asyncio.sleep(1)  # Non-blocking sleep
    print(f"Goodbye, {name}!")

# Run coroutine
asyncio.run(greet("Alice"))

# Multiple coroutines
async def fetch_data(id):
    print(f"Fetching data {id}...")
    await asyncio.sleep(2)
    return f"Data {id}"

async def main():
    # Sequential execution (slow)
    data1 = await fetch_data(1)
    data2 = await fetch_data(2)
    print(data1, data2)

asyncio.run(main())
```

### Concurrent Execution

```python
import asyncio

async def fetch_data(id):
    print(f"Fetching {id}...")
    await asyncio.sleep(2)
    print(f"Completed {id}")
    return f"Data {id}"

async def main():
    # Concurrent execution with gather
    results = await asyncio.gather(
        fetch_data(1),
        fetch_data(2),
        fetch_data(3)
    )
    print(results)  # ['Data 1', 'Data 2', 'Data 3']

asyncio.run(main())

# Using create_task for more control
async def main():
    # Create tasks
    task1 = asyncio.create_task(fetch_data(1))
    task2 = asyncio.create_task(fetch_data(2))
    task3 = asyncio.create_task(fetch_data(3))

    # Do other work while tasks run
    print("Tasks created, doing other work...")

    # Wait for results
    result1 = await task1
    result2 = await task2
    result3 = await task3

    print(result1, result2, result3)

asyncio.run(main())
```

### Async Context Managers

```python
import asyncio

class AsyncResource:
    async def __aenter__(self):
        print("Acquiring resource")
        await asyncio.sleep(1)
        return self

    async def __aexit__(self, exc_type, exc_val, exc_tb):
        print("Releasing resource")
        await asyncio.sleep(1)
        return False

async def main():
    async with AsyncResource() as resource:
        print("Using resource")
        await asyncio.sleep(1)

asyncio.run(main())

# Async context manager with decorator
from contextlib import asynccontextmanager

@asynccontextmanager
async def async_timer(name):
    import time
    start = time.time()
    try:
        yield
    finally:
        end = time.time()
        print(f"{name} took {end - start:.2f}s")

async def main():
    async with async_timer("Operation"):
        await asyncio.sleep(2)

asyncio.run(main())
```

### Async Iterators

```python
import asyncio

class AsyncRange:
    def __init__(self, count):
        self.count = count
        self.current = 0

    def __aiter__(self):
        return self

    async def __anext__(self):
        if self.current >= self.count:
            raise StopAsyncIteration

        await asyncio.sleep(0.5)
        self.current += 1
        return self.current

async def main():
    async for number in AsyncRange(5):
        print(number)

asyncio.run(main())

# Async generator
async def async_range(count):
    for i in range(count):
        await asyncio.sleep(0.5)
        yield i + 1

async def main():
    async for number in async_range(5):
        print(number)

asyncio.run(main())
```

### Error Handling in Async

```python
import asyncio

async def risky_operation(id):
    await asyncio.sleep(1)
    if id == 2:
        raise ValueError(f"Error in task {id}")
    return f"Success {id}"

async def main():
    # gather with return_exceptions
    results = await asyncio.gather(
        risky_operation(1),
        risky_operation(2),
        risky_operation(3),
        return_exceptions=True
    )

    for i, result in enumerate(results, 1):
        if isinstance(result, Exception):
            print(f"Task {i} failed: {result}")
        else:
            print(f"Task {i}: {result}")

asyncio.run(main())

# Try-except with tasks
async def main():
    task1 = asyncio.create_task(risky_operation(1))
    task2 = asyncio.create_task(risky_operation(2))

    try:
        result1 = await task1
        result2 = await task2
    except ValueError as e:
        print(f"Error: {e}")

asyncio.run(main())
```

### Timeouts

```python
import asyncio

async def slow_operation():
    await asyncio.sleep(5)
    return "Completed"

async def main():
    try:
        # Timeout after 2 seconds
        result = await asyncio.wait_for(slow_operation(), timeout=2.0)
        print(result)
    except asyncio.TimeoutError:
        print("Operation timed out")

asyncio.run(main())

# Timeout with context manager (Python 3.11+)
async def main():
    try:
        async with asyncio.timeout(2.0):
            result = await slow_operation()
            print(result)
    except asyncio.TimeoutError:
        print("Operation timed out")

asyncio.run(main())
```

### Async HTTP Requests

```python
import asyncio
import aiohttp

async def fetch(session, url):
    async with session.get(url) as response:
        return await response.text()

async def main():
    async with aiohttp.ClientSession() as session:
        # Sequential
        html1 = await fetch(session, "https://example.com")
        html2 = await fetch(session, "https://example.org")

        # Concurrent
        results = await asyncio.gather(
            fetch(session, "https://example.com"),
            fetch(session, "https://example.org"),
            fetch(session, "https://example.net")
        )

        print(f"Fetched {len(results)} pages")

asyncio.run(main())
```

## Threading

### Basic Threading

```python
import threading
import time

def worker(name, duration):
    print(f"{name} starting")
    time.sleep(duration)
    print(f"{name} finished")

# Create threads
thread1 = threading.Thread(target=worker, args=("Worker 1", 2))
thread2 = threading.Thread(target=worker, args=("Worker 2", 3))

# Start threads
thread1.start()
thread2.start()

# Wait for threads to complete
thread1.join()
thread2.join()

print("All threads completed")

# Thread with class
class WorkerThread(threading.Thread):
    def __init__(self, name, duration):
        super().__init__()
        self.name = name
        self.duration = duration

    def run(self):
        print(f"{self.name} starting")
        time.sleep(self.duration)
        print(f"{self.name} finished")

worker = WorkerThread("Worker", 2)
worker.start()
worker.join()
```

### Thread Synchronization

```python
import threading
import time

# Lock (Mutex)
lock = threading.Lock()
counter = 0

def increment():
    global counter
    for _ in range(100000):
        with lock:  # Acquire lock
            counter += 1
        # Lock released

threads = []
for i in range(10):
    thread = threading.Thread(target=increment)
    threads.append(thread)
    thread.start()

for thread in threads:
    thread.join()

print(f"Counter: {counter}")  # Should be 1000000

# RLock (Reentrant Lock)
rlock = threading.RLock()

def recursive_function(n):
    with rlock:
        if n > 0:
            recursive_function(n - 1)

# Semaphore (limit concurrent access)
semaphore = threading.Semaphore(3)  # Max 3 threads

def limited_access(id):
    with semaphore:
        print(f"Thread {id} acquired semaphore")
        time.sleep(2)
        print(f"Thread {id} releasing semaphore")

threads = [threading.Thread(target=limited_access, args=(i,))
           for i in range(10)]

for thread in threads:
    thread.start()

for thread in threads:
    thread.join()
```

### Thread Communication

```python
import threading
import queue
import time

# Queue for thread-safe communication
q = queue.Queue()

def producer():
    for i in range(5):
        print(f"Producing {i}")
        q.put(i)
        time.sleep(1)
    q.put(None)  # Sentinel value

def consumer():
    while True:
        item = q.get()
        if item is None:
            break
        print(f"Consuming {item}")
        time.sleep(0.5)
        q.task_done()

prod_thread = threading.Thread(target=producer)
cons_thread = threading.Thread(target=consumer)

prod_thread.start()
cons_thread.start()

prod_thread.join()
cons_thread.join()

# Event for signaling
event = threading.Event()

def waiter():
    print("Waiting for event...")
    event.wait()  # Block until set
    print("Event received!")

def setter():
    time.sleep(2)
    print("Setting event")
    event.set()

threading.Thread(target=waiter).start()
threading.Thread(target=setter).start()

# Condition for complex synchronization
condition = threading.Condition()
items = []

def consumer():
    with condition:
        while not items:
            print("Consumer waiting...")
            condition.wait()
        item = items.pop(0)
        print(f"Consumed {item}")

def producer():
    time.sleep(1)
    with condition:
        items.append("item")
        print("Produced item")
        condition.notify()  # Wake up consumer

threading.Thread(target=consumer).start()
threading.Thread(target=producer).start()
```

### Thread Pool

```python
from concurrent.futures import ThreadPoolExecutor
import time

def task(n):
    print(f"Task {n} starting")
    time.sleep(1)
    return n * 2

# Using ThreadPoolExecutor
with ThreadPoolExecutor(max_workers=3) as executor:
    # Submit tasks
    futures = [executor.submit(task, i) for i in range(10)]

    # Get results as completed
    for future in futures:
        result = future.result()
        print(f"Result: {result}")

# Using map
with ThreadPoolExecutor(max_workers=3) as executor:
    results = executor.map(task, range(10))
    for result in results:
        print(f"Result: {result}")
```

## Multiprocessing

### Basic Multiprocessing

```python
import multiprocessing
import time

def worker(name, duration):
    print(f"{name} starting")
    time.sleep(duration)
    print(f"{name} finished")
    return f"{name} result"

# Create processes
if __name__ == "__main__":
    process1 = multiprocessing.Process(
        target=worker,
        args=("Process 1", 2)
    )
    process2 = multiprocessing.Process(
        target=worker,
        args=("Process 2", 3)
    )

    # Start processes
    process1.start()
    process2.start()

    # Wait for completion
    process1.join()
    process2.join()

    print("All processes completed")
```

### Process Pool

```python
from multiprocessing import Pool
import time

def cpu_intensive_task(n):
    """Simulate CPU-intensive work."""
    total = 0
    for i in range(n * 1000000):
        total += i
    return total

if __name__ == "__main__":
    # Process pool
    with Pool(processes=4) as pool:
        # Map function to inputs
        results = pool.map(cpu_intensive_task, range(10))
        print(f"Results: {results}")

    # Apply async
    with Pool(processes=4) as pool:
        result = pool.apply_async(cpu_intensive_task, (5,))
        print(f"Result: {result.get()}")

    # Map with progress
    with Pool(processes=4) as pool:
        results = []
        for result in pool.imap(cpu_intensive_task, range(10)):
            results.append(result)
            print(f"Completed: {len(results)}/10")
```

### Inter-Process Communication

```python
from multiprocessing import Process, Queue, Pipe
import time

# Queue
def producer(q):
    for i in range(5):
        print(f"Producing {i}")
        q.put(i)
        time.sleep(1)

def consumer(q):
    while True:
        item = q.get()
        if item is None:
            break
        print(f"Consuming {item}")

if __name__ == "__main__":
    q = Queue()

    prod = Process(target=producer, args=(q,))
    cons = Process(target=consumer, args=(q,))

    prod.start()
    cons.start()

    prod.join()
    q.put(None)  # Stop consumer
    cons.join()

# Pipe
def sender(conn):
    conn.send("Hello from sender")
    conn.close()

def receiver(conn):
    message = conn.recv()
    print(f"Received: {message}")
    conn.close()

if __name__ == "__main__":
    parent_conn, child_conn = Pipe()

    p1 = Process(target=sender, args=(child_conn,))
    p2 = Process(target=receiver, args=(parent_conn,))

    p1.start()
    p2.start()

    p1.join()
    p2.join()
```

### Shared Memory

```python
from multiprocessing import Process, Value, Array, Manager
import time

# Shared value and array
def worker(shared_value, shared_array):
    shared_value.value += 1
    for i in range(len(shared_array)):
        shared_array[i] *= 2

if __name__ == "__main__":
    # Shared value (with lock)
    shared_value = Value('i', 0)  # 'i' = integer
    shared_array = Array('i', [1, 2, 3, 4, 5])

    processes = [
        Process(target=worker, args=(shared_value, shared_array))
        for _ in range(5)
    ]

    for p in processes:
        p.start()

    for p in processes:
        p.join()

    print(f"Value: {shared_value.value}")
    print(f"Array: {list(shared_array)}")

# Manager for complex types
def worker(shared_dict, shared_list):
    shared_dict['key'] = 'value'
    shared_list.append(1)

if __name__ == "__main__":
    with Manager() as manager:
        shared_dict = manager.dict()
        shared_list = manager.list()

        p = Process(target=worker, args=(shared_dict, shared_list))
        p.start()
        p.join()

        print(f"Dict: {dict(shared_dict)}")
        print(f"List: {list(shared_list)}")
```

## Choosing Concurrency Model

```python
# CPU-bound tasks: Use multiprocessing
def cpu_bound():
    """Heavy computation."""
    return sum(i * i for i in range(10_000_000))

# I/O-bound tasks: Use asyncio or threading
async def io_bound():
    """Network requests, file I/O, etc."""
    async with aiohttp.ClientSession() as session:
        async with session.get("https://api.example.com") as resp:
            return await resp.json()

# Comparison table:
"""
| Task Type  | Best Choice    | Reason                        |
|------------|----------------|-------------------------------|
| CPU-bound  | multiprocessing| Bypasses GIL, true parallelism|
| I/O-bound  | asyncio        | Efficient, single-threaded    |
| I/O-bound  | threading      | Simpler than asyncio          |
| Mixed      | both           | Combine as needed             |
"""
```

## Global Interpreter Lock (GIL)

```python
# Python's GIL allows only one thread to execute Python bytecode at a time
# This affects multi-threading but not multiprocessing

import threading
import time

def cpu_task():
    """CPU-intensive task."""
    count = 0
    for _ in range(50_000_000):
        count += 1
    return count

# Threading (limited by GIL)
start = time.time()
threads = [threading.Thread(target=cpu_task) for _ in range(4)]
for t in threads:
    t.start()
for t in threads:
    t.join()
print(f"Threading: {time.time() - start:.2f}s")

# Multiprocessing (not limited by GIL)
from multiprocessing import Process

start = time.time()
processes = [Process(target=cpu_task) for _ in range(4)]
for p in processes:
    p.start()
for p in processes:
    p.join()
print(f"Multiprocessing: {time.time() - start:.2f}s")

# Result: Multiprocessing is faster for CPU-bound tasks
```

## Best Practices

```python
# 1. Use async for I/O-bound operations
async def fetch_urls(urls):
    async with aiohttp.ClientSession() as session:
        tasks = [fetch(session, url) for url in urls]
        return await asyncio.gather(*tasks)

# 2. Use multiprocessing for CPU-bound operations
from multiprocessing import Pool

def process_data_parallel(data_chunks):
    with Pool() as pool:
        return pool.map(process_chunk, data_chunks)

# 3. Avoid shared state in threads
# Use queue for communication
def thread_safe_processing():
    q = queue.Queue()
    # Producer-consumer pattern

# 4. Always join threads/processes
def cleanup():
    for thread in threads:
        thread.join()  # Wait for completion

# 5. Use context managers
with ThreadPoolExecutor() as executor:
    # Work with executor
    pass
# Automatically cleaned up

# 6. Handle exceptions properly
async def safe_task():
    try:
        await risky_operation()
    except Exception as e:
        logger.error(f"Error: {e}")
        raise

# 7. Set timeouts
async def with_timeout():
    try:
        await asyncio.wait_for(operation(), timeout=5.0)
    except asyncio.TimeoutError:
        print("Timeout")
```

> **Good to know:** `asyncio` is ideal for I/O-bound operations with many concurrent tasks. `threading` is simpler than async but limited by the GIL for CPU tasks. `multiprocessing` bypasses the GIL and uses multiple CPU cores. The GIL prevents true parallelism in multi-threaded CPU-bound tasks. Use `ProcessPoolExecutor` for CPU-bound work and `ThreadPoolExecutor` for I/O-bound work. Always use locks/semaphores to protect shared state in threads. Processes have separate memory spaces and communicate via queues or pipes. `asyncio.gather()` runs coroutines concurrently. `await` suspends execution without blocking. Async context managers use `async with`. Async iterators use `async for`. Use `queue.Queue` for thread-safe communication. Set timeouts to prevent hanging operations.

---

## References

- [Python - asyncio](https://docs.python.org/3/library/asyncio.html)
- [Python - threading](https://docs.python.org/3/library/threading.html)
- [Python - multiprocessing](https://docs.python.org/3/library/multiprocessing.html)
- [Python - concurrent.futures](https://docs.python.org/3/library/concurrent.futures.html)
- [Real Python - Async IO](https://realpython.com/async-io-python/)
- [Real Python - Threading](https://realpython.com/intro-to-python-threading/)
- [PEP 3156 - Asynchronous IO](https://www.python.org/dev/peps/pep-3156/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
