# Coding Conventions

## Formatting

Go has a single, enforced formatting style. Use `gofmt` or `goimports`:

```bash
# Format a file
gofmt -w main.go

# Format all files
gofmt -w .

# Use goimports (also manages imports)
goimports -w .

# Check formatting (CI)
gofmt -l .  # List files that need formatting
```

## Naming Conventions

### General Rules

```go
// Exported (public) - uppercase first letter
func ProcessData() {}
type User struct {}
const MaxSize = 100

// Unexported (private) - lowercase first letter
func validateInput() {}
type config struct {}
const defaultTimeout = 30
```

### Packages

```go
// Short, lowercase, singular
package user      // Good
package httputil  // Good

// Avoid
package userManagement  // Bad: too long
package users          // Bad: plural
package util           // Bad: too generic
```

### Variables

```go
// Short names for short scope
for i := 0; i < 10; i++ {}
for _, v := range items {}

// Descriptive for larger scope
userCount := len(users)
maxRetryAttempts := 3

// Common abbreviations
cfg    // config
ctx    // context
err    // error
req    // request
resp   // response
buf    // buffer
```

### Functions and Methods

```go
// Verb or verb phrase
func Get() {}
func Set() {}
func Process() {}
func ValidateUser() {}

// Getters: no "Get" prefix
func (u *User) Name() string { return u.name }  // Not GetName()

// Setters: use "Set" prefix
func (u *User) SetName(n string) { u.name = n }
```

### Interfaces

```go
// Single method: method name + "er"
type Reader interface { Read(p []byte) (n int, err error) }
type Writer interface { Write(p []byte) (n int, err error) }
type Stringer interface { String() string }

// Multiple methods: descriptive noun
type ReadWriter interface {
    Reader
    Writer
}
```

### Constants and Enums

```go
// MixedCaps, not SCREAMING_CASE
const MaxConnections = 100
const defaultTimeout = 30 * time.Second

// Enums with type prefix
type Status int

const (
    StatusPending Status = iota
    StatusActive
    StatusComplete
)
```

### Acronyms

```go
// Acronyms are all caps or all lower
userID   // Not userId
httpClient  // Not HTTPClient (unless exported)
HTTPClient  // Exported
URL, ID, HTTP, API  // Exported acronyms

// In identifiers
xmlHTTPRequest  // Not XmlHttpRequest
serveHTTP      // Not ServeHttp
```

## Code Organization

### File Structure

```go
// Order in a file:
// 1. Package documentation
// 2. Package declaration
// 3. Imports
// 4. Constants
// 5. Variables
// 6. Types
// 7. Functions (constructors first)
// 8. Methods

// Package documentation (before package)
// Package user provides user management functionality.
package user

import (
    "fmt"
    "time"
)

const defaultRole = "user"

var ErrNotFound = errors.New("user not found")

type User struct {
    ID   string
    Name string
}

func NewUser(name string) *User {
    return &User{Name: name}
}

func (u *User) Validate() error {
    // ...
}
```

### Import Grouping

```go
import (
    // Standard library
    "context"
    "fmt"
    "net/http"
    
    // Third-party packages
    "github.com/gin-gonic/gin"
    "go.uber.org/zap"
    
    // Local packages
    "github.com/mycompany/myproject/internal/user"
)
```

## Comments

```go
// Package user provides user management functionality.
package user

// User represents a user in the system.
type User struct {
    // ID is the unique identifier.
    ID string
    
    // Name is the user's display name.
    Name string
}

// NewUser creates a new User with the given name.
// It returns an error if the name is empty.
func NewUser(name string) (*User, error) {
    // ...
}

// Deprecated: Use NewUser instead.
func CreateUser(name string) *User {
    // ...
}
```

## Error Handling

```go
// Check errors immediately
result, err := doSomething()
if err != nil {
    return err
}

// Add context when wrapping
if err != nil {
    return fmt.Errorf("process user %s: %w", userID, err)
}

// Use error variables for sentinel errors
var ErrNotFound = errors.New("not found")

// Use error types for errors with data
type ValidationError struct {
    Field   string
    Message string
}
```

## Common Patterns

### Constructor

```go
// NewTypeName returns a pointer
func NewUser(name string) *User {
    return &User{Name: name}
}

// With validation
func NewUser(name string) (*User, error) {
    if name == "" {
        return nil, errors.New("name is required")
    }
    return &User{Name: name}, nil
}
```

### Options Pattern

```go
type Option func(*Server)

func WithPort(port int) Option {
    return func(s *Server) {
        s.port = port
    }
}

func NewServer(opts ...Option) *Server {
    s := &Server{port: 8080}  // Defaults
    for _, opt := range opts {
        opt(s)
    }
    return s
}
```

### Interface Compliance

```go
// Compile-time interface check
var _ io.Reader = (*MyReader)(nil)
var _ http.Handler = (*MyHandler)(nil)
```

## Tools

```bash
# Formatting
gofmt -w .
goimports -w .

# Linting
go vet ./...
golangci-lint run

# Static analysis
staticcheck ./...

# Find issues
go vet ./...
```

## golangci-lint Configuration

```yaml
# .golangci.yml
run:
  timeout: 5m

linters:
  enable:
    - gofmt
    - goimports
    - govet
    - errcheck
    - staticcheck
    - gosimple
    - ineffassign
    - unused
    - misspell
    - gocritic

linters-settings:
  gofmt:
    simplify: true
```

> **Good to know:** Read [Effective Go](https://go.dev/doc/effective_go) and the [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments). Use `golangci-lint` to catch style issues automatically. The Go community values consistency—follow established patterns even if you prefer alternatives.

---

## References

- [Effective Go](https://go.dev/doc/effective_go)
- [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)
- [Uber Go Style Guide](https://github.com/uber-go/guide/blob/master/style.md)
- [Google Go Style Guide](https://google.github.io/styleguide/go/)
