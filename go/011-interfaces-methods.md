# Interfaces/Methods

## Interface Definition

```go
// Interface is a set of method signatures
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

// Composed interface
type ReadWriter interface {
    Reader
    Writer
}

// Multiple methods
type Store interface {
    Get(key string) (value string, err error)
    Set(key string, value string) error
    Delete(key string) error
}
```

## Implicit Implementation

```go
// Types implement interfaces implicitly
type File struct {
    name string
}

// File implements Reader by having Read method
func (f *File) Read(p []byte) (n int, err error) {
    // implementation
    return len(p), nil
}

// No "implements" keyword needed
var r Reader = &File{name: "test.txt"}  // Works!
```

## Empty Interface

```go
// Empty interface accepts any type
var anything interface{}
anything = 42
anything = "hello"
anything = []int{1, 2, 3}

// Go 1.18+ alias
var anything any
anything = true

// Function accepting any type
func printAny(v any) {
    fmt.Println(v)
}
```

## Type Assertions

```go
var i interface{} = "hello"

// Type assertion
s := i.(string)  // "hello"
fmt.Println(s)

// Safe type assertion
s, ok := i.(string)
if ok {
    fmt.Println("string:", s)
} else {
    fmt.Println("not a string")
}

// Panic if wrong type
n := i.(int)  // panic: interface conversion
```

## Type Switch

```go
func describe(i interface{}) {
    switch v := i.(type) {
    case int:
        fmt.Printf("Integer: %d\n", v)
    case string:
        fmt.Printf("String: %s\n", v)
    case bool:
        fmt.Printf("Boolean: %t\n", v)
    case nil:
        fmt.Println("Nil value")
    default:
        fmt.Printf("Unknown type: %T\n", v)
    }
}
```

## Common Standard Library Interfaces

```go
// io.Reader - anything that can be read from
type Reader interface {
    Read(p []byte) (n int, err error)
}

// io.Writer - anything that can be written to
type Writer interface {
    Write(p []byte) (n int, err error)
}

// fmt.Stringer - custom string representation
type Stringer interface {
    String() string
}

// error - built-in error interface
type error interface {
    Error() string
}

// sort.Interface - for sorting
type Interface interface {
    Len() int
    Less(i, j int) bool
    Swap(i, j int)
}

// http.Handler - HTTP request handling
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```

## Implementing Stringer

```go
type User struct {
    Name  string
    Email string
}

func (u User) String() string {
    return fmt.Sprintf("%s <%s>", u.Name, u.Email)
}

user := User{Name: "Alice", Email: "alice@example.com"}
fmt.Println(user)  // "Alice <alice@example.com>"
```

## Implementing error

```go
type ValidationError struct {
    Field   string
    Message string
}

func (e ValidationError) Error() string {
    return fmt.Sprintf("%s: %s", e.Field, e.Message)
}

func validate(email string) error {
    if email == "" {
        return ValidationError{Field: "email", Message: "is required"}
    }
    return nil
}
```

## Interface Composition

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Closer interface {
    Close() error
}

// Embedded interfaces
type ReadCloser interface {
    Reader
    Closer
}

// Implementation
type File struct{}

func (f *File) Read(p []byte) (n int, err error) {
    return 0, nil
}

func (f *File) Close() error {
    return nil
}

var rc ReadCloser = &File{}  // File implements both
```

## Interface Best Practices

```go
// 1. Small interfaces are better
type Writer interface {
    Write(p []byte) (n int, err error)
}

// 2. Accept interfaces, return structs
func ProcessData(r io.Reader) (*Result, error) {
    // Read from any Reader
    data, err := io.ReadAll(r)
    // Return concrete type
    return &Result{Data: data}, err
}

// 3. Define interfaces where they're used (consumer side)
package consumer

type Repository interface {
    GetUser(id string) (*User, error)
}

func NewService(repo Repository) *Service {
    return &Service{repo: repo}
}

// 4. Interface segregation
type UserReader interface {
    GetUser(id string) (*User, error)
}

type UserWriter interface {
    SaveUser(user *User) error
}

type UserRepository interface {
    UserReader
    UserWriter
}
```

## Nil Interface Gotcha

```go
// Interface has two parts: type and value
var w io.Writer  // nil (no type, no value)

// This is NOT nil!
var buf *bytes.Buffer  // nil pointer
w = buf                 // w has type (*bytes.Buffer), value nil
fmt.Println(w == nil)   // false!

// Check properly
func isNil(w io.Writer) bool {
    if w == nil {
        return true
    }
    val := reflect.ValueOf(w)
    return val.Kind() == reflect.Ptr && val.IsNil()
}
```

> **Good to know:** Go interfaces are satisfied implicitly—there's no `implements` keyword. This enables powerful decoupling and makes testing with mocks trivial. Keep interfaces small (1-3 methods) and define them in the package that uses them, not the package that implements them.

---

## References

- [Go - Interfaces](https://go.dev/tour/methods/9)
- [Effective Go - Interfaces](https://go.dev/doc/effective_go#interfaces)
- [Go Blog - The Laws of Reflection](https://go.dev/blog/laws-of-reflection)
- [Go Wiki - Interface Guidelines](https://github.com/golang/go/wiki/CodeReviewComments#interfaces)
