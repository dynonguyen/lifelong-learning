# Objects/Structs

## Struct Definition

```go
// Basic struct
type User struct {
    ID        int
    Name      string
    Email     string
    IsActive  bool
    CreatedAt time.Time
}

// Struct with tags (for JSON, DB, validation)
type User struct {
    ID        int       `json:"id" db:"id"`
    Name      string    `json:"name" db:"name"`
    Email     string    `json:"email,omitempty" db:"email"`
    IsActive  bool      `json:"is_active" db:"is_active"`
    CreatedAt time.Time `json:"created_at" db:"created_at"`
}
```

## Creating Structs

```go
// Zero value initialization
var user User  // All fields get zero values

// Literal with field names (preferred)
user := User{
    ID:       1,
    Name:     "Alice",
    Email:    "alice@example.com",
    IsActive: true,
}

// Literal without field names (order matters, not recommended)
user := User{1, "Alice", "alice@example.com", true, time.Now()}

// Using new (returns pointer)
user := new(User)  // *User with zero values

// Pointer with literal
user := &User{
    ID:   1,
    Name: "Alice",
}
```

## Accessing Fields

```go
user := User{ID: 1, Name: "Alice"}

// Direct access
name := user.Name
user.Email = "alice@example.com"

// Pointer access (automatic dereference)
ptr := &user
ptr.Name = "Bob"  // Same as (*ptr).Name = "Bob"
```

## Anonymous Structs

```go
// Inline struct definition
point := struct {
    X, Y int
}{X: 10, Y: 20}

// Useful for one-off data structures
config := struct {
    Host string
    Port int
}{
    Host: "localhost",
    Port: 8080,
}
```

## Embedded Structs (Composition)

```go
type Address struct {
    Street  string
    City    string
    Country string
}

type Person struct {
    Name    string
    Age     int
    Address // Embedded (anonymous field)
}

// Usage
p := Person{
    Name: "Alice",
    Age:  30,
    Address: Address{
        Street:  "123 Main St",
        City:    "New York",
        Country: "USA",
    },
}

// Access embedded fields directly
fmt.Println(p.City)  // "New York" (promoted field)
fmt.Println(p.Address.City)  // Also works
```

## Methods on Structs

```go
type Rectangle struct {
    Width  float64
    Height float64
}

// Value receiver (receives a copy)
func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// Pointer receiver (can modify the struct)
func (r *Rectangle) Scale(factor float64) {
    r.Width *= factor
    r.Height *= factor
}

// Usage
rect := Rectangle{Width: 10, Height: 5}
fmt.Println(rect.Area())  // 50

rect.Scale(2)
fmt.Println(rect.Width)   // 20
```

## Constructor Functions

```go
// Constructor pattern (Go convention: NewTypeName)
func NewUser(name, email string) *User {
    return &User{
        Name:      name,
        Email:     email,
        IsActive:  true,
        CreatedAt: time.Now(),
    }
}

// With validation
func NewUser(name, email string) (*User, error) {
    if name == "" {
        return nil, errors.New("name is required")
    }
    if !isValidEmail(email) {
        return nil, errors.New("invalid email")
    }
    return &User{
        Name:      name,
        Email:     email,
        IsActive:  true,
        CreatedAt: time.Now(),
    }, nil
}
```

## Functional Options Pattern

```go
type Server struct {
    host    string
    port    int
    timeout time.Duration
}

type ServerOption func(*Server)

func WithHost(host string) ServerOption {
    return func(s *Server) {
        s.host = host
    }
}

func WithPort(port int) ServerOption {
    return func(s *Server) {
        s.port = port
    }
}

func WithTimeout(d time.Duration) ServerOption {
    return func(s *Server) {
        s.timeout = d
    }
}

func NewServer(opts ...ServerOption) *Server {
    s := &Server{
        host:    "localhost",
        port:    8080,
        timeout: 30 * time.Second,
    }
    for _, opt := range opts {
        opt(s)
    }
    return s
}

// Usage
server := NewServer(
    WithHost("0.0.0.0"),
    WithPort(3000),
    WithTimeout(time.Minute),
)
```

## Comparing Structs

```go
// Structs are comparable if all fields are comparable
type Point struct {
    X, Y int
}

p1 := Point{1, 2}
p2 := Point{1, 2}
fmt.Println(p1 == p2)  // true

// Structs with uncomparable fields (slices, maps) can't use ==
type Data struct {
    Values []int  // Not comparable
}
// Use reflect.DeepEqual for deep comparison
reflect.DeepEqual(d1, d2)
```

## JSON Serialization

```go
type User struct {
    ID       int    `json:"id"`
    Name     string `json:"name"`
    Email    string `json:"email,omitempty"`
    Password string `json:"-"`  // Excluded from JSON
}

// Marshal (struct to JSON)
user := User{ID: 1, Name: "Alice", Email: "alice@example.com"}
data, err := json.Marshal(user)
// {"id":1,"name":"Alice","email":"alice@example.com"}

// Unmarshal (JSON to struct)
var user User
err := json.Unmarshal([]byte(`{"id":1,"name":"Alice"}`), &user)
```

> **Good to know:** Use pointer receivers when the method needs to modify the struct or when the struct is large (to avoid copying). Be consistent—if one method has a pointer receiver, all methods on that type should too.

---

## References

- [Go - Structs](https://go.dev/tour/moretypes/2)
- [Go - Methods](https://go.dev/tour/methods/1)
- [Effective Go - Constructors and composite literals](https://go.dev/doc/effective_go#composite_literals)
- [Go Blog - JSON and Go](https://go.dev/blog/json)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
