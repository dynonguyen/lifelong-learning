# Error Handling

## The error Interface

```go
// Built-in error interface
type error interface {
    Error() string
}

// Most functions return error as last value
func doSomething() error {
    return nil  // Success
}

func doSomethingWithResult() (int, error) {
    return 42, nil
}
```

## Creating Errors

```go
import (
    "errors"
    "fmt"
)

// Simple error
err := errors.New("something went wrong")

// Formatted error
err := fmt.Errorf("failed to process user %d: %s", userID, reason)
```

## Error Handling Pattern

```go
// Always check errors immediately
result, err := doSomething()
if err != nil {
    return err  // Or handle it
}
// Use result...

// Multiple operations
func process() error {
    data, err := fetchData()
    if err != nil {
        return fmt.Errorf("fetch data: %w", err)
    }

    parsed, err := parseData(data)
    if err != nil {
        return fmt.Errorf("parse data: %w", err)
    }

    err = saveData(parsed)
    if err != nil {
        return fmt.Errorf("save data: %w", err)
    }

    return nil
}
```

## Custom Error Types

```go
// Simple custom error
type ValidationError struct {
    Field   string
    Message string
}

func (e *ValidationError) Error() string {
    return fmt.Sprintf("%s: %s", e.Field, e.Message)
}

// Usage
func validateUser(u User) error {
    if u.Email == "" {
        return &ValidationError{Field: "email", Message: "is required"}
    }
    return nil
}
```

## Error Wrapping (Go 1.13+)

```go
// Wrap error with context
originalErr := errors.New("connection refused")
wrappedErr := fmt.Errorf("database error: %w", originalErr)

// Unwrap to get original error
unwrapped := errors.Unwrap(wrappedErr)

// Check if error is (or wraps) specific error
if errors.Is(wrappedErr, originalErr) {
    fmt.Println("It's a connection error")
}

// Check if error is (or wraps) specific type
var valErr *ValidationError
if errors.As(err, &valErr) {
    fmt.Printf("Validation failed on field: %s\n", valErr.Field)
}
```

## Sentinel Errors

```go
// Package-level error values
var (
    ErrNotFound     = errors.New("not found")
    ErrUnauthorized = errors.New("unauthorized")
    ErrConflict     = errors.New("conflict")
)

func GetUser(id string) (*User, error) {
    user, found := users[id]
    if !found {
        return nil, ErrNotFound
    }
    return user, nil
}

// Check for sentinel errors
user, err := GetUser("123")
if errors.Is(err, ErrNotFound) {
    http.Error(w, "User not found", http.StatusNotFound)
    return
}
```

## Custom Error with Unwrap

```go
type DBError struct {
    Query string
    Err   error
}

func (e *DBError) Error() string {
    return fmt.Sprintf("query %q failed: %v", e.Query, e.Err)
}

func (e *DBError) Unwrap() error {
    return e.Err
}

// Usage
err := &DBError{
    Query: "SELECT * FROM users",
    Err:   sql.ErrNoRows,
}

if errors.Is(err, sql.ErrNoRows) {
    fmt.Println("No rows found")
}
```

## Multiple Error Wrapping (Go 1.20+)

```go
// Join multiple errors
err1 := errors.New("error 1")
err2 := errors.New("error 2")
combined := errors.Join(err1, err2)

// Check for any wrapped error
errors.Is(combined, err1)  // true
errors.Is(combined, err2)  // true

// Custom type with multiple errors
type MultiError struct {
    errs []error
}

func (m *MultiError) Error() string {
    var msgs []string
    for _, e := range m.errs {
        msgs = append(msgs, e.Error())
    }
    return strings.Join(msgs, "; ")
}

func (m *MultiError) Unwrap() []error {
    return m.errs
}
```

## Panic and Recover

```go
// Panic - for unrecoverable errors
func mustDo() {
    panic("something terrible happened")
}

// Recover - catch panics
func safeCall() (err error) {
    defer func() {
        if r := recover(); r != nil {
            err = fmt.Errorf("recovered from panic: %v", r)
        }
    }()

    mustDo()  // Panics
    return nil
}

// Common pattern: Must functions
func MustParse(s string) *Config {
    cfg, err := Parse(s)
    if err != nil {
        panic(err)
    }
    return cfg
}
```

## Error Handling Best Practices

```go
// 1. Add context when wrapping
if err != nil {
    return fmt.Errorf("processing user %s: %w", userID, err)
}

// 2. Don't wrap twice with same context
// Bad: "process: process: original error"
// Good: "process: original error"

// 3. Handle errors once
if err != nil {
    log.Error("failed to process", "error", err)
    return err  // Don't log AND return - caller may log again
}

// 4. Return early on errors
func process(data []byte) (*Result, error) {
    if len(data) == 0 {
        return nil, errors.New("empty data")
    }

    parsed, err := parse(data)
    if err != nil {
        return nil, err
    }

    // Happy path continues...
    return transform(parsed)
}

// 5. Use typed errors for programmatic handling
type NotFoundError struct {
    Resource string
    ID       string
}

func (e *NotFoundError) Error() string {
    return fmt.Sprintf("%s %s not found", e.Resource, e.ID)
}
```

## HTTP Error Responses

```go
type APIError struct {
    Status  int    `json:"-"`
    Code    string `json:"code"`
    Message string `json:"message"`
}

func (e *APIError) Error() string {
    return e.Message
}

func handleError(w http.ResponseWriter, err error) {
    var apiErr *APIError
    if errors.As(err, &apiErr) {
        w.WriteHeader(apiErr.Status)
        json.NewEncoder(w).Encode(apiErr)
        return
    }

    // Default error
    w.WriteHeader(http.StatusInternalServerError)
    json.NewEncoder(w).Encode(map[string]string{
        "code":    "internal_error",
        "message": "An unexpected error occurred",
    })
}
```

> **Good to know:** Go prefers explicit error handling over exceptions. Always check returned errors, add context when wrapping, and use `errors.Is` and `errors.As` for type-safe error checking. Reserve `panic` for truly unrecoverable situations—regular errors should be returned, not panicked.

---

## References

- [Go - Error handling and Go](https://go.dev/blog/error-handling-and-go)
- [Go Blog - Working with Errors in Go 1.13](https://go.dev/blog/go1.13-errors)
- [Effective Go - Errors](https://go.dev/doc/effective_go#errors)
- [Go Wiki - Errors](https://github.com/golang/go/wiki/Errors)
