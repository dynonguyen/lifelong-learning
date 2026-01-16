# Map/HashMap/Dictionary/Set

## Map

```typescript
// Creating a Map
const userRoles = new Map<string, string>();

// Adding entries
userRoles.set('alice', 'admin');
userRoles.set('bob', 'user');

// Getting values
const aliceRole = userRoles.get('alice'); // "admin" | undefined

// Checking existence
if (userRoles.has('alice')) {
	console.log('Alice exists');
}

// Deleting
userRoles.delete('bob');

// Iterating
for (const [key, value] of userRoles) {
	console.log(`${key}: ${value}`);
}

// Map from entries
const config = new Map<string, number>([
	['timeout', 5000],
	['retries', 3],
]);

// Map methods
console.log(config.size); // 2
config.clear(); // Remove all entries
```

## Set

```typescript
// Creating a Set
const uniqueNumbers = new Set<number>();

// Adding values
uniqueNumbers.add(1);
uniqueNumbers.add(2);
uniqueNumbers.add(1); // Ignored (already exists)

console.log(uniqueNumbers.size); // 2

// Checking existence
if (uniqueNumbers.has(1)) {
	console.log('1 exists');
}

// Deleting
uniqueNumbers.delete(2);

// Set from array
const unique = new Set([1, 2, 2, 3, 3, 3]); // Set {1, 2, 3}

// Convert to array
const arr = [...unique]; // [1, 2, 3]

// Set operations (manual)
const a = new Set([1, 2, 3]);
const b = new Set([2, 3, 4]);

const union = new Set([...a, ...b]); // {1, 2, 3, 4}
const intersection = new Set([...a].filter(x => b.has(x))); // {2, 3}
const difference = new Set([...a].filter(x => !b.has(x))); // {1}
```

## WeakMap and WeakSet

```typescript
// WeakMap (keys must be objects, allows garbage collection)
const cache = new WeakMap<object, string>();
let obj = { id: 1 };
cache.set(obj, 'cached value');
// When obj is no longer referenced, it can be garbage collected

// WeakSet (values must be objects)
const visited = new WeakSet<object>();
visited.add(obj);
visited.has(obj); // true
```

## Record Type (Object as Dictionary)

```typescript
// Record utility type
type UserScores = Record<string, number>;

const scores: UserScores = {
	alice: 100,
	bob: 85,
	charlie: 92,
};

// Typed keys
type Role = 'admin' | 'user' | 'guest';
type Permissions = Record<Role, string[]>;

const rolePermissions: Permissions = {
	admin: ['read', 'write', 'delete'],
	user: ['read', 'write'],
	guest: ['read'],
};
```

> **Good to know:** Use `Map` when you need non-string keys or care about insertion order. Use plain objects (`Record`) for simple string-keyed dictionaries with static structure.

---

## References

- [MDN - Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)
- [MDN - Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)
- [TypeScript Handbook - Utility Types (Record)](https://www.typescriptlang.org/docs/handbook/utility-types.html#recordkeys-type)
