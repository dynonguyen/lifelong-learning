# Tips & Tricks

## Type Narrowing

```typescript
// typeof guard
function process(value: string | number) {
	if (typeof value === 'string') {
		return value.toUpperCase(); // TypeScript knows it's string
	}
	return value.toFixed(2); // TypeScript knows it's number
}

// instanceof guard
function handleError(error: unknown) {
	if (error instanceof Error) {
		console.log(error.message);
	}
}

// in operator
interface Bird {
	fly(): void;
}
interface Fish {
	swim(): void;
}

function move(animal: Bird | Fish) {
	if ('fly' in animal) {
		animal.fly();
	} else {
		animal.swim();
	}
}

// Discriminated unions
type Result = { status: 'success'; data: string } | { status: 'error'; error: Error };

function handle(result: Result) {
	if (result.status === 'success') {
		console.log(result.data);
	} else {
		console.error(result.error);
	}
}
```

## Assertion Functions

```typescript
function assertDefined<T>(value: T | undefined): asserts value is T {
	if (value === undefined) {
		throw new Error('Value is undefined');
	}
}

let maybeString: string | undefined = 'hello';
assertDefined(maybeString);
console.log(maybeString.toUpperCase()); // No error, type is narrowed
```

## Template Literal Types

```typescript
type HttpMethod = 'GET' | 'POST' | 'PUT' | 'DELETE';
type Endpoint = '/users' | '/posts';
type Route = `${HttpMethod} ${Endpoint}`;
// "GET /users" | "GET /posts" | "POST /users" | ...

// Dynamic keys
type EventName<T extends string> = `on${Capitalize<T>}`;
type ClickEvent = EventName<'click'>; // "onClick"
```

## Satisfies Operator

```typescript
// Validate type while keeping literal types
const colors = {
	red: '#ff0000',
	green: '#00ff00',
	blue: '#0000ff',
} satisfies Record<string, string>;

// colors.red is "#ff0000" (literal), not string
```

## Const Assertions

```typescript
// Without as const
const config = { timeout: 5000 }; // { timeout: number }

// With as const
const frozenConfig = { timeout: 5000 } as const;
// { readonly timeout: 5000 }

// Arrays become tuples
const tuple = [1, 'two', true] as const;
// readonly [1, "two", true]
```

## Non-null Assertion

```typescript
// Use sparingly - only when you're certain
const element = document.getElementById('app')!;
element.innerHTML = 'Hello';

// Prefer optional chaining + nullish coalescing
const safeElement = document.getElementById('app');
if (safeElement) {
	safeElement.innerHTML = 'Hello';
}
```

## Watch Mode & Hot Reload

### tsx (Recommended)

```bash
# Install
yarn add -D tsx

# Run with watch mode
yarn tsx watch src/index.ts
```

```json
// package.json
{
	"scripts": {
		"dev": "tsx watch src/index.ts"
	}
}
```

### nodemon

```bash
# Install
yarn add -D nodemon ts-node

# Run with nodemon
yarn nodemon --exec ts-node src/index.ts
```

```json
// nodemon.json
{
	"watch": ["src"],
	"ext": "ts,json",
	"ignore": ["src/**/*.test.ts"],
	"exec": "ts-node ./src/index.ts"
}
```

```json
// package.json
{
	"scripts": {
		"dev": "nodemon"
	}
}
```

### ts-node-dev (Alternative)

```bash
# Install
yarn add -D ts-node-dev

# Run
yarn ts-node-dev --respawn src/index.ts
```

```json
// package.json
{
	"scripts": {
		"dev": "ts-node-dev --respawn --transpile-only src/index.ts"
	}
}
```

### tsc --watch (Compile Only)

```bash
# Watch and compile to JavaScript
yarn tsc --watch
```

### Bun (Native Hot Reload)

```bash
# Bun has built-in watch mode
bun --watch src/index.ts

# Or with hot reload (preserves state)
bun --hot src/index.ts
```

> **Good to know:** For most TypeScript projects, `tsx watch` is the recommended choice - it's fast, requires no configuration, and supports both ESM and CommonJS.

> **Good to know:** Enable `strict: true` in `tsconfig.json` for the best type safety. It enables:
>
> - `strictNullChecks`
> - `strictFunctionTypes`
> - `strictBindCallApply`
> - `strictPropertyInitialization`
> - `noImplicitAny`
> - `noImplicitThis`
> - `useUnknownInCatchVariables`
> - `alwaysStrict`

---

## References

- [Dyno Tips & Tricks](https://github.com/dynonguyen/javascript-tips)
- [TypeScript Handbook - Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
- [TypeScript 4.9 - satisfies](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-9.html)
- [Total TypeScript Tips](https://www.totaltypescript.com/tips)
- [Type Challenges](https://github.com/type-challenges/type-challenges)
