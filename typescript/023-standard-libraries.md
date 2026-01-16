# Standard/Built-in Libraries

## Core Global Objects

```typescript
// Console
console.log(), console.error(), console.warn(), console.table();

// JSON
JSON.stringify(obj, null, 2);
JSON.parse(jsonString);

// Math
Math.max(1, 2, 3); // 3
Math.min(1, 2, 3); // 1
Math.floor(3.7); // 3
Math.ceil(3.2); // 4
Math.round(3.5); // 4
Math.random(); // 0-1

// Date
new Date();
Date.now();
new Date().toISOString();

// Object
Object.keys(obj);
Object.values(obj);
Object.entries(obj);
Object.assign({}, obj);
Object.freeze(obj);

// Array
Array.isArray(arr);
Array.from(iterable);
Array(5).fill(0);
```

## String Methods

```typescript
const str = 'Hello, World!';

str.length; // 13
str.toUpperCase(); // "HELLO, WORLD!"
str.toLowerCase(); // "hello, world!"
str.includes('World'); // true
str.startsWith('Hello'); // true
str.endsWith('!'); // true
str.indexOf('o'); // 4
str.slice(0, 5); // "Hello"
str.split(', '); // ["Hello", "World!"]
str.replace('World', 'TS'); // "Hello, TS!"
str.trim(); // Remove whitespace
str.padStart(15, '0'); // "00Hello, World!"
```

## Array Methods

```typescript
const arr = [1, 2, 3, 4, 5];

// Transformation
arr.map(n => n * 2); // [2, 4, 6, 8, 10]
arr.filter(n => n > 2); // [3, 4, 5]
arr.reduce((a, b) => a + b); // 15

// Search
arr.find(n => n > 2); // 3
arr.findIndex(n => n > 2); // 2
arr.includes(3); // true
arr.indexOf(3); // 2

// Testing
arr.every(n => n > 0); // true
arr.some(n => n > 4); // true

// Ordering
arr.sort((a, b) => b - a); // [5, 4, 3, 2, 1]
arr.reverse(); // [5, 4, 3, 2, 1]

// Flattening
[
	[1, 2],
	[3, 4],
].flat(); // [1, 2, 3, 4]
arr.flatMap(n => [n, n * 2]); // [1, 2, 2, 4, ...]
```

## Fetch API

```typescript
// GET request
const response = await fetch('https://api.example.com/users');
const users = await response.json();

// POST request
const newUser = await fetch('https://api.example.com/users', {
	method: 'POST',
	headers: {
		'Content-Type': 'application/json',
	},
	body: JSON.stringify({ name: 'Alice' }),
});
```

---

## References

- [MDN - JavaScript Reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference)
- [MDN - String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)
- [MDN - Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
- [MDN - Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
