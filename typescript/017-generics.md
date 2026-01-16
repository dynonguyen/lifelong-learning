# Generics

## Generic Functions

```typescript
// Basic generic function
function identity<T>(value: T): T {
	return value;
}

const str = identity<string>('hello'); // Explicit
const num = identity(42); // Inferred as number

// Multiple type parameters
function pair<K, V>(key: K, value: V): [K, V] {
	return [key, value];
}

const entry = pair('name', 'Alice'); // [string, string]
```

## Generic Constraints

```typescript
// Constraint with extends
function getLength<T extends { length: number }>(item: T): number {
	return item.length;
}

getLength('hello'); // 5
getLength([1, 2, 3]); // 3

// Constraint with keyof
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
	return obj[key];
}

const user = { name: 'Alice', age: 30 };
const name = getProperty(user, 'name'); // string
```

## Generic Types and Interfaces

```typescript
// Generic type alias
type Container<T> = {
	value: T;
	timestamp: Date;
};

// Generic interface
interface Repository<T> {
	findById(id: string): Promise<T | null>;
	findAll(): Promise<T[]>;
	save(entity: T): Promise<T>;
	delete(id: string): Promise<boolean>;
}

// Implementation
class UserRepository implements Repository<User> {
	async findById(id: string): Promise<User | null> {
		// Implementation
		return null;
	}
	// ... other methods
}
```

## Generic Classes

```typescript
class Stack<T> {
	private items: T[] = [];

	push(item: T): void {
		this.items.push(item);
	}

	pop(): T | undefined {
		return this.items.pop();
	}

	peek(): T | undefined {
		return this.items[this.items.length - 1];
	}

	isEmpty(): boolean {
		return this.items.length === 0;
	}
}

const numberStack = new Stack<number>();
numberStack.push(1);
numberStack.push(2);
console.log(numberStack.pop()); // 2
```

## Utility Types (Built-in Generics)

```typescript
interface User {
	id: number;
	name: string;
	email: string;
	password: string;
}

// Partial - all properties optional
type PartialUser = Partial<User>;

// Required - all properties required
type RequiredUser = Required<PartialUser>;

// Readonly - all properties readonly
type ReadonlyUser = Readonly<User>;

// Pick - select specific properties
type UserPreview = Pick<User, 'id' | 'name'>;

// Omit - exclude specific properties
type PublicUser = Omit<User, 'password'>;

// Record - object type with specific key and value types
type UserRoles = Record<string, 'admin' | 'user'>;

// Extract/Exclude - filter union types
type StringOrNumber = string | number | boolean;
type OnlyStrings = Extract<StringOrNumber, string>; // string
type NoStrings = Exclude<StringOrNumber, string>; // number | boolean

// NonNullable - remove null and undefined
type MaybeString = string | null | undefined;
type DefiniteString = NonNullable<MaybeString>; // string

// ReturnType - get function return type
type AddResult = ReturnType<typeof add>; // number

// Parameters - get function parameter types
type AddParams = Parameters<typeof add>; // [a: number, b: number]
```

> **Good to know:** Use `infer` keyword for advanced type inference within conditional types:
>
> ```typescript
> type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;
> type Result = UnwrapPromise<Promise<string>>; // string
> ```

---

## References

- [TypeScript Handbook - Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [TypeScript Handbook - Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
- [TypeScript Deep Dive - Generics](https://basarat.gitbook.io/typescript/type-system/generics)
