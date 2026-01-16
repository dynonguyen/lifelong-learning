# Project Structure

## Simple Project (Single Package)

```bash
myapp/
├── go.mod
├── go.sum
├── main.go
├── handlers.go
├── models.go
└── utils.go
```

## Standard Layout

```bash
myapp/
├── cmd/                    # Executable entrypoints
│   ├── api/
│   │   └── main.go
│   ├── worker/
│   │   └── main.go
│   └── cli/
│       └── main.go
├── internal/               # Private packages (enforced by Go)
│   ├── config/
│   │   └── config.go
│   ├── user/
│   │   ├── handler.go
│   │   ├── service.go
│   │   ├── repository.go
│   │   └── user.go
│   └── database/
│       └── postgres.go
├── pkg/                    # Public packages (importable by others)
│   └── validator/
│       └── validator.go
├── api/                    # API specs (OpenAPI, protobuf)
│   └── openapi.yaml
├── web/                    # Web assets
│   ├── static/
│   └── templates/
├── scripts/                # Build/deploy scripts
│   └── migrate.sh
├── deployments/            # Docker, K8s configs
│   ├── Dockerfile
│   └── kubernetes/
├── docs/                   # Documentation
│   └── architecture.md
├── go.mod
├── go.sum
├── Makefile
└── README.md
```

## Domain-Driven Layout

```bash
myapp/
├── cmd/
│   └── api/
│       └── main.go
├── internal/
│   ├── domain/             # Business entities
│   │   ├── user.go
│   │   ├── order.go
│   │   └── product.go
│   ├── service/            # Business logic
│   │   ├── user_service.go
│   │   └── order_service.go
│   ├── repository/         # Data access
│   │   ├── user_repo.go
│   │   └── order_repo.go
│   ├── handler/            # HTTP handlers
│   │   ├── user_handler.go
│   │   └── order_handler.go
│   └── infrastructure/     # External services
│       ├── database/
│       └── cache/
├── go.mod
└── go.sum
```

## Flat Structure (Small Projects)

```bash
myapp/
├── go.mod
├── main.go
├── server.go
├── handlers.go
├── models.go
├── database.go
└── config.go
```

## Key Directories

### cmd/

Contains main packages for executables:

```go
// cmd/api/main.go
package main

import (
    "github.com/mycompany/myapp/internal/config"
    "github.com/mycompany/myapp/internal/server"
)

func main() {
    cfg := config.Load()
    srv := server.New(cfg)
    srv.Run()
}
```

### internal/

Private packages that can't be imported by external projects:

```go
// internal/user/service.go
package user

type Service struct {
    repo Repository
}

func NewService(repo Repository) *Service {
    return &Service{repo: repo}
}

func (s *Service) GetUser(id string) (*User, error) {
    return s.repo.FindByID(id)
}
```

### pkg/

Public packages that can be imported by other projects:

```go
// pkg/validator/validator.go
package validator

func IsEmail(s string) bool {
    // ...
}
```

## Clean Architecture Structure

```bash
myapp/
├── cmd/
│   └── api/
│       └── main.go
├── internal/
│   ├── entity/             # Enterprise business rules
│   │   └── user.go
│   ├── usecase/            # Application business rules
│   │   ├── user_usecase.go
│   │   └── interfaces.go
│   ├── interface/          # Interface adapters
│   │   ├── controller/
│   │   │   └── user_controller.go
│   │   ├── presenter/
│   │   │   └── user_presenter.go
│   │   └── repository/
│   │       └── user_repository.go
│   └── infrastructure/     # Frameworks and drivers
│       ├── database/
│       │   └── postgres.go
│       └── router/
│           └── router.go
├── go.mod
└── go.sum
```

## Module Per Service (Microservices)

```bash
platform/
├── services/
│   ├── user-service/
│   │   ├── go.mod
│   │   ├── cmd/
│   │   └── internal/
│   ├── order-service/
│   │   ├── go.mod
│   │   ├── cmd/
│   │   └── internal/
│   └── payment-service/
│       ├── go.mod
│       ├── cmd/
│       └── internal/
├── shared/                 # Shared libraries
│   ├── go.mod
│   └── pkg/
└── go.work                 # Go workspace
```

## Test Organization

```bash
myapp/
├── internal/
│   └── user/
│       ├── user.go
│       ├── user_test.go          # Unit tests (same package)
│       ├── service.go
│       ├── service_test.go
│       └── repository_mock.go    # Mock implementations
├── test/                         # Integration tests
│   ├── integration_test.go
│   └── e2e_test.go
└── testdata/                     # Test fixtures
    └── users.json
```

## Configuration Files

```bash
myapp/
├── .env.example           # Environment template
├── .gitignore
├── .golangci.yml          # Linter config
├── Dockerfile
├── docker-compose.yml
├── Makefile
├── go.mod
├── go.sum
└── README.md
```

## Makefile Example

```makefile
.PHONY: build test run lint clean

build:
	go build -o bin/api ./cmd/api

test:
	go test -v -race ./...

run:
	go run ./cmd/api

lint:
	golangci-lint run

clean:
	rm -rf bin/

docker-build:
	docker build -t myapp .

migrate:
	go run ./cmd/migrate
```

## Package Naming Tips

```go
// Package name matches directory
// internal/user/user.go
package user

type User struct {}  // user.User

// Avoid stutter
// Bad: user.UserService
// Good: user.Service

// One package per directory
// Can have multiple files in same package
```

> **Good to know:** Start simple and grow the structure as needed. Don't over-engineer upfront. The `internal/` directory is Go's built-in access control—use it to prevent external imports of internal packages. The `cmd/` directory is the standard place for executables.

---

## References

- [Go - How to Write Go Code](https://go.dev/doc/code)
- [Standard Go Project Layout](https://github.com/golang-standards/project-layout)
- [Go Blog - Organizing Go Code](https://go.dev/blog/organizing-go-code)
- [Clean Architecture in Go](https://github.com/bxcodec/go-clean-arch)
