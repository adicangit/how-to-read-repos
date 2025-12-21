# Pattern: Frontend Applications

Learn to recognize frontend application structures instantly.

---

## The Standard Frontend Shape

Most frontend apps follow this structure:

```
project/
├── src/
│   ├── components/       # Reusable UI pieces
│   ├── pages/ (or views/)# Route-level components
│   ├── hooks/            # Custom React hooks
│   ├── utils/            # Helper functions
│   ├── services/         # API calls
│   ├── store/ (or state/)# State management
│   ├── types/            # TypeScript types
│   ├── styles/           # CSS/styling
│   └── App.tsx           # Root component
├── public/               # Static assets
├── package.json
└── README.md
```

---

## Folder by Folder

### `components/`

**What it does:** Contains reusable UI building blocks.

**What you'll find:**
```jsx
// components/Button.jsx
function Button({ onClick, children, variant }) {
  return (
    <button className={`btn btn-${variant}`} onClick={onClick}>
      {children}
    </button>
  );
}
```

**Key insight:** Components are usually organized by:
- **Type:** `Button/`, `Modal/`, `Card/`
- **Feature:** `auth/`, `dashboard/`, `settings/`
- **Atomic design:** `atoms/`, `molecules/`, `organisms/`

---

### `pages/` (or `views/`, `screens/`)

**What it does:** Contains route-level components. Each page = one URL.

**What you'll find:**
```jsx
// pages/Dashboard.jsx
function Dashboard() {
  return (
    <Layout>
      <Sidebar />
      <MainContent>
        <StatsOverview />
        <RecentActivity />
      </MainContent>
    </Layout>
  );
}
```

**Why it matters:** This is your SITEMAP. Open this to see what the app can do.

---

### `hooks/`

**What it does:** Contains custom React hooks for reusable logic.

**What you'll find:**
```jsx
// hooks/useAuth.js
function useAuth() {
  const [user, setUser] = useState(null);
  const login = async (credentials) => { /* ... */ };
  const logout = () => { /* ... */ };
  return { user, login, logout };
}
```

**Why it matters:** Shows you how shared logic is organized.

---

### `services/` (or `api/`)

**What it does:** Contains API calls and external service interactions.

**What you'll find:**
```javascript
// services/api.js
export async function getUsers() {
  const response = await fetch('/api/users');
  return response.json();
}

export async function createUser(data) {
  return fetch('/api/users', {
    method: 'POST',
    body: JSON.stringify(data)
  });
}
```

**Why it matters:** This connects frontend to backend. Crucial for understanding data flow.

---

### `store/` (or `state/`, `redux/`, `context/`)

**What it does:** Manages global application state.

**What you'll find depends on the library:**

```javascript
// Redux
const userSlice = createSlice({
  name: 'user',
  initialState: { current: null, loading: false },
  reducers: {
    setUser: (state, action) => { state.current = action.payload }
  }
});

// Zustand
const useStore = create((set) => ({
  user: null,
  setUser: (user) => set({ user })
}));

// Context
const AuthContext = createContext();
```

**Why it matters:** Understanding state = understanding data flow.

---

## Framework-Specific Patterns

### React (Create React App / Vite)

```
src/
├── App.jsx           # Root component
├── index.jsx         # Entry: renders App into DOM
├── components/
├── pages/
└── hooks/
```

Entry point: `index.jsx` → `App.jsx` → Router → Pages

---

### Next.js

```
├── app/              # App Router (Next.js 13+)
│   ├── layout.tsx    # Root layout
│   ├── page.tsx      # Home page (/)
│   └── dashboard/
│       └── page.tsx  # /dashboard
├── components/
└── lib/              # Utilities
```

Or with Pages Router:
```
├── pages/
│   ├── _app.tsx      # App wrapper
│   ├── index.tsx     # Home page
│   └── dashboard.tsx # /dashboard
```

Entry point: Folder structure IS the routing. `pages/about.tsx` = `/about`

---

### Vue.js

```
src/
├── App.vue           # Root component
├── main.js           # Entry: creates Vue instance
├── components/
├── views/            # Route-level components
├── router/           # Vue Router config
└── store/            # Vuex/Pinia
```

