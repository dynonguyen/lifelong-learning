# Testing/Unit Tests

## Jest (Popular Choice)

```typescript
// Install: npm install --save-dev jest @types/jest ts-jest
// Setup: npx ts-jest config:init

// user.ts
export function createUser(name: string, age: number) {
	if (age < 0) throw new Error('Age cannot be negative');
	return { name, age, id: Date.now() };
}

// user.test.ts
import { createUser } from './user';

describe('createUser', () => {
	it('should create a user with name and age', () => {
		const user = createUser('Alice', 30);
		expect(user.name).toBe('Alice');
		expect(user.age).toBe(30);
		expect(user.id).toBeDefined();
	});

	it('should throw error for negative age', () => {
		expect(() => createUser('Bob', -1)).toThrow('Age cannot be negative');
	});
});
```

## Vitest (Modern Alternative)

```typescript
// Install: npm install --save-dev vitest
// vitest.config.ts
import { defineConfig } from 'vitest/config';

export default defineConfig({
	test: {
		globals: true,
		environment: 'node',
	},
});

// user.test.ts
import { describe, it, expect, vi } from 'vitest';
import { createUser } from './user';

describe('createUser', () => {
	it('creates user correctly', () => {
		const user = createUser('Alice', 30);
		expect(user).toMatchObject({ name: 'Alice', age: 30 });
	});
});
```

## Mocking

```typescript
// Mocking functions
const mockFetch = vi.fn().mockResolvedValue({
	ok: true,
	json: () => Promise.resolve({ id: 1, name: 'Alice' }),
});

vi.stubGlobal('fetch', mockFetch);

// Mocking modules
vi.mock('./database', () => ({
	getUser: vi.fn().mockResolvedValue({ id: 1, name: 'Alice' }),
}));

// Spying
const consoleSpy = vi.spyOn(console, 'log');
// ... run code
expect(consoleSpy).toHaveBeenCalledWith('expected message');
```

## Testing Async Code

```typescript
describe('async operations', () => {
	it('should fetch user', async () => {
		const user = await fetchUser('1');
		expect(user.name).toBe('Alice');
	});

	it('should reject for invalid id', async () => {
		await expect(fetchUser('invalid')).rejects.toThrow('Not found');
	});
});
```

> **Good to know:** Use `describe.each` and `it.each` for parameterized tests:
>
> ```typescript
> describe.each([
> 	[1, 2, 3],
> 	[2, 3, 5],
> ])('add(%i, %i)', (a, b, expected) => {
> 	it(`returns ${expected}`, () => {
> 		expect(add(a, b)).toBe(expected);
> 	});
> });
> ```

---

## References

- [Jest Documentation](https://jestjs.io/docs/getting-started)
- [Vitest Documentation](https://vitest.dev/guide/)
- [Testing Library](https://testing-library.com/docs/)
- [Playwright - E2E Testing](https://playwright.dev/docs/intro)
