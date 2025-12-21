# Walkthrough: Messy Production Code

An annotated walkthrough of reading real, imperfect production code — the kind you'll actually encounter at work.

---

## The Reality

Tutorials show you clean code. Production shows you:
- Inconsistent patterns
- Legacy decisions nobody remembers
- Quick fixes that became permanent
- Multiple people's coding styles
- Half-finished refactors
- Comments that lie

This walkthrough teaches you to navigate that reality.

---

## What "Messy" Looks Like

Here's a typical production folder structure:

```
legacy-app/
├── src/
│   ├── components/
│   │   ├── Button.jsx
│   │   ├── Button2.jsx          # Why two buttons?
│   │   ├── ButtonNew.jsx        # Oh no.
│   │   ├── LegacyUserCard.js    # Different extension
│   │   └── UserCardRefactored.tsx
│   ├── pages/
│   ├── utils/
│   │   ├── helpers.js           # Vague name
│   │   ├── utils.js             # Even vaguer
│   │   ├── helpers2.js          # Pain.
│   │   └── oldHelpers.js        # Older pain.
│   ├── services/
│   ├── lib/                     # What's the difference from utils?
│   ├── old/                     # Should this be deleted?
│   └── App.jsx
├── legacy/                      # More old code
├── scripts/                     # Build scripts maybe?
└── package.json
```

Take a breath. This is normal.

---

## Mindset for Messy Code

### Accept These Truths

1. **You won't understand the history.** Why are there two button components? Probably a refactor that was never finished. You won't find out why.

2. **Patterns will be inconsistent.** Some files use classes, some use hooks. Some use Redux, some use local state. Deal with it.

3. **Names will lie.** `helpers.js` contains database queries. `utils.js` contains business logic. Trust the code, not the names.

4. **Documentation is outdated.** Comments from 2019 describe code that's changed five times since.

5. **Some code is dead.** Nobody knows which code. Probably don't touch it.

### Your Strategy

- **Follow the imports.** Ignore names, follow what's actually used.
- **Trace from the user.** Start from the UI, work backward.
- **Find the "hot paths."** What code runs frequently? That's maintained.
- **Don't try to refactor while reading.** Note issues, but keep moving.

---

## Step 1: Purpose (2 minutes)

In a messy codebase, the README might be:
- Nonexistent
- Outdated
- Written for a version from 3 years ago

**What to do instead:**

1. **Ask someone.** "What does this app do in one sentence?"
2. **Run the app.** Click around. See what it does.
3. **Check the routes/pages.** They tell you what features exist.

For this walkthrough, let's say it's: "An internal tool for managing customer support tickets."

---

## Step 2: Find What's Actually Used (5 minutes)

### Ignore the Folder Structure

Instead, find what's actually imported.

**Start from the entry point:**

```jsx
// App.jsx
import Dashboard from './pages/Dashboard';
import TicketList from './pages/TicketList';
import TicketDetail from './pages/TicketDetailNew';  // Note: "New"
import Settings from './pages/Settings';
import { LegacyHeader } from './components/Header';  // Note: "Legacy"
```

**Now you know:**
- These pages are used: Dashboard, TicketList, TicketDetailNew, Settings
- The header component is "legacy" (but still used)
- `TicketDetailNew` suggests there's an old version somewhere

### Make a Map of What's Alive

```
Actually used:
- pages/Dashboard.jsx ✓
- pages/TicketList.jsx ✓
- pages/TicketDetailNew.jsx ✓
- pages/Settings.jsx ✓
- components/Header.jsx ✓

Probably dead (not imported from App):
- pages/TicketDetail.jsx (replaced by TicketDetailNew?)
- pages/OldDashboard.jsx
- components/ButtonOld.jsx
```

---

## Step 3: Trace One Feature (25 minutes)

### Choose a Well-Traveled Path

Pick a feature that:
- Users use daily
- Was probably touched recently
- Isn't labeled "legacy" or "old"

I'll trace: **Creating a new support ticket**

### Finding the Starting Point

