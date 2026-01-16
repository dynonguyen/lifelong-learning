# Installation & Setup

## Install TypeScript Globally

**Using npm:**

```bash
npm install -g typescript
```

**Using yarn:**

```bash
yarn global add typescript
```

**Using bun:**

```bash
bun add -g typescript
```

## Install as Dev Dependency

**Using npm:**

```bash
npm install --save-dev typescript
```

**Using yarn:**

```bash
yarn add -D typescript
```

**Using bun:**

```bash
bun add -d typescript
```

## Verify Installation

```bash
tsc --version
```

## Initialize a TypeScript Project

```bash
tsc --init
```

This creates a `tsconfig.json` file with compiler options.

## Run TypeScript Directly (Without Compiling)

**Using ts-node (npm/yarn):**

```bash
# Install
npm install -g ts-node
# or
yarn global add ts-node

# Run
ts-node main.ts
```

**Using tsx (faster alternative):**

```bash
# Install
npm install -g tsx
# or
bun add -g tsx

# Run
tsx main.ts
```

**Using bun (native TypeScript support):**

```bash
# Bun runs TypeScript natively, no additional setup needed
bun main.ts
```

> **Good to know:** Bun has built-in TypeScript support and can run `.ts` files directly without any transpilation step, making it the fastest option for running TypeScript.

---

## References

- [TypeScript - Download](https://www.typescriptlang.org/download)
- [TypeScript - tsconfig.json](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)
- [ts-node Documentation](https://typestrong.org/ts-node/)
- [tsx GitHub](https://github.com/privatenumber/tsx)
- [Bun - TypeScript](https://bun.sh/docs/runtime/typescript)