Entry point: `main.js` → `App.vue` → `<router-view>`

---

### Angular

```
src/app/
├── app.module.ts     # Root module
├── app.component.ts  # Root component
├── app-routing.module.ts
├── components/
├── services/
└── models/
```

Entry point: `main.ts` → `app.module.ts` → `app.component.ts`

---

## How to Read a Frontend Repo

### Step 1: Find the Page You Care About

Start in `pages/` or `views/`. Find the page for the feature you're investigating.

### Step 2: Identify the Components Used

Look at what the page imports and renders:

```jsx
function Dashboard() {
  return (
    <Layout>              // Layout component
      <StatsCard />       // Find this in components/
      <UserList />        // Find this in components/
    </Layout>
  );
}
```

### Step 3: Trace Data Flow

For each component, ask:
1. Where does its data come from? (props, hooks, store, API)
2. What user actions does it handle?
3. What side effects does it trigger?

### Step 4: Find the API Calls

Search for `fetch`, `axios`, or look in `services/`:

```javascript
// This tells you what backend endpoints the feature uses
const users = await getUsers();  // GET /api/users
```

---

## Common Patterns to Recognize

### Container/Presentational (Smart/Dumb)

```jsx
// Container (smart): handles logic
function UserListContainer() {
  const [users, setUsers] = useState([]);
  useEffect(() => {
    fetchUsers().then(setUsers);
  }, []);
  return <UserList users={users} />;
}

// Presentational (dumb): just renders
function UserList({ users }) {
  return users.map(u => <div>{u.name}</div>);
}
```

**Why it exists:** Separation of concerns, testability.

---

### Compound Components

```jsx
// Used together
<Select>
  <Select.Trigger>Choose...</Select.Trigger>
  <Select.Options>
    <Select.Option value="a">Option A</Select.Option>
    <Select.Option value="b">Option B</Select.Option>
  </Select.Options>
</Select>
```

**Why it exists:** Flexible, composable component APIs.

---

### Render Props / Children as Function

```jsx
<DataFetcher url="/api/users">
  {({ data, loading }) => 
    loading ? <Spinner /> : <UserList users={data} />
  }
</DataFetcher>
```

**Why it exists:** Sharing logic between components (older pattern).

---

### Higher-Order Components (HOC)

```jsx
// Wraps a component to add functionality
const EnhancedComponent = withAuth(MyComponent);
```

**Why it exists:** Adding behavior to components (older pattern, mostly replaced by hooks).

---

## What to Ignore Initially

- [ ] Styling files (CSS, SCSS, styled-components)
- [ ] Animation logic
- [ ] Error boundaries
- [ ] Loading states (initially)
- [ ] Accessibility attributes
- [ ] SEO components (Head, meta tags)
- [ ] Analytics tracking

These matter, but not for initial understanding.

---

## Key Files to Find First

| I want to understand... | Look in... |
|------------------------|------------|
| App structure | `App.jsx` or root component |
| Available pages | `pages/` or `views/` or router config |
| How data is fetched | `services/` or `api/` |
| Shared state | `store/` or context files |
| Reusable UI | `components/` |
| Custom logic | `hooks/` |

---

## Reading Component Files

When you open a component file:

```jsx
// 1. Check the imports - what does it depend on?
import { useState } from 'react';
import { useAuth } from '../hooks/useAuth';
import { Button } from '../components/Button';

// 2. Check the props - what does it receive?
function UserCard({ user, onDelete }) {

// 3. Look at hooks - what state/effects exist?
  const [isEditing, setIsEditing] = useState(false);
  const { currentUser } = useAuth();

// 4. Skim the return - what does it render?
  return (
    <div>
      <h2>{user.name}</h2>
      {currentUser.id === user.id && (
        <Button onClick={() => onDelete(user.id)}>Delete</Button>
      )}
    </div>
  );
}
```

---

## Next Steps

- Practice with [`exercises/beginner.md`](../exercises/beginner.md)
- See it applied in [`real-world-walkthroughs/frontend-app.md`](../real-world-walkthroughs/frontend-app.md)
