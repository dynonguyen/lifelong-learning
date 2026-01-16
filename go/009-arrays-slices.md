# Arrays/Slices

## Arrays

Arrays have a fixed size that's part of their type:

```go
// Declaration
var arr [5]int                    // [0, 0, 0, 0, 0]
arr := [5]int{1, 2, 3, 4, 5}      // Explicit size
arr := [...]int{1, 2, 3, 4, 5}   // Size inferred from elements

// Accessing elements
arr[0] = 10
fmt.Println(arr[0])  // 10
fmt.Println(len(arr)) // 5

// Iterate
for i, v := range arr {
    fmt.Printf("Index %d: %d\n", i, v)
}

// Arrays are values (copied on assignment)
arr1 := [3]int{1, 2, 3}
arr2 := arr1  // Full copy
arr2[0] = 99
fmt.Println(arr1[0])  // 1 (unchanged)
```

## Slices

Slices are dynamic views into arrays:

```go
// Declaration
var s []int              // nil slice
s := []int{}             // Empty slice (not nil)
s := []int{1, 2, 3}      // Slice literal
s := make([]int, 5)      // Length 5, capacity 5
s := make([]int, 5, 10)  // Length 5, capacity 10

// Slice from array
arr := [5]int{1, 2, 3, 4, 5}
s := arr[1:4]  // [2, 3, 4]
s := arr[:3]   // [1, 2, 3]
s := arr[2:]   // [3, 4, 5]
s := arr[:]    // [1, 2, 3, 4, 5]

// Length and capacity
s := make([]int, 3, 5)
fmt.Println(len(s))  // 3
fmt.Println(cap(s))  // 5
```

## Slice Operations

```go
// Append
s := []int{1, 2, 3}
s = append(s, 4)           // [1, 2, 3, 4]
s = append(s, 5, 6, 7)     // [1, 2, 3, 4, 5, 6, 7]
s = append(s, other...)    // Append another slice

// Copy
src := []int{1, 2, 3}
dst := make([]int, len(src))
n := copy(dst, src)  // Returns number of elements copied

// Delete element at index i
s = append(s[:i], s[i+1:]...)

// Insert at index i
s = append(s[:i], append([]int{value}, s[i:]...)...)

// Pop last element
last := s[len(s)-1]
s = s[:len(s)-1]

// Clear slice (keep capacity)
s = s[:0]
```

## Slice Gotchas

```go
// Slices share underlying array
arr := [5]int{1, 2, 3, 4, 5}
s1 := arr[1:3]  // [2, 3]
s2 := arr[2:4]  // [3, 4]
s1[1] = 99
fmt.Println(s2[0])  // 99 (shared element)

// Full slice expression (limits capacity)
s := arr[1:3:3]  // [2, 3], capacity = 3-1 = 2

// Append may create new array
s := make([]int, 3, 3)
s = append(s, 4)  // Capacity exceeded, new array allocated

// Copying vs referencing
s1 := []int{1, 2, 3}
s2 := s1        // Reference (shares memory)
s3 := make([]int, len(s1))
copy(s3, s1)    // Independent copy
```

## Multi-dimensional Slices

```go
// 2D slice
matrix := [][]int{
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9},
}

// Create dynamically
rows, cols := 3, 4
matrix := make([][]int, rows)
for i := range matrix {
    matrix[i] = make([]int, cols)
}

// Access
value := matrix[1][2]  // Row 1, Column 2
```

## Slice Patterns

```go
// Filter
func filter(s []int, fn func(int) bool) []int {
    result := make([]int, 0, len(s))
    for _, v := range s {
        if fn(v) {
            result = append(result, v)
        }
    }
    return result
}

// Map
func mapInts(s []int, fn func(int) int) []int {
    result := make([]int, len(s))
    for i, v := range s {
        result[i] = fn(v)
    }
    return result
}

// Contains
func contains(s []int, target int) bool {
    for _, v := range s {
        if v == target {
            return true
        }
    }
    return false
}

// Reverse
func reverse(s []int) {
    for i, j := 0, len(s)-1; i < j; i, j = i+1, j-1 {
        s[i], s[j] = s[j], s[i]
    }
}
```

## Sorting Slices

```go
import "sort"

// Sort built-in types
nums := []int{3, 1, 4, 1, 5}
sort.Ints(nums)  // [1, 1, 3, 4, 5]

strs := []string{"banana", "apple", "cherry"}
sort.Strings(strs)  // ["apple", "banana", "cherry"]

// Sort with custom comparator
sort.Slice(nums, func(i, j int) bool {
    return nums[i] > nums[j]  // Descending
})

// Check if sorted
sort.IntsAreSorted(nums)

// Binary search (slice must be sorted)
i := sort.SearchInts(nums, 4)
```

## slices Package (Go 1.21+)

```go
import "slices"

s := []int{3, 1, 4, 1, 5}

// Common operations
slices.Sort(s)                    // Sort in place
slices.Reverse(s)                 // Reverse in place
slices.Contains(s, 4)             // true
slices.Index(s, 4)                // Index of first 4
slices.Max(s)                     // Maximum value
slices.Min(s)                     // Minimum value

// Comparison
slices.Equal(s1, s2)              // Element-wise comparison
slices.Compare(s1, s2)            // -1, 0, or 1

// Manipulation
s2 := slices.Clone(s)             // Deep copy
s2 = slices.Compact(s)            // Remove consecutive duplicates
s2 = slices.Delete(s, 1, 3)       // Delete elements [1:3]
s2 = slices.Insert(s, 1, 10, 20)  // Insert at index 1
```

> **Good to know:** Always use slices over arrays unless you need a fixed size. Pre-allocate slice capacity with `make([]T, 0, expectedSize)` when you know the approximate size to avoid repeated allocations during append.

---

## References

- [Go - Slices](https://go.dev/tour/moretypes/7)
- [Go Blog - Go Slices: usage and internals](https://go.dev/blog/slices-intro)
- [Go Wiki - Slice Tricks](https://github.com/golang/go/wiki/SliceTricks)
- [Go - slices Package](https://pkg.go.dev/slices)
