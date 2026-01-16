# Package Managers

Package managers are essential tools for managing dependencies in JavaScript/TypeScript projects. This guide covers **npm**, **yarn**, **pnpm**, and **bun**.

## Comparison

| Feature         | npm                 | yarn          | pnpm             | bun         |
| --------------- | ------------------- | ------------- | ---------------- | ----------- |
| **Speed**       | Moderate            | Fast          | Very Fast        | Fastest     |
| **Disk Space**  | High                | High          | Low (symlinks)   | Low         |
| **Lock File**   | `package-lock.json` | `yarn.lock`   | `pnpm-lock.yaml` | `bun.lockb` |
| **Workspaces**  | ✅                  | ✅            | ✅               | ✅          |
| **Plug'n'Play** | ❌                  | ✅ (optional) | ❌               | ❌          |
| **Native TS**   | ❌                  | ❌            | ❌               | ✅          |
| **Monorepo**    | Good                | Great         | Excellent        | Good        |

## Installation

### npm (comes with Node.js)

```bash
# Included with Node.js installation
node -v
npm -v
```

### yarn

```bash
# Using npm
npm install -g yarn

# Using corepack (Node.js 16.10+)
corepack enable
corepack prepare yarn@stable --activate

# Verify
yarn -v
```

### pnpm

```bash
# Using npm
npm install -g pnpm

# Using corepack
corepack enable
corepack prepare pnpm@latest --activate

# Verify
pnpm -v
```

### bun

```bash
# macOS/Linux
curl -fsSL https://bun.sh/install | bash

# Windows (via npm)
npm install -g bun

# Verify
bun -v
```

---

## Common Commands

### Initialize Project

```bash
# npm
npm init -y

# yarn (recommended)
yarn init -y

# pnpm
pnpm init

# bun
bun init
```

### Install Dependencies

```bash
# Install all dependencies from package.json

# npm
npm install

# yarn (recommended)
yarn

# pnpm
pnpm install

# bun
bun install
```

### Add Package

```bash
# Add production dependency

# npm
npm install lodash-es

# yarn (recommended)
yarn add lodash-es

# pnpm
pnpm add lodash-es

# bun
bun add lodash-es
```

### Add Dev Dependency

```bash
# npm
npm install -D typescript

# yarn (recommended)
yarn add -D typescript

# pnpm
pnpm add -D typescript

# bun
bun add -d typescript
```

### Remove Package

```bash
# npm
npm uninstall lodash-es

# yarn (recommended)
yarn remove lodash-es

# pnpm
pnpm remove lodash-es

# bun
bun remove lodash-es
```

### Update Packages

```bash
# Update all packages

# npm
npm update

# yarn (recommended)
yarn upgrade

# pnpm
pnpm update

# bun
bun update
```

### Run Scripts

```bash
# package.json: { "scripts": { "dev": "vite", "build": "tsc" } }

# npm
npm run dev
npm run build

# yarn (recommended)
yarn dev
yarn build

# pnpm
pnpm dev
pnpm build

# bun
bun run dev
bun run build
```

---

## Yarn (Recommended for TypeScript Projects)

### Why Yarn?

- **Fast & Reliable**: Parallel downloads, offline cache
- **Workspaces**: First-class monorepo support
- **Plug'n'Play**: Optional zero-install mode
- **Better UX**: Cleaner CLI output, interactive prompts

### Yarn Berry (v2+) Setup

```bash
# Initialize with latest yarn
yarn set version stable

# Enable TypeScript plugin
yarn plugin import typescript
```

### Useful Yarn Commands

```bash
# Interactive upgrade
yarn upgrade-interactive

# Why is package installed?
yarn why lodash-es

# Check for outdated packages
yarn outdated

# Run bin from node_modules
yarn dlx create-vite my-app

# Clean cache
yarn cache clean
```

### Yarn Workspaces (Monorepo)

```json
// package.json (root)
{
	"private": true,
	"workspaces": ["packages/*", "apps/*"]
}
```

```bash
# Install all workspace dependencies
yarn

# Run script in specific workspace
yarn workspace @myapp/web dev

# Add dependency to workspace
yarn workspace @myapp/api add express

# Run script in all workspaces
yarn workspaces foreach run build
```

---

## TypeScript Project Setup (with Yarn)

### 1. Initialize Project

```bash
mkdir my-project && cd my-project
yarn init -y
```

### 2. Install TypeScript & Dev Tools

```bash
yarn add -D typescript @types/node tsx
```

### 3. Initialize TypeScript

```bash
yarn tsc --init
```

### 4. Add Scripts to package.json

```json
{
	"name": "my-project",
	"scripts": {
		"dev": "tsx watch src/index.ts",
		"build": "tsc",
		"start": "node dist/index.js",
		"typecheck": "tsc --noEmit"
	},
	"devDependencies": {
		"@types/node": "^20.0.0",
		"tsx": "^4.0.0",
		"typescript": "^5.0.0"
	}
}
```

### 5. Create Source File

```bash
mkdir src
echo 'console.log("Hello, TypeScript!")' > src/index.ts
```

### 6. Run Development

```bash
yarn dev
```

---

## package.json Structure

```json
{
	"name": "my-app",
	"version": "1.0.0",
	"description": "My TypeScript application",
	"main": "dist/index.js",
	"types": "dist/index.d.ts",
	"type": "module",
	"scripts": {
		"dev": "tsx watch src/index.ts",
		"build": "tsc",
		"start": "node dist/index.js",
		"test": "vitest",
		"lint": "eslint src",
		"typecheck": "tsc --noEmit"
	},
	"dependencies": {
		"zod": "^3.22.0"
	},
	"devDependencies": {
		"@types/node": "^20.0.0",
		"typescript": "^5.0.0",
		"tsx": "^4.0.0",
		"vitest": "^1.0.0",
		"eslint": "^8.0.0"
	},
	"engines": {
		"node": ">=18.0.0"
	},
	"packageManager": "yarn@4.0.0"
}
```

---

## Tips

### Lock File

> **Important:** Always commit your lock file (`yarn.lock`) to version control. It ensures consistent installs across environments.

### Corepack

> **Good to know:** Use `corepack` (built into Node.js 16.10+) to manage package manager versions automatically:
>
> ```bash
> corepack enable
> # Now yarn/pnpm versions are managed per-project via packageManager field
> ```

### Performance Comparison

```bash
# Benchmark install times (approximate)
# Clean install of a medium-sized project:

# npm:  ~45 seconds
# yarn: ~25 seconds
# pnpm: ~15 seconds
# bun:  ~5 seconds
```

---

## References

- [npm Documentation](https://docs.npmjs.com/)
- [Yarn Documentation](https://yarnpkg.com/getting-started)
- [pnpm Documentation](https://pnpm.io/)
- [Bun Documentation](https://bun.sh/docs)
- [Corepack](https://nodejs.org/api/corepack.html)


---

## Metadata

- **Generated by AI**: Claude Opus 4.5 Thinking
- **Created at**: 2026-01-16 10:00
