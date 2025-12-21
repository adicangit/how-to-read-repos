# Walkthrough: Frontend App

An annotated walkthrough of reading a React frontend application.

---

## The Repository

For this walkthrough, we'll examine a typical React application structure. The principles apply to any React app you encounter.

**Example structure we're exploring:**

```
react-app/
├── src/
│   ├── components/
│   ├── pages/
│   ├── hooks/
│   ├── services/
│   ├── store/
│   ├── utils/
│   ├── App.tsx
│   └── main.tsx
├── public/
├── package.json
└── vite.config.ts
```

---

## What We're Demonstrating

- How to navigate a frontend codebase quickly
- Tracing a user interaction through the code
- Understanding state and data flow

---

## Step 1: Purpose (2 minutes)

**What I did:** Read the README and package.json description.

**What I found:**
- It's a task management application
- Uses React + TypeScript
- Has authentication
- Uses React Query for data fetching

**My one-sentence summary:** A React app for managing tasks, with user authentication.

---

## Step 2: Project Type (1 minute)

**Observations:**
- `src/pages/` — route-level components
- `src/components/` — reusable UI
- `vite.config.ts` — Vite bundler
- `src/store/` — state management

**Conclusion:** Standard React SPA (Single Page Application).

---

## Step 3: Entry Point (5 minutes)

**What I checked:** `package.json` scripts

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build"
  }
}
```

**Entry:** Vite looks for `index.html` → which loads `src/main.tsx`

**What I found in main.tsx:**

```tsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';
import { QueryClientProvider } from '@tanstack/react-query';
import { AuthProvider } from './context/AuthContext';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <QueryClientProvider client={queryClient}>
      <BrowserRouter>
        <AuthProvider>
          <App />
        </AuthProvider>
      </BrowserRouter>
    </QueryClientProvider>
  </React.StrictMode>
);
```

**What this tells me:**
- Uses React Router for routing
- Uses React Query for server state
- Has an AuthProvider context
- Main app is in `App.tsx`

**Next step:** Look at `App.tsx`

### App.tsx

```tsx
import { Routes, Route } from 'react-router-dom';
import Layout from './components/Layout';
import Home from './pages/Home';
import Login from './pages/Login';
import Dashboard from './pages/Dashboard';
import TaskList from './pages/TaskList';
import TaskDetail from './pages/TaskDetail';

function App() {
  return (
    <Routes>
      <Route path="/" element={<Layout />}>
        <Route index element={<Home />} />
        <Route path="login" element={<Login />} />
        <Route path="dashboard" element={<Dashboard />} />
        <Route path="tasks" element={<TaskList />} />
        <Route path="tasks/:id" element={<TaskDetail />} />
      </Route>
    </Routes>
  );
}
```

**Now I have the sitemap:**
- `/` → Home
- `/login` → Login
- `/dashboard` → Dashboard
- `/tasks` → TaskList
- `/tasks/:id` → TaskDetail

---

## Step 4: Trace One Feature (25 minutes)

### Choosing a Feature

I'll trace: **Viewing the task list and marking a task complete**

Why this feature:
- Involves data fetching
- Has user interaction
- Shows state management
- Touches multiple components

### Finding the Task List Page

**File:** `src/pages/TaskList.tsx`

```tsx
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { getTasks, updateTask } from '../services/api';
import TaskCard from '../components/TaskCard';
import LoadingSpinner from '../components/LoadingSpinner';

function TaskList() {
  const queryClient = useQueryClient();
  
  const { data: tasks, isLoading } = useQuery({
    queryKey: ['tasks'],
    queryFn: getTasks,
  });

  const completeMutation = useMutation({
    mutationFn: (taskId: string) => updateTask(taskId, { completed: true }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['tasks'] });
    },
  });

  if (isLoading) return <LoadingSpinner />;

  return (
    <div className="task-list">
      <h1>My Tasks</h1>
      {tasks?.map((task) => (
        <TaskCard
          key={task.id}
          task={task}
          onComplete={() => completeMutation.mutate(task.id)}
        />
      ))}
    </div>
  );
}
```

**What I learned:**
- `useQuery` fetches tasks from `getTasks`
- `useMutation` handles completing tasks
- Invalidates query cache after completion (refetches)
- Renders `TaskCard` for each task

**Key files to explore:**
- `services/api.ts` — where `getTasks` and `updateTask` are defined
- `components/TaskCard.tsx` — how a task is displayed

### Following to the API Service

**File:** `src/services/api.ts`

```typescript
import axios from 'axios';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
});

// Add auth token to requests
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

export const getTasks = async (): Promise<Task[]> => {
  const { data } = await api.get('/tasks');
  return data;
};

export const updateTask = async (id: string, updates: Partial<Task>): Promise<Task> => {
  const { data } = await api.patch(`/tasks/${id}`, updates);
  return data;
};
```

**What I learned:**
- Uses axios with a base URL from environment
- Automatically adds auth token from localStorage
- `getTasks` → `GET /tasks`
- `updateTask` → `PATCH /tasks/:id`

### Looking at the TaskCard Component

**File:** `src/components/TaskCard.tsx`

```tsx
interface TaskCardProps {
  task: Task;
  onComplete: () => void;
}

