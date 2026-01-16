# Pointers/References/Garbage Collection

TypeScript (and JavaScript) handles memory automatically. There are no explicit pointers or manual memory management.

## Value vs Reference Types

```typescript
// Primitives are passed by value
let a = 10;
let b = a;
b = 20;
console.log(a); // 10 (unchanged)

// Objects are passed by reference
const obj1 = { value: 10 };
const obj2 = obj1;
obj2.value = 20;
console.log(obj1.value); // 20 (changed!)

// Arrays are also references
const arr1 = [1, 2, 3];
const arr2 = arr1;
arr2.push(4);
console.log(arr1); // [1, 2, 3, 4]
```

## Copying Objects

```typescript
// Shallow copy
const original = { name: 'Alice', address: { city: 'NYC' } };

const shallowCopy = { ...original };
shallowCopy.name = 'Bob'; // Doesn't affect original
shallowCopy.address.city = 'LA'; // AFFECTS original!

// Deep copy (modern)
const deepCopy = structuredClone(original);
deepCopy.address.city = 'Chicago'; // Doesn't affect original

// Deep copy (older approach)
const jsonCopy = JSON.parse(JSON.stringify(original));
```

## Garbage Collection

```typescript
// Objects are garbage collected when no references exist
function createTemporary(): void {
	const temp = { data: new Array(1000000) };
	// temp is eligible for GC after function returns
}

// Prevent memory leaks
class EventManager {
	private handlers: Map<string, Function[]> = new Map();

	on(event: string, handler: Function): void {
		const handlers = this.handlers.get(event) ?? [];
		handlers.push(handler);
		this.handlers.set(event, handlers);
	}

	// Important: provide cleanup method
	off(event: string, handler: Function): void {
		const handlers = this.handlers.get(event);
		if (handlers) {
			const index = handlers.indexOf(handler);
			if (index > -1) handlers.splice(index, 1);
		}
	}

	// Clear all to prevent leaks
	destroy(): void {
		this.handlers.clear();
	}
}
```

## WeakRef (Advanced)

```typescript
// WeakRef allows reference without preventing GC
class Cache {
	private cache = new Map<string, WeakRef<object>>();
	private registry = new FinalizationRegistry((key: string) => {
		this.cache.delete(key);
	});

	set(key: string, value: object): void {
		this.cache.set(key, new WeakRef(value));
		this.registry.register(value, key);
	}

	get(key: string): object | undefined {
		return this.cache.get(key)?.deref();
	}
}
```

> **Good to know:** Common memory leak sources in TypeScript/JavaScript:
>
> - Event listeners not removed
> - Closures holding references
> - Timers (`setInterval`) not cleared
> - Detached DOM nodes with references

---

## References

- [MDN - Memory Management](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management)
- [MDN - WeakRef](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakRef)
- [V8 - Garbage Collection](https://v8.dev/blog/trash-talk)
