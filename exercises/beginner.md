# Exercise: Beginner

Practice reading a real repository using the navigation process.

**Time limit: 30-45 minutes**

---

## Before You Start

Make sure you've read:
- [`mindset.md`](../mindset.md)
- [`navigation.md`](../navigation.md)

Have a text file or paper ready to take notes.

---

## The Repository

We'll practice with **json-server** — a simple, well-structured project.

**Repository:** [typicode/json-server](https://github.com/typicode/json-server)

**Why this repo:**
- Small and focused
- Clear purpose
- Clean structure
- Well documented

---

## Exercise 1: Understand the Purpose (2 minutes)

Open the repository. Read only the first part of the README.

Answer these questions in your notes:

1. **What does this project do?**
   - Write one sentence.

2. **Who is it for?**
   - Developers? End users? Both?

3. **What problem does it solve?**

**Stop reading the README after you can answer these.**

<details>
<summary>Check your answers</summary>

1. json-server creates a fake REST API from a JSON file.
2. Developers (specifically frontend developers who need a mock API).
3. Lets you prototype without building a real backend.

</details>

---

## Exercise 2: Identify the Project Type (2 minutes)

Look at the folder structure. Don't open files yet, just look at the names.

Answer:

1. **What type of project is this?**
   - [ ] Frontend app
   - [ ] Backend API
   - [ ] Library/Package
   - [ ] CLI tool
   - [ ] Other

2. **What clues told you?**

<details>
<summary>Check your answer</summary>

This is a **CLI tool** (and also a library).

Clues:
- `bin/` folder (CLI entry point)
- No `src/pages/` or `src/components/`
- `package.json` has a `bin` field
- README shows terminal commands

</details>

---

## Exercise 3: Find the Entry Point (5 minutes)

Find where execution starts.

1. Check `package.json`:
   - What's in the `bin` field?
   - What's in the `main` field?

2. Find and open the CLI entry point file.

3. Skim it. What does it do at a high level?

Write down:
- The file path of the entry point
- 2-3 things it does (without understanding every line)

<details>
<summary>Check your answer</summary>

Entry points:
- CLI: `bin/index.js` (or similar)
- Library: `src/index.js` or `lib/index.js`

The CLI entry point typically:
- Parses command-line arguments
- Loads the JSON file
- Starts the server

Don't worry if you didn't catch every detail.

</details>

---

## Exercise 4: Trace One Feature (20 minutes)

Let's trace what happens when you run `json-server db.json`.

### Step 1: Start at the CLI

Open the CLI entry file. Find where it:
- Parses arguments
- Calls the main server function

### Step 2: Find the Server Setup

Search for where the Express/HTTP server is created.

Answer:
- What file creates the server?
- What middleware or routes are registered?

### Step 3: Find Route Handling

When you make a GET request to `/posts`, what handles it?

Follow the code until you understand (roughly) how a request flows:
```
HTTP request → ??? → JSON response
```

### Step 4: Write Your Trace

In your notes, write a simplified flow:

```
1. CLI parses args → ???
2. ??? creates server
3. Request to /posts → ???
4. ??? reads from JSON file
5. Response sent
```

Fill in the blanks with file names or function names.

<details>
<summary>Example trace (your answer may differ)</summary>

```
1. bin/index.js parses args
2. src/index.js creates Express app
3. Request to /posts → router middleware
4. Router reads from lowdb (JSON database)
5. Express sends JSON response
```

Your trace doesn't need to match exactly. You succeed if you have a general understanding of the flow.

</details>

---

## Exercise 5: Reflection (5 minutes)

Answer these questions:

1. **What did you understand?**
   - Write 2-3 things you now know about this codebase.

2. **What didn't you understand?**
   - Write 2-3 things that confused you. This is valuable data!

3. **What would you Google?**
   - What concepts or libraries would you look up if you needed to work on this?

4. **What would you ask a maintainer?**
   - If you could ask one question, what would it be?

---

## Exercise 6: Find Something Specific

Can you find where the project handles:

1. **Custom routes** — Where does it support custom route configuration?

2. **Static files** — Where does it serve static files?

3. **Plural vs singular routes** — How does `/posts` vs `/posts/1` work?

Pick ONE of these. Spend no more than 5 minutes looking.

**It's okay if you can't find it.** The goal is practice, not perfection.

---

## What You Just Did

1. ✅ Understood the purpose without reading everything
2. ✅ Identified the project type from structure alone
3. ✅ Found the entry point
4. ✅ Traced one feature end-to-end
5. ✅ Stopped when you had enough understanding

This is exactly how experienced developers read code.

---

## Common Struggles (And That's Okay)

- **"I didn't understand the middleware."** That's fine. You identified it exists.
- **"I don't know lowdb/Express."** Note it and move on. You can learn it separately.
- **"I got lost in the code."** Where? That's useful information.
- **"I couldn't find X."** Search techniques improve with practice.

---

## Bonus Practice

Try the same process with these beginner-friendly repos:

1. **[expressjs/morgan](https://github.com/expressjs/morgan)**
   - HTTP request logger
   - Very small, single purpose

2. **[sindresorhus/is-online](https://github.com/sindresorhus/is-online)**
   - Checks internet connectivity
   - Tiny codebase

3. **[chalk/chalk](https://github.com/chalk/chalk)**
   - Terminal string styling
   - Clean library structure

For each:
- 30 minutes max
- Follow the same 5-step process
- Take notes

---

## Next Level

When you're comfortable with this:
- Try [`exercises/intermediate.md`](intermediate.md)
- Or explore the [`real-world-walkthroughs/`](../real-world-walkthroughs/)
