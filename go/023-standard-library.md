# Standard Library

Go's standard library is comprehensive and well-designed. Here are the most commonly used packages.

## fmt - Formatted I/O

```go
import "fmt"

// Printing
fmt.Print("hello")           // No newline
fmt.Println("hello")         // With newline
fmt.Printf("%s: %d\n", k, v) // Formatted

// String formatting
s := fmt.Sprintf("value: %d", 42)

// Scanning
var name string
fmt.Scan(&name)              // From stdin
fmt.Sscan("hello", &name)    // From string
```

## strings - String Manipulation

```go
import "strings"

strings.Contains("hello", "ell")     // true
strings.HasPrefix("hello", "he")     // true
strings.HasSuffix("hello", "lo")     // true
strings.Index("hello", "l")          // 2
strings.Count("hello", "l")          // 2

strings.ToUpper("hello")             // "HELLO"
strings.ToLower("HELLO")             // "hello"
strings.TrimSpace("  hi  ")          // "hi"
strings.Trim("!!hi!!", "!")          // "hi"

strings.Split("a,b,c", ",")          // ["a", "b", "c"]
strings.Join([]string{"a","b"}, ",") // "a,b"

strings.Replace("hello", "l", "L", 1)  // "heLlo"
strings.ReplaceAll("hello", "l", "L")  // "heLLo"

// Builder for efficient concatenation
var b strings.Builder
b.WriteString("hello")
b.WriteString(" world")
s := b.String()
```

## strconv - String Conversions

```go
import "strconv"

// String to int
i, err := strconv.Atoi("42")

// Int to string
s := strconv.Itoa(42)

// Parse with control
i64, _ := strconv.ParseInt("42", 10, 64)
f64, _ := strconv.ParseFloat("3.14", 64)
b, _ := strconv.ParseBool("true")

// Format with control
s := strconv.FormatInt(42, 16)     // "2a"
s := strconv.FormatFloat(3.14, 'f', 2, 64)
```

## time - Time and Duration

```go
import "time"

// Current time
now := time.Now()

// Create specific time
t := time.Date(2024, time.January, 15, 10, 30, 0, 0, time.UTC)

// Parsing
t, _ := time.Parse("2006-01-02", "2024-01-15")
t, _ := time.Parse(time.RFC3339, "2024-01-15T10:30:00Z")

// Formatting (reference time: Mon Jan 2 15:04:05 MST 2006)
s := now.Format("2006-01-02 15:04:05")
s := now.Format(time.RFC3339)

// Duration
d := 5 * time.Second
d := time.Hour + 30*time.Minute

// Operations
future := now.Add(24 * time.Hour)
diff := future.Sub(now)
now.Before(future)
now.After(future)

// Sleep and timers
time.Sleep(time.Second)
timer := time.NewTimer(time.Second)
<-timer.C

// Ticker
ticker := time.NewTicker(time.Second)
for range ticker.C {
    fmt.Println("tick")
}
```

## io - Basic I/O

```go
import "io"

// Copy
io.Copy(dst, src)

// Read all
data, _ := io.ReadAll(reader)

// Limit reader
limited := io.LimitReader(r, 1024)

// Multi-reader/writer
r := io.MultiReader(r1, r2)
w := io.MultiWriter(w1, w2)

// Pipe
r, w := io.Pipe()
```

## os - Operating System

```go
import "os"

// Environment
os.Getenv("HOME")
os.Setenv("KEY", "value")
os.Environ()  // All env vars

// Files
f, _ := os.Open("file.txt")
f, _ := os.Create("file.txt")
f, _ := os.OpenFile("file.txt", os.O_RDWR|os.O_CREATE, 0644)
defer f.Close()

data, _ := os.ReadFile("file.txt")
os.WriteFile("file.txt", data, 0644)

// Directory
os.Mkdir("dir", 0755)
os.MkdirAll("path/to/dir", 0755)
os.Remove("file.txt")
os.RemoveAll("dir")

entries, _ := os.ReadDir(".")

// Args and exit
os.Args       // Command-line args
os.Exit(1)    // Exit with code

// Working directory
wd, _ := os.Getwd()
os.Chdir("/path")
```

## path/filepath - File Paths

```go
import "path/filepath"

filepath.Join("dir", "file.txt")     // "dir/file.txt"
filepath.Dir("/path/to/file.txt")    // "/path/to"
filepath.Base("/path/to/file.txt")   // "file.txt"
filepath.Ext("file.txt")             // ".txt"
filepath.Abs("file.txt")             // Absolute path
filepath.Clean("./dir/../file")      // Cleaned path

// Walk directory
filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
    fmt.Println(path)
    return nil
})

// Glob
matches, _ := filepath.Glob("*.go")
```

