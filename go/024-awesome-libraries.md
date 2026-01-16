# Awesome Libraries

A curated list of popular and well-maintained Go libraries.

## Web Frameworks

| Library | Description | Install |
|---------|-------------|---------|
| [Gin](https://github.com/gin-gonic/gin) | High-performance HTTP framework | `go get github.com/gin-gonic/gin` |
| [Echo](https://github.com/labstack/echo) | Minimalist, extensible framework | `go get github.com/labstack/echo/v4` |
| [Fiber](https://github.com/gofiber/fiber) | Express-inspired, fasthttp-based | `go get github.com/gofiber/fiber/v2` |
| [Chi](https://github.com/go-chi/chi) | Lightweight, composable router | `go get github.com/go-chi/chi/v5` |
| [gorilla/mux](https://github.com/gorilla/mux) | Powerful URL router | `go get github.com/gorilla/mux` |

```go
// Gin example
import "github.com/gin-gonic/gin"

r := gin.Default()
r.GET("/users/:id", func(c *gin.Context) {
    c.JSON(200, gin.H{"id": c.Param("id")})
})
r.Run(":8080")
```

## Database

| Library | Description | Install |
|---------|-------------|---------|
| [sqlx](https://github.com/jmoiron/sqlx) | Extensions to database/sql | `go get github.com/jmoiron/sqlx` |
| [GORM](https://github.com/go-gorm/gorm) | Full-featured ORM | `go get gorm.io/gorm` |
| [ent](https://github.com/ent/ent) | Entity framework with codegen | `go get entgo.io/ent` |
| [sqlc](https://github.com/sqlc-dev/sqlc) | Generate type-safe code from SQL | `go install github.com/sqlc-dev/sqlc/cmd/sqlc@latest` |
| [pgx](https://github.com/jackc/pgx) | PostgreSQL driver | `go get github.com/jackc/pgx/v5` |

```go
// sqlx example
import "github.com/jmoiron/sqlx"

db := sqlx.MustConnect("postgres", connStr)
var users []User
db.Select(&users, "SELECT * FROM users WHERE active = $1", true)
```

## Configuration

| Library | Description | Install |
|---------|-------------|---------|
| [Viper](https://github.com/spf13/viper) | Complete configuration solution | `go get github.com/spf13/viper` |
| [envconfig](https://github.com/kelseyhightower/envconfig) | Environment variable config | `go get github.com/kelseyhightower/envconfig` |
| [godotenv](https://github.com/joho/godotenv) | Load .env files | `go get github.com/joho/godotenv` |

```go
// envconfig example
type Config struct {
    Port     int    `envconfig:"PORT" default:"8080"`
    DBHost   string `envconfig:"DB_HOST" required:"true"`
    LogLevel string `envconfig:"LOG_LEVEL" default:"info"`
}

var cfg Config
envconfig.Process("", &cfg)
```

## Logging

| Library | Description | Install |
|---------|-------------|---------|
| [zap](https://github.com/uber-go/zap) | High-performance structured logging | `go get go.uber.org/zap` |
| [zerolog](https://github.com/rs/zerolog) | Zero allocation JSON logger | `go get github.com/rs/zerolog` |
| [logrus](https://github.com/sirupsen/logrus) | Structured, pluggable logger | `go get github.com/sirupsen/logrus` |

```go
// zap example
import "go.uber.org/zap"

logger, _ := zap.NewProduction()
defer logger.Sync()
logger.Info("User created",
    zap.String("user_id", "123"),
    zap.Int("age", 30),
)
```

## CLI

| Library | Description | Install |
|---------|-------------|---------|
| [Cobra](https://github.com/spf13/cobra) | CLI framework with subcommands | `go get github.com/spf13/cobra` |
| [urfave/cli](https://github.com/urfave/cli) | Simple CLI framework | `go get github.com/urfave/cli/v2` |
| [Bubble Tea](https://github.com/charmbracelet/bubbletea) | TUI framework | `go get github.com/charmbracelet/bubbletea` |

```go
// Cobra example
import "github.com/spf13/cobra"

rootCmd := &cobra.Command{
    Use:   "myapp",
    Short: "My application",
    Run: func(cmd *cobra.Command, args []string) {
        fmt.Println("Hello!")
    },
}
rootCmd.Execute()
```

## Testing

| Library | Description | Install |
|---------|-------------|---------|
| [testify](https://github.com/stretchr/testify) | Assertions and mocking | `go get github.com/stretchr/testify` |
| [gomock](https://github.com/uber-go/mock) | Mock generation | `go install go.uber.org/mock/mockgen@latest` |
| [go-cmp](https://github.com/google/go-cmp) | Deep equality comparison | `go get github.com/google/go-cmp/cmp` |
| [ginkgo](https://github.com/onsi/ginkgo) | BDD testing framework | `go get github.com/onsi/ginkgo/v2` |

```go
// testify example
import "github.com/stretchr/testify/assert"

func TestAdd(t *testing.T) {
    assert.Equal(t, 5, Add(2, 3))
    assert.NotNil(t, result)
    assert.NoError(t, err)
}
```

## Validation

| Library | Description | Install |
|---------|-------------|---------|
| [validator](https://github.com/go-playground/validator) | Struct validation | `go get github.com/go-playground/validator/v10` |
| [ozzo-validation](https://github.com/go-ozzo/ozzo-validation) | Fluent validation | `go get github.com/go-ozzo/ozzo-validation/v4` |

```go
// validator example
type User struct {
    Name  string `validate:"required,min=2"`
    Email string `validate:"required,email"`
    Age   int    `validate:"gte=0,lte=130"`
}

validate := validator.New()
err := validate.Struct(user)
```

## HTTP Client

| Library | Description | Install |
|---------|-------------|---------|
| [resty](https://github.com/go-resty/resty) | Simple HTTP client | `go get github.com/go-resty/resty/v2` |
| [req](https://github.com/imroc/req) | HTTP client with features | `go get github.com/imroc/req/v3` |

```go
// resty example
import "github.com/go-resty/resty/v2"

client := resty.New()
resp, _ := client.R().
    SetHeader("Authorization", "Bearer token").
    SetResult(&User{}).
    Get("https://api.example.com/users/1")
```

## Concurrency

| Library | Description | Install |
|---------|-------------|---------|
| [errgroup](https://pkg.go.dev/golang.org/x/sync/errgroup) | Error handling goroutine groups | `go get golang.org/x/sync/errgroup` |
| [conc](https://github.com/sourcegraph/conc) | Structured concurrency | `go get github.com/sourcegraph/conc` |

```go
// errgroup example
import "golang.org/x/sync/errgroup"

g, ctx := errgroup.WithContext(context.Background())
for _, url := range urls {
    url := url
    g.Go(func() error {
        return fetch(ctx, url)
    })
}
if err := g.Wait(); err != nil {
    return err
}
```

## Observability

| Library | Description | Install |
|---------|-------------|---------|
| [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-go) | Tracing and metrics | `go get go.opentelemetry.io/otel` |
| [Prometheus client](https://github.com/prometheus/client_golang) | Metrics exposition | `go get github.com/prometheus/client_golang` |

## gRPC

| Library | Description | Install |
|---------|-------------|---------|
| [grpc-go](https://github.com/grpc/grpc-go) | gRPC for Go | `go get google.golang.org/grpc` |
| [protobuf](https://github.com/protocolbuffers/protobuf-go) | Protocol Buffers | `go get google.golang.org/protobuf` |
| [connect-go](https://github.com/connectrpc/connect-go) | gRPC-compatible HTTP APIs | `go get connectrpc.com/connect` |

## Miscellaneous

| Library | Description | Install |
|---------|-------------|---------|
| [uuid](https://github.com/google/uuid) | UUID generation | `go get github.com/google/uuid` |
| [lo](https://github.com/samber/lo) | Lodash-style utilities | `go get github.com/samber/lo` |
| [goroutine pool](https://github.com/panjf2000/ants) | Goroutine pool | `go get github.com/panjf2000/ants/v2` |
| [cron](https://github.com/robfig/cron) | Cron job scheduler | `go get github.com/robfig/cron/v3` |

> **Good to know:** Before adding a dependency, check if the standard library can do what you need. When you do need external packages, prefer well-maintained libraries with active communities. Use `go mod tidy` to clean up unused dependencies.

---

## References

- [Awesome Go](https://github.com/avelino/awesome-go)
- [Go Packages](https://pkg.go.dev/)
- [Go Wiki - Projects](https://github.com/golang/go/wiki/Projects)