From the UI, I find a "New Ticket" button. I search:

```bash
grep -r "New Ticket" --include="*.jsx" --include="*.tsx"
```

Found in: `pages/TicketList.jsx`

### The First Obstacle: Inconsistent Patterns

**File:** `pages/TicketList.jsx`

```jsx
class TicketList extends Component {
  // Oh, it's a class component. The other pages used hooks.
  
  state = {
    tickets: [],
    loading: true,
    showModal: false,
  };
  
  async componentDidMount() {
    // Fetching pattern #1: Direct axios call
    const response = await axios.get('/api/tickets');
    this.setState({ tickets: response.data, loading: false });
  }
  
  handleCreateTicket = async (ticketData) => {
    // Fetching pattern #2: Using a service
    await ticketService.createTicket(ticketData);
    this.fetchTickets();  // Refetch after create
  };
  
  render() {
    return (
      <div>
        <Button onClick={() => this.setState({ showModal: true })}>
          New Ticket
        </Button>
        {this.state.showModal && (
          <TicketModal 
            onSubmit={this.handleCreateTicket}
            onClose={() => this.setState({ showModal: false })}
          />
        )}
        {/* ticket list rendering */}
      </div>
    );
  }
}
```

**What I notice:**
- Class component (other pages use hooks) — inconsistent
- Two fetching patterns in the same file — inconsistent
- But the code works and the flow is clear

**What I write in my notes:**
```
CreateTicket flow in pages/TicketList.jsx:
1. Button click → showModal: true
2. TicketModal appears
3. User submits → handleCreateTicket(data)
4. Calls ticketService.createTicket(data)
5. Refetches tickets

Note: Inconsistent patterns
- Class component (others use hooks)
- Direct axios AND service layer used in same file
```

### Following to the Service

**File:** `services/ticketService.js`

```javascript
// Some methods use axios directly
import axios from 'axios';
// Some use a custom wrapper
import { api } from '../lib/api';
// Some use fetch??
import { fetchWrapper } from '../utils/fetchHelpers';

export const ticketService = {
  async getTickets() {
    // Pattern 1
    return axios.get('/api/tickets').then(r => r.data);
  },
  
  async createTicket(data) {
    // Pattern 2 (different!)
    return api.post('/api/tickets', data);
  },
  
  async getTicketById(id) {
    // Pattern 3 (yet another!)
    const response = await fetchWrapper(`/api/tickets/${id}`);
    return response.json();
  },
  
  // This looks unused but I'm not sure
  async archiveTicket(id) {
    return axios.post(`/api/tickets/${id}/archive`);
  },
};
```

**What I write in my notes:**
```
services/ticketService.js:
- THREE different HTTP patterns in one file
- createTicket uses api.post (from lib/api)
- Going to follow lib/api to understand what gets sent
- archiveTicket might be dead code - no time to verify
```

### Finding the API Layer

**File:** `lib/api.js`

```javascript
import axios from 'axios';
import { getAuthToken } from '../utils/auth';

const api = axios.create({
  baseURL: process.env.REACT_APP_API_URL,
});

api.interceptors.request.use((config) => {
  const token = getAuthToken();
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Error handling that nobody documented
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Magic redirect
      window.location.href = '/login?expired=true';
    }
    throw error;
  }
);

export { api };
```

**What I understand now:**
- `api` is axios with auth token attached
- 401 errors redirect to login
- This is why `createTicket` uses `api.post` — needs auth

---

## Dealing with Mysteries

### Mystery 1: Multiple Versions of Things

```
components/
├── Button.jsx
├── Button2.jsx
├── ButtonNew.jsx
└── ButtonRefactored.tsx
```

**What to do:**
1. Search for imports: `grep -r "from.*Button" --include="*.jsx"`
2. See which is actually used in the pages you care about
3. Ignore the others (for now)

**Don't:**
- Try to understand why there are four
- Try to figure out which is "best"
- Try to consolidate them

### Mystery 2: Folders With Unclear Purposes

```
utils/
lib/
helpers/
common/
shared/
```