## net/http - HTTP Client and Server

```go
import "net/http"

// Simple GET
resp, _ := http.Get("https://api.example.com")
defer resp.Body.Close()
body, _ := io.ReadAll(resp.Body)

// POST with body
resp, _ := http.Post(url, "application/json", bytes.NewBuffer(jsonData))

// Custom client
client := &http.Client{
    Timeout: 10 * time.Second,
}
req, _ := http.NewRequestWithContext(ctx, "GET", url, nil)
req.Header.Set("Authorization", "Bearer token")
resp, _ := client.Do(req)

// Simple server
http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello!")
})
http.ListenAndServe(":8080", nil)

// Custom mux
mux := http.NewServeMux()
mux.HandleFunc("/users", usersHandler)
http.ListenAndServe(":8080", mux)
```

## encoding/json - JSON

```go
import "encoding/json"

type User struct {
    ID    int    `json:"id"`
    Name  string `json:"name"`
    Email string `json:"email,omitempty"`
}

// Marshal
data, _ := json.Marshal(user)
data, _ := json.MarshalIndent(user, "", "  ")

// Unmarshal
var user User
json.Unmarshal(data, &user)

// Streaming
encoder := json.NewEncoder(w)
encoder.Encode(user)

decoder := json.NewDecoder(r)
decoder.Decode(&user)

// Dynamic JSON
var result map[string]interface{}
json.Unmarshal(data, &result)
```

## database/sql - Database

```go
import (
    "database/sql"
    _ "github.com/lib/pq"  // PostgreSQL driver
)

db, _ := sql.Open("postgres", connStr)
defer db.Close()

// Query single row
var name string
row := db.QueryRow("SELECT name FROM users WHERE id = $1", id)
row.Scan(&name)

// Query multiple rows
rows, _ := db.Query("SELECT id, name FROM users")
defer rows.Close()
for rows.Next() {
    var id int
    var name string
    rows.Scan(&id, &name)
}

// Execute
result, _ := db.Exec("INSERT INTO users(name) VALUES($1)", name)
id, _ := result.LastInsertId()

// Transaction
tx, _ := db.Begin()
tx.Exec("INSERT ...")
tx.Commit()  // or tx.Rollback()
```

## context - Request-Scoped Values

```go
import "context"

// With cancel
ctx, cancel := context.WithCancel(context.Background())
defer cancel()

// With timeout
ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()

// With value
ctx := context.WithValue(ctx, "user_id", 123)
userID := ctx.Value("user_id").(int)

// Check cancellation
select {
case <-ctx.Done():
    return ctx.Err()
default:
    // Continue
}
```

## sync - Synchronization

```go
import "sync"

// Mutex
var mu sync.Mutex
mu.Lock()
defer mu.Unlock()

// RWMutex
var rw sync.RWMutex
rw.RLock()
defer rw.RUnlock()

// WaitGroup
var wg sync.WaitGroup
wg.Add(1)
go func() {
    defer wg.Done()
    // work
}()
wg.Wait()

// Once
var once sync.Once
once.Do(func() {
    // Initialize
})

// Pool
pool := sync.Pool{
    New: func() interface{} {
        return new(bytes.Buffer)
    },
}
buf := pool.Get().(*bytes.Buffer)
pool.Put(buf)
```

## regexp - Regular Expressions

```go
import "regexp"

// Compile and match
re := regexp.MustCompile(`\d+`)
re.MatchString("abc123")        // true
re.FindString("abc123")         // "123"
re.FindAllString("a1b2c3", -1)  // ["1", "2", "3"]

// Replace
re.ReplaceAllString("a1b2", "X")  // "aXbX"

// Submatch
re := regexp.MustCompile(`(\w+)@(\w+)\.(\w+)`)
matches := re.FindStringSubmatch("user@example.com")
// ["user@example.com", "user", "example", "com"]
```

## crypto - Cryptography

```go
import (
    "crypto/sha256"
    "crypto/rand"
    "encoding/hex"
)

// Hash
hash := sha256.Sum256([]byte("hello"))
hexHash := hex.EncodeToString(hash[:])

// Random bytes
bytes := make([]byte, 32)
rand.Read(bytes)
```

> **Good to know:** The Go standard library is production-ready and well-tested. Before reaching for third-party packages, check if the stdlib can do what you need. The `net/http` package alone is powerful enough for most web applications.

---

## References

- [Go Standard Library](https://pkg.go.dev/std)
- [Go - Effective Go](https://go.dev/doc/effective_go)
- [Go by Example](https://gobyexample.com/)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
