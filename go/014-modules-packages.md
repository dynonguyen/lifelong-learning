# Modules/Packages

## Modules

A module is a collection of packages with a `go.mod` file at its root.

### Initialize a Module

```bash
# Create a new module
go mod init github.com/username/myproject

# This creates go.mod:
# module github.com/username/myproject
# 
# go 1.22
```

### go.mod File

```go
module github.com/username/myproject

go 1.22

require (
    github.com/gin-gonic/gin v1.9.1
    github.com/jmoiron/sqlx v1.3.5
)

require (
    // indirect dependencies
    github.com/go-playground/validator/v10 v10.14.0 // indirect
)

replace github.com/old/module => github.com/new/module v1.0.0

exclude github.com/broken/module v1.2.3
```

### Common go mod Commands

```bash
# Add dependencies
go get github.com/gin-gonic/gin

# Add specific version
go get github.com/gin-gonic/gin@v1.9.1

# Update all dependencies
go get -u ./...

# Update specific dependency
go get -u github.com/gin-gonic/gin

# Remove unused dependencies
go mod tidy

# Download dependencies to cache
go mod download

# Create vendor directory
go mod vendor

# Verify dependencies
go mod verify

# Explain why a dependency exists
go mod why github.com/some/package
```

## Packages

A package is a directory containing Go source files with the same package declaration.

### Package Declaration

```go
// Every Go file starts with package declaration
package main      // Executable package

package user      // Library package

package user_test // Test package (external)
```

### Package Naming Conventions

```bash
myproject/
├── go.mod
├── main.go           # package main
├── user/
│   ├── user.go       # package user
│   └── user_test.go  # package user (or user_test)
├── internal/
│   └── auth/
│       └── auth.go   # package auth (only accessible within module)
└── pkg/
    └── utils/
        └── utils.go  # package utils (public utilities)
```

### Import Paths

```go
// Standard library
import "fmt"
import "net/http"

// Third-party
import "github.com/gin-gonic/gin"

// Local packages
import "github.com/username/myproject/user"

// Grouped imports
import (
    "fmt"
    "net/http"
    
    "github.com/gin-gonic/gin"
    
    "github.com/username/myproject/user"
)
```

### Import Aliases

```go
// Alias to avoid conflicts
import (
    stdjson "encoding/json"
    fastjson "github.com/valyala/fastjson"
)

// Dot import (brings exports into current namespace)
import . "fmt"
Println("Hello")  // No fmt. prefix needed

// Blank import (side effects only)
import _ "github.com/lib/pq"
```

## Visibility (Exported vs Unexported)

```go
package user

// Exported (accessible from other packages) - uppercase
type User struct {      // Exported type
    Name  string        // Exported field
    Email string        // Exported field
    age   int           // unexported field
}

func NewUser() *User {  // Exported function
    return &User{}
}

// unexported (only accessible within package) - lowercase
func validateEmail(e string) bool {
    return true
}

type config struct {    // unexported type
    host string
}
```

## Internal Packages

Packages under `internal/` are only importable by packages in the same module:

```bash
myproject/
├── internal/
│   └── auth/       # Only myproject packages can import
│       └── auth.go
├── user/
│   └── user.go     # Can import internal/auth
└── main.go         # Can import internal/auth
```

```go
// Works (same module)
import "github.com/username/myproject/internal/auth"

// Error: use of internal package not allowed
// (from another module)
```

## Package Initialization

```go
package config

import "os"

var dbHost string
var dbPort string

// init() runs when package is imported
func init() {
    dbHost = os.Getenv("DB_HOST")
    dbPort = os.Getenv("DB_PORT")
}

// Multiple init functions allowed
func init() {
    validateConfig()
}
```

### Initialization Order

1. Import dependencies (depth-first)
2. Initialize package-level variables
3. Run `init()` functions in order of appearance
4. Repeat for each package
5. Run `main()`

## Multi-File Packages

```go
// user/user.go
package user

type User struct {
    ID   int
    Name string
}

// user/repository.go
package user

func Save(u *User) error {
    // Same package, can access User
    return nil
}

// user/service.go
package user

func CreateUser(name string) (*User, error) {
    u := &User{Name: name}
    err := Save(u)  // Same package, can call Save
    return u, err
}
```

## Build Tags

```go
//go:build linux
// +build linux

package mypackage

// This file only compiles on Linux
```

```go
//go:build !windows
// +build !windows

package mypackage

// This file compiles on everything except Windows
```

```go
//go:build integration
// +build integration

package mypackage_test

// Run with: go test -tags=integration
```

> **Good to know:** Package names should be short, lowercase, and singular (e.g., `user` not `users`). Avoid generic names like `util`, `common`, or `misc`. The `internal` directory is enforced by the Go toolchain—packages outside your module cannot import internal packages.

---

## References

- [Go Modules Reference](https://go.dev/ref/mod)
- [Go - How to Write Go Code](https://go.dev/doc/code)
- [Go Blog - Using Go Modules](https://go.dev/blog/using-go-modules)
- [Effective Go - Package Names](https://go.dev/doc/effective_go#package-names)
