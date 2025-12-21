# Pattern: Libraries & Packages

Libraries are fundamentally different from applications. Learn to read them differently.

---

## Libraries vs Applications

| Aspect | Application | Library |
|--------|-------------|---------|
| Purpose | Runs and does things | Provides tools for others to use |
| Entry point | Starts execution | Exports functions/classes |
| Users | End users | Developers |
| Structure | Pages, routes | Modules, exports |
| Focus | Features | API surface |

The key difference: **Applications run. Libraries are imported.**

---

## The Standard Library Shape

```
project/
├── src/
│   ├── index.ts          # Main exports
│   ├── core/             # Core functionality
│   ├── utils/            # Internal helpers
│   └── types.ts          # Type definitions
├── dist/ (or build/)     # Compiled output
├── examples/             # Usage examples
├── docs/                 # Documentation
├── tests/
├── package.json
└── README.md
```

---

## How to Read a Library

### Step 1: Understand What It Does

Read the README's first section:
- What problem does it solve?
- What does basic usage look like?
- Who is it for?

### Step 2: Find the Public API

The public API is what users import. Look for:

**`src/index.ts` (or `index.js`):**
```typescript
// This file tells you EVERYTHING the library exposes
export { createClient } from './client';
export { parseData } from './parser';
export { formatOutput } from './formatter';
export type { Config, Options, Result } from './types';
```

This is your table of contents. These exports are the library's API.

### Step 3: Check package.json

```json
{
  "name": "my-library",
  "main": "./dist/index.js",        // CommonJS entry
  "module": "./dist/index.mjs",     // ESM entry
  "types": "./dist/index.d.ts",     // TypeScript types
  "exports": {
    ".": "./dist/index.js",
    "./utils": "./dist/utils.js"    // Sub-path exports
  }
}
```

This tells you:
- What files are the entry points
- What can be imported and how
- Whether there are sub-path imports (`import { x } from 'lib/utils'`)

### Step 4: Read the Types

If it's TypeScript (or has `.d.ts` files):

```typescript
// types.ts or index.d.ts
interface Config {
  apiKey: string;
  timeout?: number;
  retries?: number;
}

interface Client {
  connect(): Promise<void>;
  query(sql: string): Promise<Result[]>;
  disconnect(): void;
}
```

Types are the **contract**. They tell you exactly what the library expects and returns.

### Step 5: Look at Examples

Good libraries have an `examples/` folder or README examples:

```javascript
// examples/basic.js
import { createClient } from 'my-library';

const client = createClient({ apiKey: 'xxx' });
await client.connect();
const results = await client.query('SELECT * FROM users');
console.log(results);
```

Examples show you **how it's meant to be used**.

---

## Key Questions for Libraries

1. **What's the main export?** Usually a factory function or class.
2. **What configuration does it need?** Look at the Config/Options type.
3. **What's the lifecycle?** (init → use → cleanup)
4. **What errors can it throw?** Check for custom error classes.
5. **Is it sync or async?** Look for Promises, async/await.

---

## Common Library Patterns

### Factory Functions

```javascript
// Instead of: new Client(config)
// Libraries often use: createClient(config)

import { createClient } from 'library';
const client = createClient({ apiKey: 'xxx' });
```

**Why:** More flexible, can return different implementations.

---

### Builder Pattern

```javascript
const query = new QueryBuilder()
  .select('name', 'email')
  .from('users')
  .where('active', true)
  .limit(10)
  .build();
```

**Why:** Fluent API for complex configurations.

---

### Plugin/Middleware System

```javascript
const app = createApp();
app.use(loggingPlugin);
app.use(cachingPlugin);
app.use(customPlugin());
```

**Why:** Extensibility without modifying core code.

---

### Options Object

```javascript
// Instead of many parameters
function parse(input, format, strict, encoding) { }

// Libraries use options objects
function parse(input, { format, strict, encoding }) { }
```

**Why:** Readable, extensible, optional parameters.

---

## Reading Library Internals

When you need to understand *how* a library works:

### Follow One Function

1. Find a public function in `index.ts`
2. Follow it to its implementation
3. Note what it calls
4. Stop when you understand enough

### Look for Core Logic

```
src/
├── index.ts       # Exports (start here)
├── client.ts      # Main class/functions
├── parser.ts      # Core parsing logic ← usually the meat
├── utils.ts       # Helpers (less important)
└── constants.ts   # Values (safe to skim)
```

Core logic is usually in files named after what the library does (parser, validator, renderer, etc.).

### Check Test Files

Tests show you:
- Expected inputs and outputs
- Edge cases
- How the author thinks about the code

```javascript
// tests/parser.test.js
test('parses simple input', () => {
  expect(parse('hello')).toEqual({ text: 'hello' });
});

test('handles empty input', () => {
  expect(parse('')).toEqual({ text: '' });
});

test('throws on invalid input', () => {
  expect(() => parse(null)).toThrow(InvalidInputError);
});
```

---

## Type of Libraries

### Utility Libraries (lodash, date-fns)

```
src/
├── index.ts          # Re-exports everything
├── array/
│   ├── chunk.ts
│   ├── flatten.ts
│   └── index.ts
├── string/
│   ├── capitalize.ts
│   └── index.ts
```

**How to read:** Pick one function, read it. They're usually independent.

---

### Client Libraries (axios, prisma)

```
src/
├── index.ts
├── client.ts         # Main client class
├── request.ts        # Request handling
├── response.ts       # Response handling
├── interceptors.ts   # Middleware
└── errors.ts
```

**How to read:** Start with the client class, trace one method.

---

### Framework Libraries (express, fastapi)

```
src/
├── index.ts
├── application.ts    # Main app class
├── router.ts         # Routing logic
├── request.ts        # Request wrapper
├── response.ts       # Response wrapper
└── middleware.ts
```

**How to read:** Start with the application, trace request → router → handler → response.

---

### UI Component Libraries (radix, headlessui)

```
src/
├── index.ts
├── Button/
│   ├── Button.tsx
│   ├── Button.test.tsx
│   └── index.ts
├── Modal/
│   ├── Modal.tsx
│   └── index.ts
```

**How to read:** Pick one component. They're usually self-contained.

---

## What to Ignore in Libraries

- [ ] Build configuration (rollup, webpack, tsup)
- [ ] CI/CD setup
- [ ] Multiple output formats (CJS, ESM, UMD)
- [ ] Development tooling
- [ ] Changelog
- [ ] Contributing guidelines

Come back when you need them.

---

## Quick Reference

| I want to understand... | Look at... |
|------------------------|------------|
| What the library exports | `src/index.ts` |
| How to use it | `examples/` or README |
| What config it accepts | Types/interfaces |
| What it returns | Return types |
| How a function works | Follow from export to implementation |
| Edge cases | Test files |

---

## Reading Popular Library Patterns

### React Component Library

Entry: Look for the component export
```tsx
// src/Button/index.tsx
export { Button } from './Button';
export type { ButtonProps } from './Button.types';
```

Then read the component file to understand props and behavior.

### API Client Library

Entry: Look for `createClient` or `Client` class
```typescript
export function createClient(config: Config): Client {
  return new ClientImpl(config);
}
```

Then trace one method (like `get()` or `query()`).

### Build Tool / CLI Library

Entry: Check `package.json` → `bin` field
```json
{
  "bin": {
    "mytool": "./bin/cli.js"
  }
}
```

Then follow from CLI → argument parsing → core logic.

---

## Next Steps

- See how this differs from apps in [`patterns/backend.md`](backend.md)
- Practice reading a library in [`exercises/intermediate.md`](../exercises/intermediate.md)
