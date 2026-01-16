# Functions/Macros

## Function Declarations

```typescript
// Named function
function add(a: number, b: number): number {
	return a + b;
}

// Arrow function
const multiply = (a: number, b: number): number => a * b;

// Function expression
const divide = function (a: number, b: number): number {
	return a / b;
};
```

## Parameters

```typescript
// Optional parameters
function greet(name: string, greeting?: string): string {
	return `${greeting ?? 'Hello'}, ${name}!`;
}

// Default parameters
function createUser(name: string, role: string = 'user'): object {
	return { name, role };
}

// Rest parameters
function sum(...numbers: number[]): number {
	return numbers.reduce((acc, n) => acc + n, 0);
}

// Destructured parameters
function printUser({ name, age }: { name: string; age: number }): void {
	console.log(`${name} is ${age} years old`);
}
```

## Function Types

```typescript
// Function type alias
type MathOperation = (a: number, b: number) => number;

const subtract: MathOperation = (a, b) => a - b;

// Callback types
function processArray(arr: number[], callback: (item: number, index: number) => number): number[] {
	return arr.map(callback);
}

// Function overloads
function format(value: string): string;
function format(value: number): string;
function format(value: string | number): string {
	if (typeof value === 'string') {
		return value.toUpperCase();
	}
	return value.toFixed(2);
}
```

## Higher-Order Functions

```typescript
// Function returning a function
function multiplier(factor: number): (n: number) => number {
	return n => n * factor;
}

const double = multiplier(2);
console.log(double(5)); // 10

// Function accepting a function
function retry<T>(fn: () => T, attempts: number): T | null {
	for (let i = 0; i < attempts; i++) {
		try {
			return fn();
		} catch {
			continue;
		}
	}
	return null;
}
```

> **Good to know:** TypeScript doesn't have macros like Rust. For metaprogramming, use decorators (experimental) or code generation tools.

---

## References

- [TypeScript Handbook - More on Functions](https://www.typescriptlang.org/docs/handbook/2/functions.html)
- [TypeScript Handbook - Function Overloads](https://www.typescriptlang.org/docs/handbook/2/functions.html#function-overloads)
- [MDN - Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions)
