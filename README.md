# Project Routvi — Monorepo

A monorepo managed with [Turborepo](https://turbo.build/repo) and npm workspaces, containing multiple frontend applications and one backend service for the Routvi project.

---

## Repository Structure

```
project-routvi-monorepos/
├── apps/
│   ├── backend/      # NestJS API (TypeScript)
│   ├── client/       # React + Vite (TypeScript)
│   ├── admin/        # React + Vite (TypeScript)
│   ├── partner/      # React + Vite (TypeScript)
│   ├── web/          # React + Vite (TypeScript)
│   ├── commerce/     # Next.js + Tailwind CSS (TypeScript)
│   └── rh/           # Next.js + Tailwind CSS (TypeScript)
├── turbo.json
├── package.json
└── buildspec.yml
```

---

## Apps

### `backend` — NestJS API

A NestJS application bootstrapped with the NestJS CLI.

- **Framework:** NestJS v11
- **Runtime:** Node.js
- **Language:** TypeScript
- **Port:** `3000` (configurable via `PORT` environment variable)
- **Implemented endpoints:**
  - `GET /` — returns a "Hello World!" string

**Scripts:**

| Command | Description |
|---|---|
| `npm run start:dev` | Start in watch mode |
| `npm run build` | Build for production |
| `npm run start:prod` | Run production build |
| `npm test` | Run unit tests (Jest) |
| `npm run test:e2e` | Run end-to-end tests |

---

### `client`, `admin`, `partner`, `web` — React + Vite Apps

Four separate single-page applications, each scaffolded with [Vite](https://vitejs.dev/) using the React + TypeScript template.

- **Framework:** React v19
- **Bundler:** Vite v6 with `@vitejs/plugin-react-swc`
- **Language:** TypeScript
- **Current state:** Default Vite + React scaffold (no custom business logic implemented)

**Scripts (same for all four):**

| Command | Description |
|---|---|
| `npm run dev` | Start the local dev server |
| `npm run build` | Build for production |
| `npm run preview` | Preview the production build |
| `npm run lint` | Run ESLint |

---

### `commerce`, `rh` — Next.js Apps

Two applications scaffolded with [Next.js](https://nextjs.org/) using the App Router.

- **Framework:** Next.js v16
- **Styling:** Tailwind CSS v4
- **Language:** TypeScript
- **Current state:** Default Next.js App Router scaffold (no custom business logic implemented)

**Scripts (same for both):**

| Command | Description |
|---|---|
| `npm run dev` | Start the local dev server |
| `npm run build` | Build for production |
| `npm run start` | Start the production server |
| `npm run lint` | Run ESLint |

---

## Monorepo Tooling

- **Package manager:** npm v10.9.0
- **Task runner:** Turborepo v2.7.2

**Turborepo tasks defined in `turbo.json`:**

| Task | Behavior |
|---|---|
| `build` | Runs builds in dependency order, outputs `dist/**` and `.next/**` |
| `check-types` | Runs TypeScript type-checking in dependency order |
| `dev` | Runs all dev servers in parallel (persistent, no cache) |

To run a task across all apps:

```bash
npx turbo run dev
npx turbo run build
```

To run a task for a specific app:

```bash
npx turbo run build --filter=commerce
npx turbo run build --filter=rh
```

---

## CI/CD

The repository includes AWS CodeBuild `buildspec.yml` files.

### Root `buildspec.yml`

Runs on Node.js 20. Uses `git diff` to detect which apps changed in the latest commit. If changes are detected in `admin`, `client`, `partner`, or `web`, it:

1. Installs dependencies with `npm install`
2. Builds the app with `vite build --base=/<app-name>/`
3. Syncs the `dist/` output to an S3 bucket at `s3://project-r-monorepos-bucket/<app-name>`

### `apps/commerce/buildspec.yml` and `apps/rh/buildspec.yml`

Each runs on Node.js 20. Installs dependencies, then builds the target app using:

```bash
npx turbo run build --filter=<app-name>
```

Build artifacts are collected from `apps/<app-name>/out/` (static HTML export).

---

## Local Development Setup

**Prerequisites:**
- Node.js ≥ 20
- npm ≥ 10.9.0

**Install all dependencies:**

```bash
npm install
```

**Start all apps in development mode:**

```bash
npx turbo run dev
```

**Start a single app:**

```bash
cd apps/backend && npm run start:dev
cd apps/client && npm run dev
```
