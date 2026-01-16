# Concurrency

## Goroutines

Goroutines are lightweight threads managed by the Go runtime.

```go
// Start a goroutine
go doSomething()

// With anonymous function
go func() {
    fmt.Println("In goroutine")
}()

// With arguments
go func(msg string) {
    fmt.Println(msg)
}("hello")

// Wait for completion
var wg sync.WaitGroup
wg.Add(1)
go func() {
    defer wg.Done()
    doWork()
}()
wg.Wait()
```

## Channels

Channels are typed conduits for communication between goroutines.

```go
// Create channel
ch := make(chan int)      // Unbuffered
ch := make(chan int, 10)  // Buffered (capacity 10)

// Send and receive
ch <- 42       // Send
value := <-ch  // Receive

// Close channel
close(ch)

// Check if closed
value, ok := <-ch
if !ok {
    fmt.Println("Channel closed")
}
```

## Channel Patterns

### Worker Pool

```go
func worker(id int, jobs <-chan int, results chan<- int) {
    for job := range jobs {
        results <- job * 2
    }
}

func main() {
    jobs := make(chan int, 100)
    results := make(chan int, 100)
    
    // Start workers
    for w := 1; w <= 3; w++ {
        go worker(w, jobs, results)
    }
    
    // Send jobs
    for j := 1; j <= 9; j++ {
        jobs <- j
    }
    close(jobs)
    
    // Collect results
    for r := 1; r <= 9; r++ {
        <-results
    }
}
```

### Fan-Out, Fan-In

```go
// Fan-out: multiple goroutines reading from one channel
func fanOut(input <-chan int, workers int) []<-chan int {
    channels := make([]<-chan int, workers)
    for i := 0; i < workers; i++ {
        channels[i] = worker(input)
    }
    return channels
}

// Fan-in: merge multiple channels into one
func fanIn(channels ...<-chan int) <-chan int {
    var wg sync.WaitGroup
    merged := make(chan int)
    
    for _, ch := range channels {
        wg.Add(1)
        go func(c <-chan int) {
            defer wg.Done()
            for v := range c {
                merged <- v
            }
        }(ch)
    }
    
    go func() {
        wg.Wait()
        close(merged)
    }()
    
    return merged
}
```

### Pipeline

```go
func generator(nums ...int) <-chan int {
    out := make(chan int)
    go func() {
        for _, n := range nums {
            out <- n
        }
        close(out)
    }()
    return out
}

func square(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        for n := range in {
            out <- n * n
        }
        close(out)
    }()
    return out
}

func main() {
    // Pipeline: generate -> square -> print
    for n := range square(generator(1, 2, 3, 4)) {
        fmt.Println(n)
    }
}
```

## Select Statement

```go
select {
case msg := <-ch1:
    fmt.Println("Received from ch1:", msg)
case msg := <-ch2:
    fmt.Println("Received from ch2:", msg)
case ch3 <- value:
    fmt.Println("Sent to ch3")
default:
    fmt.Println("No communication ready")
}

// With timeout
select {
case result := <-ch:
    fmt.Println("Got result:", result)
case <-time.After(time.Second):
    fmt.Println("Timeout!")
}

// Non-blocking receive
select {
case msg := <-ch:
    fmt.Println(msg)
default:
    fmt.Println("No message available")
}
```

## Context for Cancellation

```go
import "context"

func worker(ctx context.Context) error {
    for {
        select {
        case <-ctx.Done():
            return ctx.Err()
        default:
            // Do work
        }
    }
}

func main() {
    // With cancel
    ctx, cancel := context.WithCancel(context.Background())
    defer cancel()
    
    go worker(ctx)
    time.Sleep(time.Second)
    cancel()  // Signal cancellation
    
    // With timeout
    ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
    defer cancel()
    
    // With deadline
    deadline := time.Now().Add(10 * time.Second)
    ctx, cancel := context.WithDeadline(context.Background(), deadline)
    defer cancel()
    
    // With value
    ctx := context.WithValue(ctx, "user_id", 123)
    userID := ctx.Value("user_id").(int)
}
```

