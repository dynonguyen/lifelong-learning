# Tips & Tricks

## Idioms and Patterns

### Accept Interfaces, Return Structs

```go
// Good: Accept interface
func ProcessData(r io.Reader) error {
    data, err := io.ReadAll(r)
    // ...
}

// Good: Return concrete type
func NewServer() *Server {
    return &Server{}
}

// Can pass any Reader
ProcessData(file)
ProcessData(bytes.NewReader(data))
ProcessData(resp.Body)
```

### Functional Options

```go
type Server struct {
    host    string
    port    int
    timeout time.Duration
}

type Option func(*Server)

func WithPort(port int) Option {
    return func(s *Server) { s.port = port }
}

func WithTimeout(d time.Duration) Option {
    return func(s *Server) { s.timeout = d }
}

func NewServer(opts ...Option) *Server {
    s := &Server{host: "localhost", port: 8080, timeout: 30 * time.Second}
    for _, opt := range opts {
        opt(s)
    }
    return s
}

// Usage
s := NewServer(WithPort(3000), WithTimeout(time.Minute))
```

### Compile-Time Interface Check

```go
var _ io.Reader = (*MyReader)(nil)
var _ http.Handler = (*MyHandler)(nil)
```

### Empty Struct for Signals

```go
// Use struct{} for signals (zero memory)
done := make(chan struct{})
close(done)  // Signal completion

// Set implementation
set := make(map[string]struct{})
set["item"] = struct{}{}
```

## Performance Tips

### Pre-allocate Slices

```go
// Bad: grows repeatedly
var items []Item
for _, v := range data {
    items = append(items, process(v))
}

// Good: pre-allocate
items := make([]Item, 0, len(data))
for _, v := range data {
    items = append(items, process(v))
}
```

### Use strings.Builder

```go
// Bad: creates many intermediate strings
var s string
for _, word := range words {
    s += word + " "
}

// Good: efficient concatenation
var b strings.Builder
for _, word := range words {
    b.WriteString(word)
    b.WriteString(" ")
}
s := b.String()
```

### Use sync.Pool for Reuse

```go
var bufPool = sync.Pool{
    New: func() interface{} {
        return new(bytes.Buffer)
    },
}

func process(data []byte) {
    buf := bufPool.Get().(*bytes.Buffer)
    defer func() {
        buf.Reset()
        bufPool.Put(buf)
    }()
    buf.Write(data)
    // ...
}
```

### Avoid Unnecessary Allocations

```go
// Bad: allocates new slice
func getFirst(items []int) []int {
    return items[:1]  // Safe, shares memory
}

// Be aware: modifications affect original
s1 := []int{1, 2, 3}
s2 := s1[:2]
s2[0] = 99  // s1[0] is also 99

// Safe copy if needed
s2 := make([]int, 2)
copy(s2, s1[:2])
```

## Common Gotchas

### Loop Variable Capture

```go
// Bug: all goroutines see same value
for i := 0; i < 5; i++ {
    go func() {
        fmt.Println(i)  // May print 5,5,5,5,5
    }()
}

// Fix: pass as argument
for i := 0; i < 5; i++ {
    go func(n int) {
        fmt.Println(n)  // Prints 0,1,2,3,4
    }(i)
}

// Go 1.22+: automatically fixed
for i := 0; i < 5; i++ {
    go func() {
        fmt.Println(i)  // Works correctly
    }()
}
```

### Nil Slice vs Empty Slice

```go
var nilSlice []int     // nil, len=0, cap=0
emptySlice := []int{}  // not nil, len=0, cap=0

nilSlice == nil        // true
emptySlice == nil      // false

// JSON encoding differs
json.Marshal(nilSlice)   // null
json.Marshal(emptySlice) // []
```

### Nil Interface vs Nil Value

```go
var w io.Writer         // nil interface
var buf *bytes.Buffer   // nil pointer
w = buf                 // NOT nil! (type is set)

w == nil                // false!

// Check properly
func isNil(w io.Writer) bool {
    return w == nil || reflect.ValueOf(w).IsNil()
}
```

### Map Concurrent Access

```go
// Maps are NOT thread-safe
// This can panic:
m := make(map[string]int)
go func() { m["a"] = 1 }()
go func() { _ = m["a"] }()

// Use sync.RWMutex or sync.Map
var mu sync.RWMutex
var m = make(map[string]int)

// Write
mu.Lock()
m["key"] = value
mu.Unlock()

// Read
mu.RLock()
v := m["key"]
mu.RUnlock()
```

### Defer in Loops

```go
// Bug: files not closed until function returns
for _, path := range paths {
    f, _ := os.Open(path)
    defer f.Close()  // Deferred until function ends!
    process(f)
}

// Fix: use anonymous function
for _, path := range paths {
    func() {
        f, _ := os.Open(path)
        defer f.Close()
        process(f)
    }()
}
```

## Debugging Tips

### Print Stack Trace

```go
import "runtime/debug"

func handler() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Printf("Panic: %v\n%s", r, debug.Stack())
        }
    }()
    // ...
}
```

### Race Detector

```bash
go run -race main.go
go test -race ./...
```

### CPU and Memory Profiling

```go
import _ "net/http/pprof"

func main() {
    go func() {
        http.ListenAndServe(":6060", nil)
    }()
    // ...
}
// Visit http://localhost:6060/debug/pprof/
```

### Build Information

```bash
# View escape analysis
go build -gcflags='-m' main.go

# View assembly
go build -gcflags='-S' main.go

# View dependencies
go mod graph
go mod why package
```

## Useful Snippets

### Retry with Backoff

```go
func retry(attempts int, sleep time.Duration, fn func() error) error {
    for i := 0; i < attempts; i++ {
        if err := fn(); err == nil {
            return nil
        }
        time.Sleep(sleep)
        sleep *= 2  // Exponential backoff
    }
    return fmt.Errorf("after %d attempts", attempts)
}
```

### Graceful Shutdown

```go
quit := make(chan os.Signal, 1)
signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)

go func() {
    srv.ListenAndServe()
}()

<-quit
ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
defer cancel()
srv.Shutdown(ctx)
```

### Generic Must Function

```go
func Must[T any](v T, err error) T {
    if err != nil {
        panic(err)
    }
    return v
}

// Usage
data := Must(os.ReadFile("config.json"))
```

### Timeout Context

```go
func withTimeout(fn func(ctx context.Context) error) error {
    ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
    defer cancel()
    
    done := make(chan error, 1)
    go func() {
        done <- fn(ctx)
    }()
    
    select {
    case err := <-done:
        return err
    case <-ctx.Done():
        return ctx.Err()
    }
}
```

> **Good to know:** Use `go vet` and `golangci-lint` to catch common issues. Enable the race detector in tests. Read the Go Proverbs for philosophical guidance on Go programming.

---

## References

- [Go Proverbs](https://go-proverbs.github.io/)
- [Effective Go](https://go.dev/doc/effective_go)
- [Go Wiki - Common Mistakes](https://github.com/golang/go/wiki/CommonMistakes)
- [Uber Go Style Guide](https://github.com/uber-go/guide/blob/master/style.md)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
