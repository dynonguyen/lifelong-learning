# Installation & Setup

## Install Go

**macOS (using Homebrew):**

```bash
brew install go
```

**Linux (Ubuntu/Debian):**

```bash
# Download and install (replace version as needed)
wget https://go.dev/dl/go1.22.0.linux-amd64.tar.gz
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.22.0.linux-amd64.tar.gz

# Add to PATH in ~/.bashrc or ~/.zshrc
export PATH=$PATH:/usr/local/go/bin
```

**Windows:**

Download the MSI installer from [go.dev/dl](https://go.dev/dl/) and run it.

## Verify Installation

```bash
go version
# Output: go version go1.22.0 darwin/arm64
```

## Configure Environment

```bash
# View Go environment variables
go env

# Set GOPATH (optional, defaults to ~/go)
go env -w GOPATH=$HOME/go

# Add Go binaries to PATH
export PATH=$PATH:$(go env GOPATH)/bin
```

## Initialize a Go Project

```bash
# Create project directory
mkdir myproject && cd myproject

# Initialize a module
go mod init github.com/username/myproject
```

This creates a `go.mod` file:

```go
module github.com/username/myproject

go 1.22
```

## Create and Run Your First Program

```go
// main.go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Go!")
}
```

```bash
# Run directly
go run main.go

# Build binary
go build -o myapp main.go

# Run binary
./myapp
```

## Install Development Tools

```bash
# Install commonly used tools
go install golang.org/x/tools/gopls@latest          # Language server
go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest  # Linter
go install github.com/air-verse/air@latest          # Hot reload
```

> **Good to know:** Go downloads dependencies to `$GOPATH/pkg/mod` and installs binaries to `$GOPATH/bin`. Make sure `$GOPATH/bin` is in your `PATH` to run installed tools.

---

## References

- [Go - Download and Install](https://go.dev/doc/install)
- [Go - Getting Started](https://go.dev/doc/tutorial/getting-started)
- [Go - Managing Dependencies](https://go.dev/doc/modules/managing-dependencies)
