# Exercise: Intermediate

Practice reading a larger, more complex repository.

**Time limit: 45-60 minutes**

---

## Before You Start

Complete the beginner exercise first. Make sure you're comfortable with:
- Finding entry points
- Tracing features
- Taking structured notes

---

## The Repository

We'll practice with **zustand** — a popular React state management library.

**Repository:** [pmndrs/zustand](https://github.com/pmndrs/zustand)

**Why this repo:**
- Real-world library used by many
- Moderate complexity
- TypeScript
- Good documentation
- Common patterns you'll see elsewhere

---

## Part 1: Initial Reconnaissance (10 minutes)

### Step 1: Understand the Purpose

Read the first section of the README.

Answer:
1. What problem does Zustand solve?
2. How is it different from other solutions (Redux, Context)?
3. What's the basic usage pattern?

### Step 2: Identify Structure

Look at the folder structure.

Answer:
1. Where is the main source code?
2. Is this a library, app, or monorepo?
3. What testing framework do they use?

### Step 3: Find the Public API

Open `src/index.ts` (or equivalent).

Answer:
1. What functions/objects are exported?
2. Which export is the "main" one that users import?

Write down your findings before continuing.

<details>
<summary>Check your answers</summary>

1. Zustand is a lightweight state management library for React.
2. It's simpler than Redux (less boilerplate) and more powerful than Context (better performance).
3. `create()` function returns a hook you use in components.

Structure:
- Source in `src/`
- It's a library
- Tests likely use Jest or Vitest

Main exports:
- `create` — the primary function
- `createStore` — vanilla version
- Various middleware and utilities

</details>

---

## Part 2: Trace the Core API (25 minutes)

### Goal

Understand what happens when you write:

```javascript
import { create } from 'zustand';

const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}));
```

### Step 1: Find `create`

Locate the `create` function definition.

Questions to answer:
- What file is it in?
- What arguments does it take?
- What does it return?

### Step 2: Understand the Store Creation

Trace what `create()` does:
- How is the initial state created?
- How is `set` implemented?
- What's returned to the user?

Take notes like:
```
create(stateCreator) 
  → calls stateCreator with (set, get, api)
  → returns ??? 
```

### Step 3: Understand `set`

The `set` function is key to Zustand.

Find where `set` is defined and answer:
- How does it update state?
- How does it notify subscribers?

### Step 4: React Integration

How does the returned hook work with React?

Find where:
- React hooks are used (useState, useEffect, useSyncExternalStore)
- Component re-renders are triggered

Write your trace:
```
1. create() is called with state creator function
2. [what happens?]
3. Returns a React hook
4. When hook is called in component: [what happens?]
5. When set() is called: [what happens?]
6. Component re-renders because: [why?]
```

<details>
<summary>Hint: Key files to look at</summary>

- `src/vanilla.ts` — core store logic (non-React)
- `src/react.ts` — React bindings
- Look for `useSyncExternalStore` (React 18 subscription API)

</details>

---

## Part 3: Explore Middleware (15 minutes)

Zustand has middleware like `persist`, `devtools`, and `immer`.

### Pick ONE middleware

Look in the source for middleware. Pick one (e.g., `persist`).

Answer:
1. What file is it in?
2. What function signature does it have?
3. How does it wrap the state creator?
4. What's the general pattern for middleware?

### Middleware Pattern

Most Zustand middleware follows this pattern:

```javascript
const myMiddleware = (config) => (set, get, api) => {
  // Wrap set, get, or api
  // Call the original stateCreator
  // Return modified state
};
```

Can you see this pattern in the middleware you chose?

---

## Part 4: Questions to Answer

By now, you should be able to answer:

1. **How is state stored?** Where does the actual state object live?

2. **How does `set` work?** What happens when you call `set({ count: 5 })`?

3. **How do components subscribe?** How does React know to re-render?

4. **What's vanilla vs react?** Why are there separate modules?

5. **How does middleware work?** How can middleware intercept `set`?

Write your answers. Don't peek at the solution until you've tried.

<details>
<summary>Check your understanding</summary>

1. **State storage:** State is stored in a closure inside `createStore`. It's just a JavaScript object in memory.

2. **set function:** `set` merges new state with existing state (or replaces if you return new object), then calls all subscriber functions.

3. **Component subscription:** The React hook uses `useSyncExternalStore` (or a shim for older React) to subscribe to state changes. When state changes, React re-renders.

4. **Vanilla vs React:** `vanilla.ts` is the core store logic with no React dependency. `react.ts` wraps it with React hooks. This allows using Zustand outside React.

5. **Middleware:** Middleware wraps the state creator function. It can intercept `set`, `get`, or modify the API. It's like decorators or higher-order functions.

</details>

---

## Part 5: Reflection

Answer in your notes:

1. **What patterns did you recognize?**
   - Factory functions?
   - Closures?
   - Subscription pattern?
   - Middleware/decorator pattern?

2. **What was confusing?**
   - List specific things you'd want to learn more about.

3. **What would you look up?**
   - TypeScript generics?
   - useSyncExternalStore?
   - Closure scope?

4. **How long did this take?**
   - It's okay if it took longer than 45 minutes.

---

## Bonus Challenges

If you want more practice:

### Challenge 1: Add Logging

How would you write middleware that logs every state change?

```javascript
const logMiddleware = (config) => (set, get, api) => {
  // Your implementation
};
```

### Challenge 2: Find the Types

Look at the TypeScript types.
- What's the type of `create`?
- What's `StateCreator`?
- How do generics flow through?

### Challenge 3: Compare with Redux

Open the Redux Toolkit repo and compare:
- How is state stored differently?
- How are subscriptions handled?
- Why is Zustand simpler?

---

## More Practice Repositories

Try the same process with:

1. **[jotai](https://github.com/pmndrs/jotai)**
   - Another state library from the same team
   - Compare the architecture to Zustand

2. **[swr](https://github.com/vercel/swr)**
   - Data fetching library
   - Focus on the caching logic

3. **[zod](https://github.com/colinhacks/zod)**
   - Schema validation library
   - Heavy TypeScript

4. **[date-fns](https://github.com/date-fns/date-fns)**
   - Date utility library
   - Many small, independent functions

---

## What You Practiced

1. ✅ Read a real library used in production
2. ✅ Traced core functionality through multiple files
3. ✅ Understood a non-trivial pattern (subscriptions)
4. ✅ Explored middleware/plugin architecture
5. ✅ Read TypeScript code

---

## Next Level

Ready for more?
- Try [`exercises/advanced.md`](advanced.md)
- Explore [`real-world-walkthroughs/`](../real-world-walkthroughs/)
