# Pattern: Monorepos

Monorepos contain multiple projects in one repository. They look overwhelming but follow predictable patterns.

---

## What Is a Monorepo?

A monorepo contains multiple related projects (packages, apps, services) in a single repository.

Instead of:
```
github.com/company/frontend
github.com/company/backend
github.com/company/shared-utils
```

You have:
```
github.com/company/platform
├── apps/
│   ├── frontend/
│   └── backend/
└── packages/
    └── shared-utils/
```

---

## Why Monorepos Feel Overwhelming

1. **Size:** More files, more folders, more everything
2. **Unfamiliarity:** Non-standard root structure
3. **Dependencies:** Packages depend on each other
4. **Tooling:** Extra configuration for managing multiple packages

The good news: **Each package inside is just a normal project.**

---

## Common Monorepo Structures

### Structure 1: apps/ + packages/

```
monorepo/
├── apps/
│   ├── web/              # Frontend application
│   ├── api/              # Backend application
│   └── admin/            # Admin dashboard
├── packages/
│   ├── ui/               # Shared UI components
│   ├── utils/            # Shared utilities
│   └── types/            # Shared TypeScript types
├── package.json          # Root package.json
├── turbo.json           # Turborepo config
└── pnpm-workspace.yaml  # Workspace config
```

**Pattern:** `apps/` = runnable applications, `packages/` = shared libraries.

---

### Structure 2: packages/ only

```
monorepo/
├── packages/
│   ├── core/
│   ├── react/
│   ├── vue/
│   └── shared/
├── package.json
└── lerna.json
```

**Pattern:** Common for library ecosystems (like Babel, React).

---

### Structure 3: services/

```
monorepo/
├── services/
│   ├── user-service/
│   ├── order-service/
│   └── notification-service/
├── libs/
│   └── common/
└── docker-compose.yml
```

**Pattern:** Common for microservices architectures.

---

## How to Navigate a Monorepo

### Step 1: Identify What's at the Root

The root `package.json` typically only has:
- Dev dependencies for the whole repo
- Workspace configuration
- Scripts to run commands across packages

```json
{
  "name": "monorepo",
  "private": true,
  "workspaces": ["apps/*", "packages/*"],
  "scripts": {
    "build": "turbo run build",
    "dev": "turbo run dev --parallel"
  }
}
```

**Key insight:** Root package.json is for orchestration, not code.

---

### Step 2: Find the Workspaces

Look for workspace configuration:

**pnpm:**
```yaml
# pnpm-workspace.yaml
packages:
  - 'apps/*'
  - 'packages/*'
```

**npm/yarn:**
```json
// package.json
{
  "workspaces": ["apps/*", "packages/*"]
}
```

**Lerna:**
```json
// lerna.json
{
  "packages": ["packages/*"]
}
```

This tells you where the actual code lives.

---

### Step 3: Pick ONE Package

Don't try to understand the whole monorepo.

1. Find the package relevant to your task
2. Navigate to that package's folder
3. Treat it like a standalone project
4. Read it using the appropriate pattern (backend, frontend, library)

```
# You're working on the frontend?
cd apps/web
# Now read it like any frontend app
```

---

### Step 4: Understand Internal Dependencies

Packages in a monorepo often depend on each other.

Check the package's `package.json`:

```json
{
  "name": "@company/web",
  "dependencies": {
    "@company/ui": "workspace:*",     // Internal package!
    "@company/utils": "workspace:*",  // Internal package!
    "react": "^18.0.0"                // External package
  }
}
```

The `workspace:*` or `*` version tells you it's an internal dependency.

---

## Monorepo Tooling You'll See

### Turborepo

```json
// turbo.json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"]
    },
    "dev": {
      "cache": false
    }
  }
}
```

**What it does:** Runs commands across packages efficiently, with caching.

**For reading code:** You can mostly ignore this.

---

### Lerna

