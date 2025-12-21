# Exercise: Advanced

Practice reading a large, production codebase with multiple moving parts.

**Time limit: 60-90 minutes**

---

## Before You Start

This exercise assumes you've completed:
- Beginner exercise
- Intermediate exercise

You should be comfortable with:
- Tracing code across files
- Recognizing common patterns
- Taking structured notes
- Knowing when to stop

---

## The Repository

We'll practice with **Next.js** — a large, production framework.

**Repository:** [vercel/next.js](https://github.com/vercel/next.js)

**Why this repo:**
- Real-world, widely-used framework
- Monorepo structure
- Complex but well-organized
- Mix of frontend and build tooling
- Industry-standard code quality

**Warning:** This is a large codebase. The goal is NOT to understand everything. The goal is to practice navigating complexity.

---

## Part 1: Monorepo Orientation (10 minutes)

### Step 1: Identify the Structure

Open the repository root. Don't dive into folders yet.

Answer:
1. What type of monorepo structure is this?
2. Where are the packages? Apps?
3. What tooling manages the monorepo?

### Step 2: Find Key Packages

Locate:
- The main `next` package (what users `npm install`)
- Examples (if they exist)
- Documentation (if it exists)

### Step 3: Map the Territory

In your notes, draw a rough map:

```
next.js/
├── [main package location]: ???
├── [examples location]: ???
├── [other key folders]: ???
└── [config files]: ???
```

<details>
<summary>Check your orientation</summary>

```
next.js/
├── packages/next/          # The main Next.js package
├── packages/create-next-app/  # CLI to create new apps
├── examples/               # Example projects
├── docs/                   # Documentation (may be separate repo)
├── test/                   # Tests
├── turbo.json             # Turborepo config
└── pnpm-workspace.yaml    # Workspace definition
```

Don't worry if your map differs slightly. The structure evolves.

</details>

---

## Part 2: Focus on One Package (15 minutes)

We'll focus on `packages/next` — the core framework.

### Step 1: Navigate to the Package

Open `packages/next/`. Look at its structure.

Answer:
1. What folders exist?
2. What does `package.json` export?
3. Where are the entry points?

### Step 2: Identify the Core Areas

Next.js has several major features:
- Pages/App router
- Server-side rendering
- API routes
- Build/compilation
- Dev server

Without reading code, identify which folders likely contain each feature.

Make guesses like:
- Pages router → probably `src/client/` or `src/server/`
- Build system → probably `src/build/`

---

## Part 3: Trace One Feature (30 minutes)

Pick ONE feature to trace. I recommend: **How does `getServerSideProps` work?**

### The Question

When you write:

```jsx
export async function getServerSideProps(context) {
  return { props: { data: 'hello' } };
}

export default function Page({ data }) {
  return <div>{data}</div>;
}
```

How does Next.js:
1. Know this page has `getServerSideProps`?
2. Call it on the server?
3. Pass the result to the component?

### Tracing Strategy

1. **Search for the function name:**
   ```bash
   grep -r "getServerSideProps" packages/next/src --include="*.ts"
   ```

2. **Look for where it's called, not defined:**
   User code defines it; Next.js code calls it.

3. **Find the server request handler:**
   There must be code that handles incoming requests and decides what to do.

4. **Follow the request flow:**
   ```
   HTTP request → ??? → getServerSideProps called → ??? → HTML response
   ```

### What to Note

As you trace, write down:
- File paths you visited
- Key functions you found
- What you understood
- What you skipped (and why)

### Time Check

After 30 minutes, stop tracing. Write what you learned, even if incomplete.

<details>
<summary>Hints if you're stuck</summary>

- Look in `packages/next/src/server/` for request handling
- Search for `renderToHTML` or `renderPage`
- The render function likely checks for `getServerSideProps`
- Data fetching probably happens in something like `getServerSidePropsResult`

Don't worry about the exact path. The goal is practicing navigation.

</details>

---

## Part 4: Explore the Build System (15 minutes)

Next.js compiles your code. Let's understand a piece of it.

### Question

When you run `next build`, what happens to your pages?

### Exploration

1. Find the build entry point:
   - Check `package.json` → `bin` field
   - Or search for "build" in the src folder

2. Trace one step:
   - How are pages discovered?
   - How does it know what to compile?

3. Look for configuration:
   - Where is webpack/turbopack configured?
   - How are plugins applied?

### Don't Go Deep

This is complex. Note:
- The major steps you can identify
- The files involved
- What you'd need to learn to understand more

---

## Part 5: Reading Without Understanding (10 minutes)

This is an advanced skill: extracting information from code you don't fully understand.

### Exercise

Open any file you encountered that confused you.

Practice these techniques:

1. **Read the function names:**
   ```typescript
   async function renderToHTMLOrFlight(...)
   ```
   - What does the name suggest?
   - "render", "HTML", "Flight" — Flight is React's streaming format

2. **Read the imports:**
   ```typescript
   import { renderToReadableStream } from 'react-dom/server';
   ```
   - This tells you React server rendering is involved

3. **Read the types:**
   ```typescript
   function getProps(
     ctx: GetServerSidePropsContext
   ): Promise<GetServerSidePropsResult>
   ```
   - Input: context with request/response
   - Output: the props result

4. **Read the control flow:**
   - What are the major if/else branches?
   - What conditions determine the path?

### Write Down

For one confusing file:
- What does it probably do? (guess from names)
- What would you need to learn to understand it fully?
- Is it critical for your task or safe to ignore?

---

## Part 6: Reflection

Answer these questions:

### What Did You Learn?

1. How does Next.js organize a large codebase?
2. What patterns did you recognize?
3. What was surprisingly simple?
4. What was surprisingly complex?

### What Strategies Worked?

1. Which search techniques helped most?
2. How did you decide what to ignore?
3. When did you know to stop going deeper?

### What Would You Do Differently?

1. If you had to make a bug fix, what would you do first?
2. If you had to add a feature, how would you find where?
3. What questions would you ask a maintainer?

---

## Advanced Challenges

### Challenge 1: Compare App Router vs Pages Router

Next.js has two routing systems.
- Find where each is implemented
- What's different in the request handling?
- Why might they have separate code?

### Challenge 2: Trace Error Handling

When a page throws an error:
- How is it caught?
- How is the error page rendered?
- Where is error boundary logic?

### Challenge 3: Understand Middleware

Next.js has middleware that runs on the edge.
- Where is middleware code processed?
- How is it different from regular server code?

### Challenge 4: Build Time vs Runtime

Identify which code runs:
- At build time (`next build`)
- At request time (`next start`)
- On the client

---

## More Advanced Practice

Other large codebases to practice with:

1. **[prisma/prisma](https://github.com/prisma/prisma)**
   - Database ORM
   - Code generation
   - Client/engine split

2. **[trpc/trpc](https://github.com/trpc/trpc)**
   - Type-safe APIs
   - Monorepo with multiple packages

3. **[vitejs/vite](https://github.com/vitejs/vite)**
   - Build tool
   - Plugin architecture

4. **[facebook/react](https://github.com/facebook/react)**
   - The React library itself
   - Very different architecture

---

## What You Practiced

1. ✅ Navigated a production monorepo
2. ✅ Found relevant code in a large codebase
3. ✅ Traced a feature through complex code
4. ✅ Extracted meaning from code you didn't fully understand
5. ✅ Made strategic decisions about what to ignore
6. ✅ Documented your exploration

---

## The Meta-Skill

At this level, the skill isn't "understanding code."

The skill is **managing your attention in complexity**.

You can't understand everything. You don't need to. You need to:
- Find what's relevant
- Understand it enough for your purpose
- Know what you're ignoring and why
- Know when to stop

This is how senior engineers navigate unfamiliar codebases.

---

## Next Steps

- Review the [`real-world-walkthroughs/`](../real-world-walkthroughs/) to see annotated examples
- Apply these skills to a codebase you work with
- Teach someone else this process
