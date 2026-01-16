# Variables & Constants

## Variable Declaration

```go
// Explicit type declaration
var name string
var age int
var isActive bool

// Declaration with initialization
var name string = "Alice"
var age int = 30

// Type inference
var message = "Hello"  // inferred as string
var count = 42         // inferred as int

// Short declaration (inside functions only)
name := "Bob"
age := 25
isActive := true

// Multiple variables
var x, y, z int
var a, b, c = 1, "two", 3.0

// Short declaration with multiple values
first, second := "one", 2
```

## Grouped Declarations

```go
var (
    name      string = "Alice"
    age       int    = 30
    isActive  bool   = true
    balance   float64
)
```

## Zero Values

Uninitialized variables get their zero value:

```go
var i int       // 0
var f float64   // 0.0
var b bool      // false
var s string    // "" (empty string)
var p *int      // nil
var sl []int    // nil
var m map[string]int  // nil
var ch chan int // nil
var fn func()   // nil
```

## Constants

```go
// Single constant
const Pi = 3.14159
const MaxRetries = 3

// Typed constant
const Timeout time.Duration = 30 * time.Second

// Grouped constants
const (
    StatusPending  = "pending"
    StatusActive   = "active"
    StatusComplete = "complete"
)

// Computed constants
const (
    KB = 1024
    MB = KB * 1024
    GB = MB * 1024
)
```

## iota (Constant Generator)

```go
// Basic enumeration (starts at 0)
const (
    Sunday = iota  // 0
    Monday         // 1
    Tuesday        // 2
    Wednesday      // 3
    Thursday       // 4
    Friday         // 5
    Saturday       // 6
)

// Skip values
const (
    _  = iota // skip 0
    KB = 1 << (10 * iota) // 1 << 10 = 1024
    MB                     // 1 << 20
    GB                     // 1 << 30
)

// Bitmask pattern
const (
    FlagRead  = 1 << iota  // 1
    FlagWrite              // 2
    FlagExec               // 4
)

// Multiple iota in same line
const (
    a, b = iota, iota + 10  // 0, 10
    c, d                     // 1, 11
)
```

## Reassignment vs Redeclaration

```go
// Reassignment
x := 1
x = 2  // OK

// Redeclaration (at least one new variable required)
x, y := 1, 2
x, z := 3, 4  // OK: z is new

// Cannot redeclare in same scope with no new variables
// x := 5  // Error: no new variables on left side
```

## Shadowing

```go
x := 1
if true {
    x := 2      // New variable, shadows outer x
    fmt.Println(x)  // 2
}
fmt.Println(x)      // 1

// Be careful with := in if statements
if x := getValue(); x > 0 {
    // x is scoped to this if block
}
// x not accessible here
```

## Blank Identifier

```go
// Ignore values
_, err := someFunction()

// Ignore index in range
for _, value := range slice {
    fmt.Println(value)
}

// Import for side effects
import _ "github.com/lib/pq"
```

> **Good to know:** Use `:=` for brevity inside functions, but prefer `var` for package-level variables and when you want to explicitly show the type. Constants in Go are truly immutable and evaluated at compile time—they can only be primitive types.

---

## References

- [Go - Variables](https://go.dev/tour/basics/8)
- [Go - Constants](https://go.dev/tour/basics/15)
- [Effective Go - Declarations and Assignments](https://go.dev/doc/effective_go#declarations)
- [Go Blog - Constants](https://go.dev/blog/constants)