**What to do:**
1. When you need a utility, search for it: `grep -r "formatDate" src/`
2. Follow imports — the code will tell you where things are
3. Accept that the organization doesn't make sense

### Mystery 3: Comments That Lie

```javascript
// This function validates the email
function processUserData(user) {
  // Actually it does 47 things, none of which is email validation
  const result = transformUser(user);
  await saveToLegacySystem(result);
  await notifyAnalytics(result);
  return result;
}
```

**What to do:**
- Read the code, not the comments
- Trust function calls over descriptions
- If a comment is clearly wrong, you can update it (or delete it)

---

## My Notes From This Exploration

```
Create Ticket Flow:
==================
TicketList.jsx (class component)
  → Button opens TicketModal
  → Modal submit calls handleCreateTicket()
  → ticketService.createTicket(data)
  → api.post('/api/tickets', data)
  → Auth token attached via interceptor
  → Response returns, refetch ticket list

Key observations:
- Inconsistent: class + hooks in same codebase
- Inconsistent: 3 different HTTP approaches
- But: auth consistently handled in lib/api.js
- But: flow is understandable once traced

Known landmines:
- services/ticketService.js uses 3 patterns
- Don't refactor while fixing bugs
- Some components have multiple versions - check imports

Questions I'd ask team:
1. Why three Button components? Which should new code use?
2. Is ticketService.archiveTicket used anywhere?
3. What's the plan for class → hooks migration?
```

---

## Survival Strategies

### 1. The "Import Chain" Strategy

When you need to find something:
```
Your feature → Page that uses it → Component → Service → API
```

Follow the imports. Ignore everything else.

### 2. The "What's Actually Used" Strategy

```bash
# Find what's imported from a confusing directory
grep -r "from.*utils/" --include="*.jsx" | sort | uniq -c | sort -rn

# Shows you which utils are actually used and how often
```

### 3. The "Find the Hot Path" Strategy

Recent changes are usually the best code:
```bash
git log --oneline -20 --name-only
# See what files were recently modified
```

Recently touched files are more likely to:
- Follow current patterns
- Actually work
- Have context someone remembers

### 4. The "Ignore the Naming" Strategy

Names are often:
- From years ago
- Aspirational ("CleanUserCard")
- Wrong ("helpers" containing business logic)

Read the code. Trust the imports.

---

## What I Intentionally Ignored

| Pattern | Why I Ignored It |
|---------|------------------|
| Multiple Button versions | Not relevant to my feature |
| Folder organization | Confusing and inconsistent |
| Dead code in `/old` | Obvious legacy |
| 3 HTTP patterns | I found the one my feature uses |
| Comments | Often outdated |
| Class vs hooks inconsistency | Not my problem today |

---

## What to Do When You're Lost

1. **Find one working path.** Just one.
2. **Write it down.** Your notes are your lifeline.
3. **Ask someone.** "What's the intended pattern for X?"
4. **Look at recent PRs.** They show current practice.
5. **Run the tests.** They show how things are supposed to work.

---

## When to Stop

You've succeeded when:
- You can complete your task
- You understand one path through the code
- You have notes to reference later
- You know what you're ignoring (and why)

You don't need to:
- Understand everything
- Fix inconsistencies
- Refactor while reading
- Document the whole system

---

## The Hard Truth

Every production codebase is messy. The question isn't "how do I understand this perfectly?" — it's "how do I understand enough to do my job?"

That's the skill.

---

## Key Takeaways

1. **Follow imports, not folder structure.** The code tells the truth.
2. **Find what's actually used.** Ignore dead code.
3. **Accept inconsistency.** Multiple patterns will coexist.
4. **Take notes obsessively.** You'll forget.
5. **Don't refactor while reading.** Stay focused.
6. **Trust recent code over old comments.** Things change.
7. **Ask for help.** Someone knows the context.

---

## You're Not Alone

Every developer feels lost in production code.
Every senior engineer has said "what the hell is this?"
Every codebase has mysteries nobody can explain.

This is normal. The skill is moving forward anyway.
