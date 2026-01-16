# Arrays/Slices/Lists/Vectors

## Array Basics

```typescript
// Array type syntax
const numbers: number[] = [1, 2, 3, 4, 5];
const names: Array<string> = ['Alice', 'Bob', 'Charlie']; // Generic syntax

// Mixed types with union
const mixed: (string | number)[] = [1, 'two', 3, 'four'];

// Readonly arrays
const frozen: readonly number[] = [1, 2, 3];
// frozen.push(4); // Error - readonly

// Array methods (with type inference)
const doubled = numbers.map(n => n * 2); // number[]
const evens = numbers.filter(n => n % 2 === 0); // number[]
const sum = numbers.reduce((acc, n) => acc + n, 0); // number
```

## Tuples

```typescript
// Fixed-length arrays with specific types
type Point = [number, number];
const origin: Point = [0, 0];

// Named tuple elements (TypeScript 4.0+)
type HttpResponse = [status: number, body: string, headers?: object];
const response: HttpResponse = [200, '{"success": true}'];

// Rest elements in tuples
type StringNumberBooleans = [string, number, ...boolean[]];
const data: StringNumberBooleans = ['hello', 42, true, false, true];

// Readonly tuples
type ReadonlyPoint = readonly [number, number];
```

## Array Operations

```typescript
const fruits: string[] = ['apple', 'banana'];

// Adding elements
fruits.push('cherry'); // Add to end
fruits.unshift('apricot'); // Add to beginning

// Removing elements
const last = fruits.pop(); // Remove from end
const first = fruits.shift(); // Remove from beginning

// Slicing and splicing
const sliced = fruits.slice(1, 3); // New array [1, 3)
fruits.splice(1, 1, 'blueberry'); // Remove 1, insert "blueberry"

// Finding elements
const found = fruits.find(f => f.startsWith('b'));
const index = fruits.findIndex(f => f === 'cherry');
const includes = fruits.includes('apple');

// Spread operator
const moreFruits = [...fruits, 'dragonfruit'];
const combined = [...fruits, ...['grape', 'honeydew']];
```

> **Good to know:** Use `as const` to create a readonly tuple from an array literal:
>
> ```typescript
> const colors = ['red', 'green', 'blue'] as const;
> // type: readonly ["red", "green", "blue"]
> ```

---

## References

- [TypeScript Handbook - Arrays](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#arrays)
- [TypeScript Handbook - Tuple Types](https://www.typescriptlang.org/docs/handbook/2/objects.html#tuple-types)
- [MDN - Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
