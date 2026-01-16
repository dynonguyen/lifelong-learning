# Printing/Logging

TypeScript uses the same console methods as JavaScript:

```typescript
// Basic logging
console.log('Hello, World!');

// Logging with types
const user: string = 'Alice';
const age: number = 30;
console.log(`User: ${user}, Age: ${age}`);

// Different log levels
console.info('Information message');
console.warn('Warning message');
console.error('Error message');
console.debug('Debug message');

// Logging objects
const person = { name: 'Bob', role: 'Developer' };
console.log(person); // { name: 'Bob', role: 'Developer' }
console.table(person); // Formatted table output
console.dir(person, { depth: null }); // Deep object inspection

// Timing operations
console.time('operation');
// ... some operation
console.timeEnd('operation'); // operation: 1.234ms

// Grouping logs
console.group('User Details');
console.log('Name: Alice');
console.log('Email: alice@example.com');
console.groupEnd();
```

---

## References

- [MDN - Console API](https://developer.mozilla.org/en-US/docs/Web/API/Console)
- [Node.js - Console](https://nodejs.org/api/console.html)
- [Chrome DevTools - Console](https://developer.chrome.com/docs/devtools/console/)