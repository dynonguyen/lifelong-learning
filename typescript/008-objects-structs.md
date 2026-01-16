# Objects/Structs

## Object Types

```typescript
// Inline object type
const user: { name: string; age: number; email?: string } = {
	name: 'Alice',
	age: 30,
};

// Type alias for objects
type User = {
	id: number;
	name: string;
	email: string;
	isActive: boolean;
	createdAt: Date;
};

const newUser: User = {
	id: 1,
	name: 'Bob',
	email: 'bob@example.com',
	isActive: true,
	createdAt: new Date(),
};

// Optional properties
type Config = {
	apiUrl: string;
	timeout?: number; // Optional
	retries?: number; // Optional
};

// Readonly properties
type ImmutablePoint = {
	readonly x: number;
	readonly y: number;
};

// Index signatures
type Dictionary = {
	[key: string]: string;
};

const translations: Dictionary = {
	hello: 'hola',
	goodbye: 'adiós',
};
```

## Nested Objects

```typescript
type Address = {
	street: string;
	city: string;
	country: string;
	zipCode: string;
};

type Person = {
	name: string;
	address: Address;
	contacts: {
		email: string;
		phone?: string;
	};
};

const person: Person = {
	name: 'Charlie',
	address: {
		street: '123 Main St',
		city: 'New York',
		country: 'USA',
		zipCode: '10001',
	},
	contacts: {
		email: 'charlie@example.com',
	},
};
```

---

## References

- [TypeScript Handbook - Object Types](https://www.typescriptlang.org/docs/handbook/2/objects.html)
- [TypeScript Handbook - Index Signatures](https://www.typescriptlang.org/docs/handbook/2/objects.html#index-signatures)
- [TypeScript Deep Dive - Type Assertion](https://basarat.gitbook.io/typescript/type-system/type-assertion)