## sync Package

### Mutex

```go
type SafeCounter struct {
    mu    sync.Mutex
    value int
}

func (c *SafeCounter) Inc() {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.value++
}

func (c *SafeCounter) Value() int {
    c.mu.Lock()
    defer c.mu.Unlock()
    return c.value
}
```

### RWMutex

```go
type Cache struct {
    mu   sync.RWMutex
    data map[string]string
}

func (c *Cache) Get(key string) (string, bool) {
    c.mu.RLock()
    defer c.mu.RUnlock()
    val, ok := c.data[key]
    return val, ok
}

func (c *Cache) Set(key, value string) {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.data[key] = value
}
```

### WaitGroup

```go
var wg sync.WaitGroup

for i := 0; i < 5; i++ {
    wg.Add(1)
    go func(id int) {
        defer wg.Done()
        doWork(id)
    }(i)
}

wg.Wait()  // Block until all goroutines complete
```

### Once

```go
var (
    instance *Singleton
    once     sync.Once
)

func GetInstance() *Singleton {
    once.Do(func() {
        instance = &Singleton{}
        instance.init()
    })
    return instance
}
```

### Cond

```go
var (
    mu    sync.Mutex
    cond  = sync.NewCond(&mu)
    ready bool
)

// Waiter
func waiter() {
    mu.Lock()
    for !ready {
        cond.Wait()
    }
    // Process...
    mu.Unlock()
}

// Signaler
func signaler() {
    mu.Lock()
    ready = true
    cond.Broadcast()  // Wake all waiters
    // Or cond.Signal() for one waiter
    mu.Unlock()
}
```

## Atomic Operations

```go
import "sync/atomic"

var counter int64

// Atomic add
atomic.AddInt64(&counter, 1)

// Atomic load
value := atomic.LoadInt64(&counter)

// Atomic store
atomic.StoreInt64(&counter, 100)

// Compare and swap
swapped := atomic.CompareAndSwapInt64(&counter, 100, 200)

// Atomic value (for any type)
var config atomic.Value
config.Store(Config{Timeout: time.Second})
cfg := config.Load().(Config)
```

## Common Patterns

### Semaphore

```go
sem := make(chan struct{}, maxConcurrency)

for _, item := range items {
    sem <- struct{}{}  // Acquire
    go func(i Item) {
        defer func() { <-sem }()  // Release
        process(i)
    }(item)
}
```

### Rate Limiter

```go
limiter := time.Tick(time.Second / 10)  // 10 per second

for req := range requests {
    <-limiter
    go handle(req)
}

// Or use golang.org/x/time/rate
import "golang.org/x/time/rate"

limiter := rate.NewLimiter(10, 1)  // 10 per second, burst of 1

if limiter.Allow() {
    process()
}

// Wait for permission
ctx := context.Background()
limiter.Wait(ctx)
```

### Graceful Shutdown

```go
func main() {
    srv := &http.Server{Addr: ":8080"}
    
    go func() {
        if err := srv.ListenAndServe(); err != http.ErrServerClosed {
            log.Fatal(err)
        }
    }()
    
    quit := make(chan os.Signal, 1)
    signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
    <-quit
    
    ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
    defer cancel()
    
    if err := srv.Shutdown(ctx); err != nil {
        log.Fatal(err)
    }
}
```

> **Good to know:** "Don't communicate by sharing memory; share memory by communicating." Prefer channels for coordination between goroutines. Use mutexes for protecting shared state. Always pass context for cancellation. Use `go vet` and the race detector (`go run -race`) to catch concurrency bugs.

---

## References

- [Go - Concurrency](https://go.dev/tour/concurrency)
- [Go Blog - Share Memory By Communicating](https://go.dev/blog/codelab-share)
- [Go Blog - Go Concurrency Patterns](https://go.dev/blog/pipelines)
- [Go Blog - Context](https://go.dev/blog/context)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
