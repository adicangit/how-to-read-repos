# Walkthrough: Small API

An annotated walkthrough of reading a clean, well-structured backend API.

---

## The Repository

**Repository:** [HowardHinnworry/express-rest-api-boilerplate](https://github.com/hagopj13/node-express-boilerplate) (or similar Express boilerplate)

*Note: We'll use a generic Express API structure. Apply these principles to any similar repo.*

---

## What We're Demonstrating

- How to quickly understand a backend API
- What to read, what to skip
- Real decisions made during exploration

---

## Initial View

Opening the repository, here's what we see:

```
express-api/
├── src/
│   ├── config/
│   ├── controllers/
│   ├── middlewares/
│   ├── models/
│   ├── routes/
│   ├── services/
│   ├── utils/
│   ├── validations/
│   └── index.js
├── tests/
├── .env.example
├── .eslintrc.json
├── .gitignore
├── docker-compose.yml
├── Dockerfile
├── package.json
└── README.md
```

---

## Step 1: Purpose (2 minutes)

**What I did:** Opened README.md, read the first paragraph.

**What I learned:**
> "A boilerplate for building production-ready RESTful APIs using Node.js, Express, and MongoDB."

**My one-sentence summary:** This is a template for building Express APIs with MongoDB.

**What I ignored:** The rest of the README (installation, features list, configuration options). I'll come back if needed.

---

## Step 2: Project Type (1 minute)

**Observation:** I see `routes/`, `controllers/`, `services/`, `models/`.

**Conclusion:** This is a standard backend API with layered architecture.

**I recognized this pattern immediately.** If you don't, check [`patterns/backend.md`](../patterns/backend.md).

---

## Step 3: Entry Point (3 minutes)

**What I checked:** `package.json`

```json
{
  "main": "src/index.js",
  "scripts": {
    "start": "node src/index.js",
    "dev": "nodemon src/index.js"
  }
}
```

**Entry point:** `src/index.js`

**What I found in index.js:**

```javascript
const app = require('./app');
const config = require('./config/config');
const logger = require('./config/logger');
const mongoose = require('mongoose');

mongoose.connect(config.mongoose.url).then(() => {
  logger.info('Connected to MongoDB');
  app.listen(config.port, () => {
    logger.info(`Listening on port ${config.port}`);
  });
});
```

**What this tells me:**
- Connects to MongoDB
- Loads config from `./config/config`
- The actual Express app is in `./app.js`

**Next step:** Look at `app.js` to see the Express setup.

---

## Step 4: Trace One Feature (20 minutes)

### Choosing a Feature

I'll trace: **Get all users (GET /v1/users)**

Why this feature:
- Simple CRUD operation
- Touches all layers
- Not complicated by auth (initially)

### Finding the Route

**What I did:** Opened `src/routes/index.js`

```javascript
const express = require('express');
const userRoute = require('./user.route');
const authRoute = require('./auth.route');

const router = express.Router();

router.use('/users', userRoute);
router.use('/auth', authRoute);

module.exports = router;
```

**What I learned:** User routes are in `./user.route.js`

### Opening the User Routes

**File:** `src/routes/user.route.js`

```javascript
const express = require('express');
const auth = require('../middlewares/auth');
const validate = require('../middlewares/validate');
const userValidation = require('../validations/user.validation');
const userController = require('../controllers/user.controller');

const router = express.Router();

router
  .route('/')
  .get(auth('getUsers'), validate(userValidation.getUsers), userController.getUsers)
  .post(auth('manageUsers'), validate(userValidation.createUser), userController.createUser);

module.exports = router;
```

**What I learned:**
- `GET /users` → `userController.getUsers`
- Has auth middleware (requires 'getUsers' permission)
- Has validation middleware
- Controller is in `../controllers/user.controller`

**What I'm ignoring for now:**
- How auth works (I see it exists, that's enough)
- Validation details (can come back later)

### Following to the Controller

**File:** `src/controllers/user.controller.js`

```javascript
const userService = require('../services/user.service');
const catchAsync = require('../utils/catchAsync');

const getUsers = catchAsync(async (req, res) => {
  const filter = pick(req.query, ['name', 'role']);
  const options = pick(req.query, ['sortBy', 'limit', 'page']);
  const result = await userService.queryUsers(filter, options);
  res.send(result);
});

module.exports = {
  getUsers,
  createUser,
  // ...
};
```

**What I learned:**
- Controller extracts query params
- Calls `userService.queryUsers`
- Returns the result
- Uses `catchAsync` for error handling

**What I'm noting:** `pick` is a utility to extract specific keys. I don't need to read it.

### Following to the Service

**File:** `src/services/user.service.js`

```javascript
const User = require('../models/user.model');
const ApiError = require('../utils/ApiError');

const queryUsers = async (filter, options) => {
  const users = await User.paginate(filter, options);
  return users;
};

module.exports = {
  queryUsers,
  // ...
};
```

**What I learned:**
- Uses a User model with pagination
- Returns users from MongoDB
- Has custom error handling

### Peeking at the Model

**File:** `src/models/user.model.js`

```javascript
const mongoose = require('mongoose');
const bcrypt = require('bcryptjs');

const userSchema = mongoose.Schema({
  name: { type: String, required: true, trim: true },
  email: { type: String, required: true, unique: true, lowercase: true },
  password: { type: String, required: true, minlength: 8 },
  role: { type: String, enum: ['user', 'admin'], default: 'user' },
  isEmailVerified: { type: Boolean, default: false },
}, { timestamps: true });

// Plugin for pagination
userSchema.plugin(paginate);

module.exports = mongoose.model('User', userSchema);
```

**What I learned:**
- User has name, email, password, role
- Uses bcrypt for password hashing
- Has a pagination plugin

---

## My Trace Notes

Here's what I wrote during exploration:

```
GET /users flow:

1. Request hits routes/user.route.js
2. auth middleware checks 'getUsers' permission
3. validate middleware checks query params
4. userController.getUsers extracts filter/options
5. userService.queryUsers queries MongoDB
6. User.paginate returns paginated results
7. Response sent as JSON

Key files:
- routes/user.route.js (URL mapping)
- controllers/user.controller.js (request handling)
- services/user.service.js (business logic)
- models/user.model.js (data schema)

Questions for later:
- How does auth middleware work?
- How does pagination plugin work?
- What's catchAsync doing exactly?
```

---

## What I Intentionally Ignored

| File/Folder | Why I Skipped It |
|-------------|------------------|
| `config/` | Will check if I need config values |
| `middlewares/auth.js` | I know it checks permissions, details later |
| `validations/` | I know validation exists, that's enough |
| `utils/` | Helpers, read when needed |
| `tests/` | Useful later for examples |
| `.eslintrc.json` | Code style, not logic |
| `Dockerfile` | Deployment, not logic |
| `.env.example` | Will check for required env vars |

---

## What Remained Unclear

And that's okay:

1. **How does authentication work?**
   - I know it uses JWT (saw it mentioned in README)
   - I know the auth middleware checks roles/permissions
   - I'd need to trace auth specifically to understand fully

2. **How does the paginate plugin work?**
   - It's probably a mongoose plugin
   - I'd Google "mongoose paginate" if I needed details

3. **What's the error handling strategy?**
   - Saw `catchAsync` and `ApiError`
   - Would need to trace an error path to understand

---

## Time Spent

| Activity | Time |
|----------|------|
| README | 2 min |
| Identify structure | 1 min |
| Find entry point | 3 min |
| Trace getUsers | 15 min |
| Write notes | 5 min |
| **Total** | **~26 min** |

Under 30 minutes for a solid understanding of the core flow.

---

## What I Could Do Now

After this exploration, I could:

1. **Add a new endpoint** — I know where to add routes, controllers, services
2. **Modify the User model** — I know the schema structure
3. **Debug a user query issue** — I know the data flow
4. **Ask specific questions** — "How does the auth middleware verify JWTs?"

---

## Key Takeaways

1. **Layered architecture is predictable.** Route → Controller → Service → Model.

2. **Follow imports.** Each file tells you where to go next.

3. **Ignore infrastructure.** Config, docker, tests — skip on first pass.

4. **One feature is enough.** Understanding one flow reveals the pattern.

5. **Write notes as you go.** They're your map for later.

---

## Try It Yourself

Apply this same process to:
- [express-generator](https://github.com/expressjs/generator) — minimal Express structure
- [fastify-example](https://github.com/fastify/example) — Fastify's example app
- Any Express/Fastify/Koa API you encounter

Use the same steps:
1. Purpose (2 min)
2. Project type (1 min)
3. Entry point (3 min)
4. Trace one feature (20 min)
5. Notes (5 min)
