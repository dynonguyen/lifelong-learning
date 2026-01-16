# Functions

## Function Declaration

```go
// Basic function
func greet() {
    fmt.Println("Hello!")
}

// With parameters
func add(a int, b int) int {
    return a + b
}

// Shortened parameter syntax (same type)
func add(a, b int) int {
    return a + b
}

// Multiple return values
func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("division by zero")
    }
    return a / b, nil
}

// Named return values
func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    return  // "naked return"
}
```

## Variadic Functions

```go
// Accept variable number of arguments
func sum(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}

// Usage
sum(1, 2, 3)        // 6
sum(1, 2, 3, 4, 5)  // 15

// Spread slice
nums := []int{1, 2, 3}
sum(nums...)  // 6
```

## Anonymous Functions

```go
// Assign to variable
add := func(a, b int) int {
    return a + b
}
result := add(1, 2)

// Immediately invoked
result := func(a, b int) int {
    return a + b
}(1, 2)

// As callback
process(func(x int) int {
    return x * 2
})
```

## Closures

```go
// Function that returns a function
func counter() func() int {
    count := 0
    return func() int {
        count++
        return count
    }
}

c := counter()
fmt.Println(c())  // 1
fmt.Println(c())  // 2
fmt.Println(c())  // 3

// Capture by reference (common gotcha)
for i := 0; i < 3; i++ {
    go func() {
        fmt.Println(i)  // May print 3, 3, 3
    }()
}

// Fix: pass as argument
for i := 0; i < 3; i++ {
    go func(n int) {
        fmt.Println(n)  // Prints 0, 1, 2
    }(i)
}
```

## Function Types

```go
// Function type definition
type MathFunc func(int, int) int

// Use as parameter
func apply(fn MathFunc, a, b int) int {
    return fn(a, b)
}

// Use as return type
func getOperation(op string) MathFunc {
    switch op {
    case "+":
        return func(a, b int) int { return a + b }
    case "-":
        return func(a, b int) int { return a - b }
    default:
        return func(a, b int) int { return 0 }
    }
}
```

## Methods

```go
type Rectangle struct {
    Width, Height float64
}

// Value receiver
func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// Pointer receiver (can modify struct)
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

## Defer

```go
// Defer executes after function returns
func readFile(path string) error {
    f, err := os.Open(path)
    if err != nil {
        return err
    }
    defer f.Close()  // Always executed

    // Read file...
    return nil
}

// Multiple defers (LIFO order)
func example() {
    defer fmt.Println("1")
    defer fmt.Println("2")
    defer fmt.Println("3")
}
// Output: 3, 2, 1

// Defer with arguments (evaluated immediately)
func example() {
    x := 10
    defer fmt.Println(x)  // Prints 10
    x = 20
}
```

## Panic and Recover

```go
// Panic stops normal execution
func mustDo() {
    panic("something went wrong")
}

// Recover catches panic
func safeCall() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered from:", r)
        }
    }()

    mustDo()  // Panic here
}

// Practical use: graceful HTTP handler recovery
func recoveryMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        defer func() {
            if err := recover(); err != nil {
                http.Error(w, "Internal Server Error", 500)
                log.Printf("Panic: %v", err)
            }
        }()
        next.ServeHTTP(w, r)
    })
}
```

## Higher-Order Functions

```go
// Function that takes a function
func filter(nums []int, predicate func(int) bool) []int {
    result := make([]int, 0)
    for _, n := range nums {
        if predicate(n) {
            result = append(result, n)
        }
    }
    return result
}

// Usage
evens := filter([]int{1, 2, 3, 4, 5}, func(n int) bool {
    return n%2 == 0
})

// Function that returns a function
func multiplier(factor int) func(int) int {
    return func(n int) int {
        return n * factor
    }
}

double := multiplier(2)
triple := multiplier(3)
fmt.Println(double(5))  // 10
fmt.Println(triple(5))  // 15
```

## init() Function

```go
package main

var config Config

// init runs before main
func init() {
    config = loadConfig()
}

// Multiple init functions allowed (per file, per package)
func init() {
    validateConfig(config)
}

func main() {
    // config is ready
}
```

> **Good to know:** Prefer returning errors over using panic. Use named return values sparingly—they're useful for documentation but can make code less readable. Always use pointer receivers if any method modifies the struct or if the struct is large.

---

## References

- [Go - Functions](https://go.dev/tour/basics/4)
- [Go - Methods](https://go.dev/tour/methods/1)
- [Effective Go - Functions](https://go.dev/doc/effective_go#functions)
- [Go Blog - Defer, Panic, and Recover](https://go.dev/blog/defer-panic-and-recover)
