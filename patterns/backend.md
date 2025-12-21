# Pattern: Backend APIs

Learn to recognize backend API structures instantly.

---

## The Standard Backend Shape

Most backend APIs follow this structure (with slight naming variations):

```
project/
├── src/
│   ├── routes/          # URL → handler mapping
│   ├── controllers/     # Request handling logic
│   ├── services/        # Business logic
│   ├── models/          # Data structures / DB schemas
│   ├── middleware/      # Pre/post request processing
│   ├── utils/           # Helper functions
│   └── config/          # Environment & settings
├── tests/
├── package.json / requirements.txt / go.mod
└── README.md
```

Once you see this pattern, you know exactly where to look.

---

## Layer by Layer

### Routes (or `api/`, `endpoints/`)

**What it does:** Maps URLs to handler functions.

**What you'll find:**
```javascript
// routes/users.js
router.get('/users', userController.getAll);
router.post('/users', userController.create);
router.get('/users/:id', userController.getById);
```

**Why it matters:** This is your TABLE OF CONTENTS. Open this first to see what the API can do.

---

### Controllers (or `handlers/`)

**What it does:** Receives requests, calls services, sends responses.

**What you'll find:**
```javascript
// controllers/userController.js
async function getById(req, res) {
  const user = await userService.findById(req.params.id);
  res.json(user);
}
```

**Why it matters:** This shows you the "what" without the "how."

---

### Services (or `logic/`, `domain/`)

**What it does:** Contains business logic. The actual work happens here.

**What you'll find:**
```javascript
// services/userService.js
async function findById(id) {
  const user = await UserModel.findById(id);
  if (!user) throw new NotFoundError('User not found');
  return sanitize(user);
}
```

**Why it matters:** This is where you understand HOW things work.

---

### Models (or `entities/`, `schemas/`)

**What it does:** Defines data structures and database interactions.

**What you'll find:**
```javascript
// models/User.js
const UserSchema = {
  id: String,
  email: String,
  name: String,
  createdAt: Date
};
```

**Why it matters:** This tells you what DATA exists in the system.

---

### Middleware

**What it does:** Runs before/after requests. Authentication, logging, validation.

**What you'll find:**
```javascript
// middleware/auth.js
function requireAuth(req, res, next) {
  const token = req.headers.authorization;
  if (!token) return res.status(401).send('Unauthorized');
  // verify token...
  next();
}
```

**Why it matters:** Usually safe to ignore initially. Come back when you hit auth issues.

---

## Naming Variations

Same concepts, different names:

| Concept | Possible Names |
|---------|---------------|
| Routes | `routes/`, `api/`, `endpoints/`, `routers/` |
| Controllers | `controllers/`, `handlers/`, `actions/` |
| Services | `services/`, `logic/`, `domain/`, `use-cases/` |
| Models | `models/`, `entities/`, `schemas/`, `db/` |
| Middleware | `middleware/`, `interceptors/`, `guards/` |

---

## Framework-Specific Patterns

### Express.js (Node.js)

```
src/
├── app.js            # Entry: creates Express app
├── routes/
├── controllers/
├── services/
└── models/
```

Entry point clue: Look for `express()` and `app.listen()`.

---

### FastAPI / Flask (Python)

```
app/
├── main.py           # Entry: creates app instance
├── routers/          # FastAPI uses "routers"
├── services/
├── models/
└── schemas/          # Pydantic models for validation
```

Entry point clue: Look for `@app.route` or `APIRouter()`.

---

### Django (Python)

```
project/
├── manage.py         # CLI entry point
├── project/
│   ├── settings.py   # Configuration
│   └── urls.py       # Root URL routing
└── apps/
    └── users/
        ├── views.py      # Like controllers
        ├── models.py
        ├── urls.py       # App-specific routes
        └── serializers.py
```

Entry point clue: Look for `urls.py` and follow the URL patterns.

---

### Spring Boot (Java)

```
src/main/java/com/example/
├── Application.java      # Entry: @SpringBootApplication
├── controller/
├── service/
├── repository/           # Like models, database access
└── model/
```

Entry point clue: Look for `@SpringBootApplication` and `main()`.

---

### Go (net/http or Gin)

```
cmd/
├── main.go               # Entry point
internal/
├── handlers/             # Like controllers
├── services/
├── models/
└── repository/
pkg/                      # Shared packages
```

Entry point clue: `cmd/` folder is conventional for entry points.

---

## How to Read a Backend Repo

### Step 1: Find the Route You Care About

```bash
# Search for the endpoint
grep -r "POST /users" --include="*.js"
grep -r "@app.post" --include="*.py"
```

### Step 2: Follow the Chain

```
Route → Controller → Service → Model → Database
```

Each step adds detail. Stop when you have enough.

### Step 3: Understand the Data

Check the models to understand:
- What fields exist?
- What's required vs optional?
- What are the relationships?

---

## Common Patterns to Recognize

### Repository Pattern

Data access is abstracted into "repositories":

```javascript
// Instead of direct DB calls in services
const users = await db.query('SELECT * FROM users');

// They use a repository
const users = await userRepository.findAll();
```

**Why it exists:** Makes testing easier, abstracts the database.

---

### DTO (Data Transfer Object)

Separate objects for API input/output vs database models:

```javascript
// What the API receives
const CreateUserDTO = { email, password, name };

// What's stored in DB
const UserEntity = { id, email, passwordHash, name, createdAt };

// What the API returns
const UserResponseDTO = { id, email, name };
```

**Why it exists:** Security (don't expose password hash), flexibility.

---

### Dependency Injection

Services receive their dependencies rather than creating them:

```javascript
// Instead of
class UserService {
  constructor() {
    this.db = new Database();  // Creates its own
  }
}

// They inject it
class UserService {
  constructor(database) {
    this.db = database;  // Receives it
  }
}
```

**Why it exists:** Testing, flexibility, configuration.

---

## What to Ignore Initially

- [ ] Authentication middleware (complex, tangential)
- [ ] Error handling middleware (important but not for understanding)
- [ ] Validation schemas (come back when you need them)
- [ ] Database migrations (historical, not current)
- [ ] Rate limiting, caching (infrastructure concerns)

---

## Quick Reference

| I want to find... | Look in... |
|-------------------|------------|
| What endpoints exist | `routes/` |
| What an endpoint does | `controllers/` |
| How the logic works | `services/` |
| What data looks like | `models/` |
| How auth works | `middleware/auth` |
| Database queries | `models/` or `repository/` |

---

## Next Steps

- Practice recognizing this pattern in [`exercises/beginner.md`](../exercises/beginner.md)
- See it applied in [`real-world-walkthroughs/small-api.md`](../real-world-walkthroughs/small-api.md)