```json
// lerna.json
{
  "version": "independent",
  "packages": ["packages/*"]
}
```

**What it does:** Manages versioning and publishing of packages.

**For reading code:** You can ignore this.

---

### Nx

```json
// nx.json
{
  "tasksRunnerOptions": { ... },
  "targetDefaults": { ... }
}
```

**What it does:** Build system with computation caching.

**For reading code:** You can ignore this.

---

### Changesets

```
.changeset/
├── config.json
└── feature-update.md
```

**What it does:** Manages changelogs and releases.

**For reading code:** Completely ignore.

---

## Finding Your Way Around

### "Where is the code for X feature?"

1. Is it an app or shared code?
   - App → Look in `apps/`
   - Shared → Look in `packages/`

2. Search for the feature name:
   ```bash
   grep -r "FeatureName" --include="*.tsx" apps/ packages/
   ```

3. Check imports to trace dependencies:
   ```typescript
   import { Button } from '@company/ui';  // → packages/ui
   import { api } from '@company/api-client';  // → packages/api-client
   ```

---

### "How do these packages connect?"

Draw a dependency graph:

```
apps/web
  ├── @company/ui (packages/ui)
  ├── @company/utils (packages/utils)
  └── @company/types (packages/types)

apps/api
  ├── @company/utils (packages/utils)
  └── @company/types (packages/types)

packages/ui
  └── @company/types (packages/types)
```

Start from your app, follow the imports.

---

### "Where do I make a change?"

1. **App-specific change:** Make it in the app
2. **Shared UI change:** Make it in the UI package
3. **Shared logic change:** Make it in the utils package
4. **Type change:** Make it in the types package

Changes in packages affect all apps that depend on them.

---

## Each Package Is Just a Regular Project

Once you're inside a package:

```
apps/web/                  # This is just a frontend app!
├── src/
│   ├── components/
│   ├── pages/
│   └── App.tsx
├── package.json
└── tsconfig.json
```

Use the appropriate pattern:
- [`patterns/frontend.md`](frontend.md) for frontend apps
- [`patterns/backend.md`](backend.md) for backend services
- [`patterns/libraries.md`](libraries.md) for shared packages

---

## What to Ignore at the Root

These are for monorepo management, not code understanding:

- [ ] `turbo.json`, `nx.json`, `lerna.json`
- [ ] `.changeset/`
- [ ] Root `package.json` (mostly)
- [ ] Root `tsconfig.json` (just base config)
- [ ] Docker files (for deployment)
- [ ] CI/CD configuration
- [ ] Root scripts

---

## Quick Reference

| I want to find... | Look in... |
|-------------------|------------|
| Main frontend app | `apps/web` or `apps/frontend` |
| Main backend app | `apps/api` or `apps/backend` or `services/` |
| Shared components | `packages/ui` or `packages/components` |
| Shared utilities | `packages/utils` or `packages/common` |
| Type definitions | `packages/types` or `packages/shared` |
| Configuration | `packages/config` |

---

## Common Naming Conventions

| Package Name | Usually Contains |
|--------------|------------------|
| `ui`, `components` | Shared React/Vue components |
| `utils`, `helpers`, `common` | Shared utility functions |
| `types`, `shared-types` | TypeScript type definitions |
| `config`, `eslint-config`, `tsconfig` | Shared configuration |
| `api-client`, `sdk` | API client code |
| `db`, `database`, `prisma` | Database utilities |

---

## Reading a Monorepo: Summary

1. **Don't try to understand everything** — pick ONE package
2. **Check the workspace config** — know what packages exist
3. **Navigate to your package** — treat it as a standalone project
4. **Trace internal imports** — understand what it depends on
5. **Ignore root config** — it's orchestration, not code

Monorepos are just multiple normal projects. One at a time.

---

## Next Steps

- Practice navigating a monorepo in [`exercises/advanced.md`](../exercises/advanced.md)
