# Package Manager

Go uses `go mod` as its built-in package manager (since Go 1.11).

## Initialize a Module

```bash
# Initialize new module
go mod init github.com/username/myproject

# Initialize with Go version
go mod init -go=1.22 github.com/username/myproject
```

## Adding Dependencies

```bash
# Add dependency (latest version)
go get github.com/gin-gonic/gin

# Add specific version
go get github.com/gin-gonic/gin@v1.9.1

# Add specific commit
go get github.com/gin-gonic/gin@abc123

# Add branch
go get github.com/gin-gonic/gin@main

# Add latest major version
go get github.com/gin-gonic/gin@latest
```

## Updating Dependencies

```bash
# Update single dependency to latest minor/patch
go get -u github.com/gin-gonic/gin

# Update to latest including major version
go get github.com/gin-gonic/gin@latest

# Update all dependencies (minor/patch)
go get -u ./...

# Update all dependencies (including test dependencies)
go get -u -t ./...

# Update only patch versions
go get -u=patch ./...
```

## Removing Dependencies

```bash
# Remove unused dependencies
go mod tidy

# Remove specific dependency
# (delete from go.mod, then run tidy)
go mod tidy
```

## Dependency Management

```bash
# Download dependencies to local cache
go mod download

# Verify dependency checksums
go mod verify

# Explain why a dependency exists
go mod why github.com/some/package

# View dependency graph
go mod graph

# View final versions of all dependencies
go list -m all
```

## Vendoring

```bash
# Create vendor directory with all dependencies
go mod vendor

# Build using vendor directory
go build -mod=vendor ./...

# Clean and rebuild vendor
rm -rf vendor && go mod vendor
```

## go.sum File

The `go.sum` file contains cryptographic checksums for modules:

```
github.com/gin-gonic/gin v1.9.1 h1:4idEAncQnU5cB7BeOkP...
github.com/gin-gonic/gin v1.9.1/go.mod h1:6rtgU7...
```

> Always commit `go.sum` to version control for reproducible builds.

## Private Modules

```bash
# Configure private module paths
go env -w GOPRIVATE=github.com/mycompany/*

# Or use comma-separated patterns
go env -w GOPRIVATE=github.com/mycompany/*,gitlab.mycompany.com/*

# Skip proxy for specific modules
go env -w GONOPROXY=github.com/mycompany/*

# Skip checksum verification
go env -w GONOSUMDB=github.com/mycompany/*
```

### Authentication for Private Repos

```bash
# Git credential helper
git config --global url."https://${GITHUB_TOKEN}@github.com/".insteadOf "https://github.com/"

# Or use .netrc file
# ~/.netrc
machine github.com
login your-username
password your-token
```

## Replace Directive

```go
// go.mod

// Replace with local path (development)
replace github.com/username/mylib => ../mylib

// Replace with fork
replace github.com/original/pkg => github.com/fork/pkg v1.0.0

// Replace specific version
replace github.com/pkg v1.2.3 => github.com/pkg v1.2.4
```

## Exclude Directive

```go
// go.mod

// Exclude problematic versions
exclude github.com/broken/pkg v1.2.3
exclude (
    github.com/broken/pkg v1.2.4
    github.com/broken/pkg v1.2.5
)
```

## Retract Directive

```go
// go.mod (for module authors)

// Retract accidental release
retract v1.0.0

// Retract with reason
retract (
    v1.0.1 // Contains critical bug
    [v1.0.2, v1.0.5] // Range of bad versions
)
```

## Workspaces (Go 1.18+)

For multi-module development:

```bash
# Initialize workspace
go work init

# Add modules to workspace
go work use ./api
go work use ./shared

# Sync workspace
go work sync
```

```go
// go.work
go 1.22

use (
    ./api
    ./shared
    ./worker
)

replace github.com/mycompany/shared => ./shared
```

## Common Commands Summary

| Command | Description |
|---------|-------------|
| `go mod init` | Initialize new module |
| `go mod tidy` | Add missing, remove unused dependencies |
| `go mod download` | Download dependencies to cache |
| `go mod verify` | Verify dependencies |
| `go mod vendor` | Create vendor directory |
| `go mod graph` | Print dependency graph |
| `go mod why` | Explain why package is needed |
| `go get` | Add or update dependencies |
| `go list -m all` | List all dependencies |
| `go list -m -u all` | List available updates |

## Environment Variables

```bash
# View all Go environment
go env

# Important variables
GOPATH      # Go workspace (default: ~/go)
GOMODCACHE  # Module cache location
GOPROXY     # Module proxy URL
GOPRIVATE   # Private module patterns
GONOPROXY   # Skip proxy for these modules
GONOSUMDB   # Skip checksum for these modules
```

> **Good to know:** Always run `go mod tidy` before committing. It ensures `go.mod` and `go.sum` are consistent and removes unused dependencies. Use `go mod vendor` if you need reproducible builds without network access.

---

## References

- [Go Modules Reference](https://go.dev/ref/mod)
- [Go Blog - Using Go Modules](https://go.dev/blog/using-go-modules)
- [Go Wiki - Modules](https://github.com/golang/go/wiki/Modules)
- [Go - Module Version Numbering](https://go.dev/doc/modules/version-numbers)
