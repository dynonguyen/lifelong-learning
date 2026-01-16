# Basic Syntax/Declarations

TypeScript extends JavaScript syntax with type annotations:

```typescript
// Type annotations use colon syntax
const name: string = 'TypeScript';
const version: number = 5.0;
const isAwesome: boolean = true;

// Type inference (TypeScript infers the type)
const inferredString = 'Hello'; // type: string
const inferredNumber = 42; // type: number

// Function with typed parameters and return type
function greet(name: string): string {
	return `Hello, ${name}!`;
}

// Arrow function with types
const add = (a: number, b: number): number => a + b;
```

> **Good to know:** TypeScript files use `.ts` extension (or `.tsx` for React JSX).

---

## References

- [TypeScript Handbook - The Basics](https://www.typescriptlang.org/docs/handbook/2/basic-types.html)
- [TypeScript Handbook - Everyday Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html)
- [TypeScript Deep Dive - Getting Started](https://basarat.gitbook.io/typescript/getting-started)