function TaskCard({ task, onComplete }: TaskCardProps) {
  return (
    <div className={`task-card ${task.completed ? 'completed' : ''}`}>
      <h3>{task.title}</h3>
      <p>{task.description}</p>
      {!task.completed && (
        <button onClick={onComplete}>
          Mark Complete
        </button>
      )}
    </div>
  );
}
```

**What I learned:**
- Receives task and onComplete callback as props
- Displays title and description
- Shows complete button only if not completed
- Adds 'completed' class when done

---

## My Trace Notes

```
Task List Feature Flow:

1. TaskList page renders
2. useQuery calls getTasks() from api.ts
3. getTasks() makes GET /tasks with auth token
4. Tasks render as TaskCard components
5. User clicks "Mark Complete"
6. onComplete calls completeMutation.mutate(taskId)
7. Mutation calls updateTask() → PATCH /tasks/:id
8. onSuccess invalidates 'tasks' query
9. React Query refetches task list
10. UI updates with completed task

Key files:
- pages/TaskList.tsx (orchestrates feature)
- services/api.ts (API calls)
- components/TaskCard.tsx (display)

Patterns observed:
- Container/presenter: TaskList handles logic, TaskCard just renders
- React Query for server state
- Optimistic updates could be added
```

---

## Understanding State Management

### What I investigated:

**File:** `src/store/` or `src/context/`

In this app, I found:
- **Server state:** React Query (`useQuery`, `useMutation`)
- **Auth state:** Context (`AuthProvider`)
- **UI state:** Local component state (`useState`)

```tsx
// src/context/AuthContext.tsx
const AuthContext = createContext<AuthContextType | null>(null);

export function AuthProvider({ children }) {
  const [user, setUser] = useState<User | null>(null);
  
  const login = async (credentials) => {
    const { user, token } = await loginApi(credentials);
    localStorage.setItem('token', token);
    setUser(user);
  };
  
  const logout = () => {
    localStorage.removeItem('token');
    setUser(null);
  };
  
  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}
```

**What I learned:**
- User state is in React Context
- Token stored in localStorage
- Login/logout update both context and localStorage

---

## What I Intentionally Ignored

| File/Folder | Why I Skipped It |
|-------------|------------------|
| `components/Layout.tsx` | UI wrapper, not core logic |
| `components/LoadingSpinner.tsx` | Just UI |
| `utils/` | Helpers, read when needed |
| `hooks/` | Would check if I saw custom hooks used |
| `vite.config.ts` | Build config |
| `tailwind.config.js` | Styling config |
| `tsconfig.json` | TypeScript config |
| CSS files | Styling, not logic |

---

## What Remained Unclear

1. **How does protected routing work?**
   - Probably in Layout or a PrivateRoute component
   - Would trace if I needed to understand auth flow

2. **How are errors handled?**
   - React Query has error state
   - Would look for error boundaries

3. **How is form validation done?**
   - Would check login/create pages
   - Probably uses a form library

---

## Data Flow Diagram

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   TaskList  │────▶│   TaskCard  │     │             │
│   (page)    │     │ (component) │     │             │
└─────────────┘     └─────────────┘     │   Backend   │
       │                   │            │    API      │
       │ useQuery         │ onClick    │             │
       ▼                   ▼            │             │
┌─────────────┐     ┌─────────────┐     │             │
│   getTasks  │────▶│ updateTask  │────▶│             │
│   (api.ts)  │◀────│  (api.ts)   │◀────│             │
└─────────────┘     └─────────────┘     └─────────────┘
       │                   │
       │ invalidate        │
       ▼                   │
┌─────────────┐            │
│ React Query │◀───────────┘
│   Cache     │
└─────────────┘
```

---

## Time Spent

| Activity | Time |
|----------|------|
| README + package.json | 2 min |
| Entry point (main.tsx, App.tsx) | 5 min |
| TaskList page | 8 min |
| API service | 5 min |
| TaskCard component | 3 min |
| Auth context | 5 min |
| Notes | 5 min |
| **Total** | **~33 min** |

---

## What I Could Do Now

After this exploration, I could:

1. **Add a new feature** — I know the patterns for pages, components, and API calls
2. **Fix a bug in task completion** — I know the data flow
3. **Add a new API endpoint** — I know where API calls are defined
4. **Understand any other feature** — Follow the same pattern

---

## Key Patterns I Recognized

1. **Container/Presenter:** Pages handle logic, components just render
2. **Centralized API:** All API calls in one place with shared config
3. **React Query for server state:** No Redux needed for async data
4. **Context for auth:** Simple, app-wide auth state
5. **File organization:** Logical grouping by type (pages, components, services)

---

## Try It Yourself

Apply this process to:
- [Bulletproof React](https://github.com/alan2207/bulletproof-react) — well-structured React app
- [Next.js Commerce](https://github.com/vercel/commerce) — e-commerce starter
- Any React app you work with

Questions to answer:
1. Where are routes defined?
2. How is data fetched?
3. How is state managed?
4. What happens when a user clicks something?
