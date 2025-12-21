# Navigation: The Step-by-Step Process

This is the **core algorithm** for reading any unfamiliar repository.

Follow these steps in order. Don't skip ahead.

---

## The Process Overview

```
Step 1: Understand the Purpose (2 minutes)
         ↓
Step 2: Identify the Project Type (2 minutes)
         ↓
Step 3: Find the Entry Point (5 minutes)
         ↓
Step 4: Trace One Feature (20-30 minutes)
         ↓
Step 5: Expand Only If Needed
```

Total time for initial exploration: **30-45 minutes max**.

---

## Step 1: Understand the Purpose

**Time limit: 2 minutes**

Before touching any code, answer this question:

> "What does this project do?"

Where to look:
1. **README.md** — Read the first 2-3 paragraphs only
2. **Project description** — The one-liner under the repo name on GitHub
3. **package.json / pyproject.toml / Cargo.toml** — The `description` field

What you're looking for:
- Is this a library or an application?
- Who uses this? (developers, end users, other services)
- What problem does it solve?

**Do not** read the entire README. Skim for purpose only.

### Example Output

> "This is a REST API that handles user authentication for a mobile app."

> "This is a React component library for building dashboards."

> "This is a CLI tool that converts markdown to PDF."

One sentence is enough. Move on.

---

## Step 2: Identify the Project Type

**Time limit: 2 minutes**

Now categorize what you're looking at:

| Type | Clues |
|------|-------|
| **Backend API** | `routes/`, `controllers/`, `api/`, Express/Flask/FastAPI |
| **Frontend App** | `src/components/`, `pages/`, React/Vue/Angular |
| **Library/Package** | `src/index.ts`, exports, no `pages/` or `routes/` |
| **CLI Tool** | `bin/`, `cli.js`, argument parsing |
| **Monorepo** | `packages/`, `apps/`, multiple package.json files |
| **Full-stack** | Both `frontend/` and `backend/` or `client/` and `server/` |

Why this matters: Each project type has **predictable patterns**. Once you know the type, you know where to look.

See the [`patterns/`](patterns/) folder for detailed breakdowns.

---

## Step 3: Find the Entry Point

**Time limit: 5 minutes**

Every codebase has a place where execution begins. Find it.

### Common Entry Points by Project Type

**Backend API:**
- `src/index.js` or `src/app.js`
- `main.py` or `app.py`
- `cmd/main.go`
- Look for: server startup, route registration

**Frontend App:**
- `src/index.js` or `src/main.tsx`
- `pages/_app.js` (Next.js)
- `src/App.vue` (Vue)
- Look for: root component, router setup

**Library:**
- `src/index.ts` or `lib/index.js`
- Look for: what's being exported

**CLI Tool:**
- Check `package.json` → `bin` field
- `cli.js`, `bin/index.js`
- Look for: argument parsing, main command

### How to Confirm You Found It

The entry point usually:
- Imports from many other files
- Is referenced in `package.json` (`main`, `bin`, `scripts`)
- Contains initialization logic (setup, config loading)
- Has comments like "start here" (if you're lucky)

### If You Can't Find It

1. Search for `main` or `start` in the codebase
2. Look at the `scripts` section in `package.json`
3. Check the README's "Getting Started" section
4. Look for files named `index`, `main`, `app`, or `server`

---

## Step 4: Trace One Feature

**Time limit: 20-30 minutes**

This is where the real learning happens.

Pick **one specific feature** and follow it through the code.

### How to Choose a Feature

Good choices:
- The simplest user-facing feature
- Something mentioned in the README
- A basic CRUD operation (create, read, update, delete)
- The "hello world" of the project

Bad choices:
- Complex features with many edge cases
- Features that touch authentication/authorization
- Anything described as "legacy" or "deprecated"

### The Tracing Process

1. **Start at the entry point**
2. **Find where your feature is triggered** (a route, a button, a command)
3. **Follow the function calls** — Click through each function
4. **Note what you discover:**
   - What files are involved?
   - What's the data flow?
   - What external services are called?
5. **Stop when you hit the "edge"** — database, API call, or external service

### What to Ignore While Tracing

- Error handling (for now)
- Logging
- Validation logic
- Comments
- Tests
- Anything that looks like boilerplate

You're tracing the **happy path** — the simplest successful execution.

### Take Notes

As you trace, write down:

```
Feature: User login

1. POST /api/login → routes/auth.js
2. authController.login() → controllers/auth.js
3. UserService.authenticate() → services/user.js
4. bcrypt.compare() → checks password
5. jwt.sign() → creates token
6. Returns token to client
```

These notes are gold. They're your map.

---

## Step 5: Expand Only If Needed

After tracing one feature, **stop and assess**.

Ask yourself:
- Can I complete my current task?
- Do I understand enough to ask a specific question?
- Do I need to trace another feature?

If yes to either of the first two: **you're done exploring**.

If you need more context:
- Trace ONE more feature using the same process
- Don't trace more than 2-3 features in one session

---

## Files You Can Safely Ignore (At First)

These files are important but rarely helpful for initial understanding:

| File/Folder | Why You Can Ignore It |
|-------------|----------------------|
| `.github/` | CI/CD configuration |
| `__tests__/`, `test/`, `spec/` | Tests (read later for examples) |
| `.eslintrc`, `.prettierrc` | Code style config |
| `Dockerfile`, `docker-compose.yml` | Deployment config |
| `.env.example` | Environment variables |
| `node_modules/`, `vendor/` | Dependencies |
| `*.config.js` | Build/tool configuration |
| `CHANGELOG.md` | Historical changes |
| `LICENSE` | Legal stuff |
| `migrations/` | Database history |

Come back to these when you need them. Not before.

---

## Files You Should Probably Read

| File/Folder | Why It's Useful |
|-------------|-----------------|
| `README.md` (first paragraphs) | Purpose and setup |
| `package.json` / `pyproject.toml` | Dependencies, scripts, entry points |
| `src/index.*` or `main.*` | Entry point |
| `routes/` or `pages/` | Application structure |
| `types/` or `*.d.ts` | Data shapes (TypeScript) |
| `models/` or `schema/` | Data structure |
| `config/` | How the app is configured |

---

## The Quick Reference Checklist

When opening any new repo:

- [ ] What does this do? (README, 2 min)
- [ ] What type of project is this? (structure, 2 min)
- [ ] Where does it start? (entry point, 5 min)
- [ ] How does one feature work? (trace, 20-30 min)
- [ ] Do I understand enough? (assess)

---

## What to Do When You're Stuck

1. **Can't find the entry point?**
   - Search for `main`, `start`, `init`
   - Check package.json scripts
   - Look at the README's "Getting Started"

2. **Feature is too complex?**
   - Pick a simpler feature
   - Look for utility functions first

3. **Code uses patterns you don't recognize?**
   - Note the pattern name
   - Google it separately
   - Don't let it block your tracing

4. **Can't understand what a function does?**
   - Look at its tests (now tests are useful!)
   - Look at where it's called from
   - Read the function name literally

5. **Been stuck for 15+ minutes?**
   - Take a break
   - Write down your specific question
   - Ask someone

---

## Next Steps

Now that you know the process:

1. Learn the patterns for your stack in [`patterns/`](patterns/)
2. Practice with a real repo in [`exercises/`](exercises/)
3. See the process applied in [`real-world-walkthroughs/`](real-world-walkthroughs/)
