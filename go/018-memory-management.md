# Memory Management

## Pointers

```go
// Pointer declaration
var p *int       // Pointer to int, nil by default
x := 42
p = &x           // Get address of x
fmt.Println(*p)  // 42 (dereference)
*p = 100         // Modify value through pointer
fmt.Println(x)   // 100

// Pointer to struct
type User struct {
    Name string
}

u := &User{Name: "Alice"}
u.Name = "Bob"  // Automatic dereference

// new() - allocates zeroed memory, returns pointer
p := new(int)   // *int pointing to 0
```

## When to Use Pointers

```go
// 1. Modify the original value
func increment(n *int) {
    *n++
}

// 2. Avoid copying large structs
func processLargeStruct(data *LargeData) {
    // Works with pointer, no copy
}

// 3. Optional/nullable values
type Config struct {
    Timeout *time.Duration  // nil means "not set"
}

// 4. Shared state
type Counter struct {
    value int
}

func (c *Counter) Increment() {
    c.value++
}
```

## Stack vs Heap

```go
// Go manages stack/heap allocation automatically
// The compiler decides based on escape analysis

// Stack allocated (doesn't escape)
func stackAlloc() int {
    x := 42  // Usually on stack
    return x
}

// Heap allocated (escapes)
func heapAlloc() *int {
    x := 42
    return &x  // x escapes to heap
}

// View escape analysis
// go build -gcflags='-m' main.go
```

## make vs new

```go
// new(T) - allocates zeroed T, returns *T
p := new(int)           // *int
arr := new([3]int)      // *[3]int
s := new(MyStruct)      // *MyStruct

// make(T, args) - creates initialized slice, map, or channel
slice := make([]int, 5, 10)     // len=5, cap=10
m := make(map[string]int)       // Empty map
ch := make(chan int, 100)       // Buffered channel

// Difference
// new returns pointer to zeroed value
// make returns initialized value (not pointer)
```

## Garbage Collection

Go uses a concurrent, tri-color mark-and-sweep garbage collector.

```go
// GC runs automatically
// Manual trigger (rarely needed)
import "runtime"
runtime.GC()

// Read GC stats
var stats runtime.MemStats
runtime.ReadMemStats(&stats)
fmt.Printf("Allocated: %d bytes\n", stats.Alloc)
fmt.Printf("GC cycles: %d\n", stats.NumGC)

// Environment variables
// GOGC=100 (default) - GC when heap doubles
// GOGC=50  - More frequent GC
// GOGC=off - Disable GC
// GOMEMLIMIT=1GiB - Set memory limit (Go 1.19+)
```

## Memory Leaks Prevention

```go
// 1. Close resources
func processFile(path string) error {
    f, err := os.Open(path)
    if err != nil {
        return err
    }
    defer f.Close()  // Always close!
    // ...
}

// 2. Stop goroutines
func worker(ctx context.Context) {
    for {
        select {
        case <-ctx.Done():
            return  // Exit when cancelled
        case work := <-workChan:
            process(work)
        }
    }
}

// 3. Clear slice references
data := make([]*LargeObject, 1000)
// When done:
for i := range data {
    data[i] = nil  // Allow GC
}
data = nil

// 4. Be careful with closures
for i := 0; i < 10; i++ {
    go func(n int) {  // Pass value, don't capture
        fmt.Println(n)
    }(i)
}
```

## sync.Pool for Reuse

```go
import "sync"

var bufferPool = sync.Pool{
    New: func() interface{} {
        return new(bytes.Buffer)
    },
}

func process(data []byte) {
    buf := bufferPool.Get().(*bytes.Buffer)
    defer func() {
        buf.Reset()
        bufferPool.Put(buf)
    }()
    
    buf.Write(data)
    // Use buf...
}
```

## Memory Profiling

```go
import (
    "runtime/pprof"
    "os"
)

// Write heap profile
func writeHeapProfile() {
    f, _ := os.Create("mem.prof")
    defer f.Close()
    pprof.WriteHeapProfile(f)
}

// Analyze with:
// go tool pprof mem.prof

// In-memory profiling
import _ "net/http/pprof"

func main() {
    go http.ListenAndServe(":6060", nil)
    // Access at http://localhost:6060/debug/pprof/
}
```

## Unsafe Package

```go
import "unsafe"

// Get size of type
size := unsafe.Sizeof(int64(0))  // 8

// Pointer arithmetic (use carefully!)
arr := [3]int{10, 20, 30}
p := unsafe.Pointer(&arr[0])
p = unsafe.Add(p, unsafe.Sizeof(arr[0]))
val := *(*int)(p)  // 20

// Convert between pointer types
type A struct{ x int }
type B struct{ y int }

a := A{x: 42}
b := *(*B)(unsafe.Pointer(&a))  // Dangerous!
```

> ⚠️ Avoid `unsafe` unless absolutely necessary. It bypasses Go's type safety.

## Memory Alignment

```go
// Struct fields are aligned for efficiency
type Inefficient struct {
    a bool    // 1 byte + 7 padding
    b int64   // 8 bytes
    c bool    // 1 byte + 7 padding
}  // Total: 24 bytes

type Efficient struct {
    b int64   // 8 bytes
    a bool    // 1 byte
    c bool    // 1 byte + 6 padding
}  // Total: 16 bytes

// Check alignment
unsafe.Alignof(x)  // Required alignment
unsafe.Offsetof(s.field)  // Field offset in struct
```

## Best Practices

```go
// 1. Prefer value types for small structs
type Point struct {
    X, Y int
}
// Pass by value is fine (16 bytes)

// 2. Use pointers for large structs
type LargeStruct struct {
    // Many fields...
}
func process(l *LargeStruct) { }

// 3. Pre-allocate slices when size is known
data := make([]int, 0, expectedSize)

// 4. Reuse buffers
buf := bytes.NewBuffer(make([]byte, 0, 4096))
buf.Reset()  // Reuse instead of creating new

// 5. Use streaming for large data
// Don't load entire file into memory
scanner := bufio.NewScanner(file)
for scanner.Scan() {
    processLine(scanner.Text())
}
```

> **Good to know:** Go's garbage collector is optimized for low latency. Focus on reducing allocations (use sync.Pool, pre-allocate) rather than manual memory management. Use `go build -gcflags='-m'` to see escape analysis and understand what allocates on the heap.

---

## References

- [Go - Pointers](https://go.dev/tour/moretypes/1)
- [Go Blog - Getting to Go: The Journey of Go's Garbage Collector](https://go.dev/blog/ismmkeynote)
- [Go - Memory Model](https://go.dev/ref/mem)
- [Go - pprof](https://pkg.go.dev/runtime/pprof)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
