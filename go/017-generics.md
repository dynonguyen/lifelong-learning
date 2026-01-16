# Generics

Generics were introduced in Go 1.18, enabling type parameters for functions, types, and interfaces.

## Basic Syntax

```go
// Generic function
func Min[T constraints.Ordered](a, b T) T {
    if a < b {
        return a
    }
    return b
}

// Usage (type inference)
Min(1, 2)        // int
Min(1.5, 2.5)    // float64
Min("a", "b")    // string

// Explicit type parameter
Min[int](1, 2)
```

## Type Constraints

```go
import "golang.org/x/exp/constraints"

// Built-in constraints (Go 1.21+)
any           // Any type
comparable    // Types that support == and !=

// constraints package
constraints.Ordered   // <, >, <=, >=
constraints.Integer   // All integer types
constraints.Float     // All float types
constraints.Signed    // Signed integers
constraints.Unsigned  // Unsigned integers
constraints.Complex   // Complex numbers

// Usage
func Max[T constraints.Ordered](a, b T) T {
    if a > b {
        return a
    }
    return b
}
```

## Custom Constraints

```go
// Interface as constraint
type Stringer interface {
    String() string
}

func PrintAll[T Stringer](items []T) {
    for _, item := range items {
        fmt.Println(item.String())
    }
}

// Type set constraint (union)
type Number interface {
    int | int32 | int64 | float32 | float64
}

func Sum[T Number](nums []T) T {
    var sum T
    for _, n := range nums {
        sum += n
    }
    return sum
}

// Approximation constraint (~)
type Integer interface {
    ~int | ~int8 | ~int16 | ~int32 | ~int64
}

// Matches int and all types with underlying type int
type MyInt int
var x MyInt = 5
Sum([]MyInt{1, 2, 3})  // Works with ~int
```

## Generic Types

```go
// Generic struct
type Stack[T any] struct {
    items []T
}

func (s *Stack[T]) Push(item T) {
    s.items = append(s.items, item)
}

func (s *Stack[T]) Pop() (T, bool) {
    if len(s.items) == 0 {
        var zero T
        return zero, false
    }
    item := s.items[len(s.items)-1]
    s.items = s.items[:len(s.items)-1]
    return item, true
}

// Usage
intStack := Stack[int]{}
intStack.Push(1)
intStack.Push(2)
val, _ := intStack.Pop()  // 2

// Generic map alias
type Map[K comparable, V any] map[K]V
users := Map[string, User]{}
```

## Multiple Type Parameters

```go
// Pair type
type Pair[T, U any] struct {
    First  T
    Second U
}

func MakePair[T, U any](first T, second U) Pair[T, U] {
    return Pair[T, U]{First: first, Second: second}
}

// Map function
func Map[T, U any](items []T, fn func(T) U) []U {
    result := make([]U, len(items))
    for i, item := range items {
        result[i] = fn(item)
    }
    return result
}

// Usage
numbers := []int{1, 2, 3}
strings := Map(numbers, strconv.Itoa)  // ["1", "2", "3"]
```

## Generic Interfaces

```go
// Interface with type parameter
type Container[T any] interface {
    Get() T
    Set(T)
}

// Implementation
type Box[T any] struct {
    value T
}

func (b *Box[T]) Get() T {
    return b.value
}

func (b *Box[T]) Set(v T) {
    b.value = v
}

// Verify implementation
var _ Container[int] = &Box[int]{}
```

## Common Generic Patterns

### Filter

```go
func Filter[T any](items []T, predicate func(T) bool) []T {
    result := make([]T, 0, len(items))
    for _, item := range items {
        if predicate(item) {
            result = append(result, item)
        }
    }
    return result
}

evens := Filter([]int{1, 2, 3, 4, 5}, func(n int) bool {
    return n%2 == 0
})
```

### Reduce

```go
func Reduce[T, U any](items []T, initial U, fn func(U, T) U) U {
    result := initial
    for _, item := range items {
        result = fn(result, item)
    }
    return result
}

sum := Reduce([]int{1, 2, 3, 4}, 0, func(acc, n int) int {
    return acc + n
})
```

### Find

```go
func Find[T any](items []T, predicate func(T) bool) (T, bool) {
    for _, item := range items {
        if predicate(item) {
            return item, true
        }
    }
    var zero T
    return zero, false
}
```

### Keys and Values

```go
func Keys[K comparable, V any](m map[K]V) []K {
    keys := make([]K, 0, len(m))
    for k := range m {
        keys = append(keys, k)
    }
    return keys
}

func Values[K comparable, V any](m map[K]V) []V {
    vals := make([]V, 0, len(m))
    for _, v := range m {
        vals = append(vals, v)
    }
    return vals
}
```

## Zero Values in Generics

```go
func Zero[T any]() T {
    var zero T
    return zero
}

func GetOrDefault[T any](ptr *T, def T) T {
    if ptr == nil {
        return def
    }
    return *ptr
}
```

## Type Inference

```go
// Type can often be inferred
func First[T any](items []T) T {
    return items[0]
}

First([]int{1, 2, 3})        // T inferred as int
First([]string{"a", "b"})    // T inferred as string

// Sometimes explicit type needed
func New[T any]() *T {
    return new(T)
}

ptr := New[int]()  // Must specify type
```

## Limitations

```go
// Cannot use type parameters with methods
type Container[T any] struct {
    items []T
}

// ERROR: methods cannot have type parameters
func (c *Container[T]) Transform[U any](fn func(T) U) []U { ... }

// Workaround: use function instead
func Transform[T, U any](c *Container[T], fn func(T) U) []U { ... }

// Cannot use type parameters in type assertions
func example[T any](v any) T {
    // ERROR: cannot use type parameter in type assertion
    return v.(T)
}
```

> **Good to know:** Start without generics—add them when you find yourself writing the same logic for multiple types. The standard library's `slices`, `maps`, and `cmp` packages (Go 1.21+) provide many generic utilities out of the box.

---

## References

- [Go - Tutorial: Getting started with generics](https://go.dev/doc/tutorial/generics)
- [Go - Type Parameters Proposal](https://go.googlesource.com/proposal/+/refs/heads/master/design/43651-type-parameters.md)
- [Go - constraints Package](https://pkg.go.dev/golang.org/x/exp/constraints)
- [Go - slices Package](https://pkg.go.dev/slices)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
