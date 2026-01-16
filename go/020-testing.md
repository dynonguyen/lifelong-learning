# Testing

## Basic Tests

```go
// math.go
package math

func Add(a, b int) int {
    return a + b
}

// math_test.go
package math

import "testing"

func TestAdd(t *testing.T) {
    result := Add(2, 3)
    if result != 5 {
        t.Errorf("Add(2, 3) = %d; want 5", result)
    }
}
```

## Running Tests

```bash
# Run all tests
go test ./...

# Run tests in current package
go test

# Verbose output
go test -v

# Run specific test
go test -run TestAdd

# Run with regex
go test -run "TestAdd.*"

# With coverage
go test -cover

# Generate coverage report
go test -coverprofile=coverage.out
go tool cover -html=coverage.out

# Race detection
go test -race

# Short mode (skip long tests)
go test -short

# Timeout
go test -timeout 30s
```

## Test Functions

```go
func TestXxx(t *testing.T) { }     // Regular test
func BenchmarkXxx(b *testing.B) { } // Benchmark
func ExampleXxx() { }              // Example (documentation)
func FuzzXxx(f *testing.F) { }     // Fuzz test (Go 1.18+)
```

## Table-Driven Tests

```go
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive", 2, 3, 5},
        {"negative", -1, -2, -3},
        {"zero", 0, 0, 0},
        {"mixed", -1, 5, 4},
    }
    
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Add(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Add(%d, %d) = %d; want %d",
                    tt.a, tt.b, result, tt.expected)
            }
        })
    }
}
```

## Subtests

```go
func TestGroup(t *testing.T) {
    t.Run("SubTest1", func(t *testing.T) {
        // Subtest 1
    })
    
    t.Run("SubTest2", func(t *testing.T) {
        // Subtest 2
    })
}

// Run specific subtest
// go test -run TestGroup/SubTest1
```

## Test Helpers

```go
func TestSomething(t *testing.T) {
    helper := func(t *testing.T, input string) {
        t.Helper()  // Mark as helper (better error reporting)
        if input == "" {
            t.Fatal("empty input")
        }
    }
    
    helper(t, "valid")
}

// Cleanup
func TestWithCleanup(t *testing.T) {
    tempDir := t.TempDir()  // Auto-cleaned up
    
    // Or manual cleanup
    t.Cleanup(func() {
        os.RemoveAll(tempDir)
    })
}
```

## Testing Methods

```go
t.Log("message")           // Log message
t.Logf("format %s", arg)   // Formatted log

t.Error("failed")          // Log error, continue
t.Errorf("got %v", val)    // Formatted error

t.Fatal("critical")        // Log and stop
t.Fatalf("got %v", val)    // Formatted fatal

t.Skip("skipping")         // Skip test
t.Skipf("skipping: %s", r) // Formatted skip

t.Parallel()               // Run in parallel
```

## Setup and Teardown

```go
// Package level setup
func TestMain(m *testing.M) {
    // Setup
    setup()
    
    // Run tests
    code := m.Run()
    
    // Teardown
    teardown()
    
    os.Exit(code)
}

// Per-test setup
func TestWithSetup(t *testing.T) {
    // Setup
    db := setupTestDB(t)
    
    // Cleanup runs after test
    t.Cleanup(func() {
        db.Close()
    })
    
    // Test code...
}
```

## Benchmarks

```go
func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(1, 2)
    }
}

// With setup
func BenchmarkProcess(b *testing.B) {
    data := generateData()
    
    b.ResetTimer()  // Exclude setup time
    
    for i := 0; i < b.N; i++ {
        Process(data)
    }
}

// Parallel benchmark
func BenchmarkParallel(b *testing.B) {
    b.RunParallel(func(pb *testing.PB) {
        for pb.Next() {
            DoWork()
        }
    })
}

// Run benchmarks
// go test -bench=.
// go test -bench=BenchmarkAdd -benchmem
// go test -bench=. -benchtime=5s
```

## Examples

```go
func ExampleAdd() {
    result := Add(2, 3)
    fmt.Println(result)
    // Output: 5
}

func ExampleAdd_negative() {
    result := Add(-1, -2)
    fmt.Println(result)
    // Output: -3
}

// Unordered output
func ExampleShuffle() {
    for _, n := range Shuffle([]int{1, 2, 3}) {
        fmt.Println(n)
    }
    // Unordered output:
    // 1
    // 2
    // 3
}
```

## Fuzz Testing (Go 1.18+)

```go
func FuzzReverse(f *testing.F) {
    // Seed corpus
    f.Add("hello")
    f.Add("世界")
    f.Add("")
    
    f.Fuzz(func(t *testing.T, s string) {
        rev := Reverse(s)
        doubleRev := Reverse(rev)
        if s != doubleRev {
            t.Errorf("double reverse mismatch: %q", s)
        }
    })
}

// Run fuzzing
// go test -fuzz=FuzzReverse
// go test -fuzz=FuzzReverse -fuzztime=30s
```

## Mocking

```go
// Use interfaces for mockability
type UserRepository interface {
    GetByID(id string) (*User, error)
}

// Real implementation
type PostgresUserRepo struct {
    db *sql.DB
}

func (r *PostgresUserRepo) GetByID(id string) (*User, error) {
    // Real database query
}

// Mock implementation
type MockUserRepo struct {
    users map[string]*User
    err   error
}

func (m *MockUserRepo) GetByID(id string) (*User, error) {
    if m.err != nil {
        return nil, m.err
    }
    return m.users[id], nil
}

// Test with mock
func TestGetUser(t *testing.T) {
    mock := &MockUserRepo{
        users: map[string]*User{
            "1": {ID: "1", Name: "Alice"},
        },
    }
    
    service := NewUserService(mock)
    user, err := service.GetUser("1")
    
    if err != nil {
        t.Fatal(err)
    }
    if user.Name != "Alice" {
        t.Errorf("got %s; want Alice", user.Name)
    }
}
```

## HTTP Testing

```go
import (
    "net/http"
    "net/http/httptest"
)

func TestHandler(t *testing.T) {
    req := httptest.NewRequest("GET", "/users/1", nil)
    w := httptest.NewRecorder()
    
    handler(w, req)
    
    resp := w.Result()
    if resp.StatusCode != http.StatusOK {
        t.Errorf("status = %d; want %d", resp.StatusCode, http.StatusOK)
    }
}

// Test server
func TestAPIClient(t *testing.T) {
    server := httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(http.StatusOK)
        w.Write([]byte(`{"id": 1, "name": "Alice"}`))
    }))
    defer server.Close()
    
    client := NewAPIClient(server.URL)
    user, err := client.GetUser(1)
    // Assert...
}
```

## Test Organization

```bash
project/
├── user/
│   ├── user.go
│   ├── user_test.go       # Same package tests
│   └── user_integration_test.go  # Integration tests
├── testdata/              # Test fixtures
│   └── users.json
└── internal/
    └── testutil/          # Shared test utilities
        └── helpers.go
```

> **Good to know:** Use table-driven tests for multiple cases. Keep tests close to the code they test (same package). Use `t.Parallel()` for independent tests to speed up execution. The `testdata` directory is ignored by `go build` but accessible to tests.

---

## References

- [Go - Testing Package](https://pkg.go.dev/testing)
- [Go Blog - Using Subtests and Sub-benchmarks](https://go.dev/blog/subtests)
- [Go Blog - Fuzzing is Beta Ready](https://go.dev/blog/fuzz-beta)
- [Go Wiki - Table Driven Tests](https://github.com/golang/go/wiki/TableDrivenTests)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
