# Next.js Starter Kit — Design Spec

**Date:** 2026-03-23
**Purpose:** Personal/side projects
**Status:** Approved

---

## Overview

A minimal, opinionated Next.js starter kit for personal and side projects. No auth, no database — just a clean, well-configured foundation with TypeScript, Tailwind v4, pnpm, and a solid developer experience setup.

---

## Project Structure

```
nextjs-starter-kit/
├── src/
│   ├── app/
│   │   ├── layout.tsx        # Root layout (fonts, metadata)
│   │   ├── page.tsx          # Home page
│   │   └── globals.css       # Tailwind v4 import + @theme block
│   ├── components/           # Shared UI components
│   ├── hooks/                # Custom React hooks
│   └── lib/                  # Shared utilities
├── public/                   # Static assets
├── .github/
│   └── dependabot.yml        # Weekly npm dependency updates
├── .husky/
│   ├── pre-commit            # Runs lint-staged
│   ├── commit-msg            # Runs commitlint
│   └── pre-push              # Runs type-check
├── .claude/
│   └── settings.json         # Claude Code allow/deny permissions
├── eslint.config.mjs         # ESLint 9 flat config
├── .env.example              # Documents required env vars (empty)
├── .nvmrc                    # Node version pin (Node 24)
├── .npmrc                    # pnpm config (shamefully-hoist=false)
├── .prettierrc
├── commitlint.config.mjs
├── lint-staged.config.mjs
├── postcss.config.mjs        # @tailwindcss/postcss plugin
├── tsconfig.json
├── next.config.ts
└── README.md
```

- Uses the **App Router** (Next.js 16)
- `src/` directory separates source from config
- No `tailwind.config.ts` — v4 uses CSS-based configuration

---

## TypeScript

Strict mode with additional safety flags:

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true
  }
}
```

- `noUncheckedIndexedAccess` — array/object index access returns `T | undefined`
- `exactOptionalPropertyTypes` — distinguishes missing properties from `undefined`

---

## Styling — Tailwind v4

- `@import "tailwindcss"` in `globals.css` (replaces v3 directives)
- Custom design tokens defined via `@theme` block in CSS
- Content detection is automatic — no path configuration needed (v4 scans automatically)
- PostCSS integration via `@tailwindcss/postcss`

```css
/* src/app/globals.css */
@import 'tailwindcss';

@theme {
  /* custom tokens, e.g. --color-brand: #6366f1; */
}
```

```js
// postcss.config.mjs
const config = {
  plugins: {
    '@tailwindcss/postcss': {},
  },
}
export default config
```

---

## Linting & Formatting

**ESLint:**

- `eslint-config-next` — Next.js recommended rules
- `eslint-config-prettier` — disables rules that conflict with Prettier

**Prettier:**

```json
{
  "semi": false,
  "singleQuote": true,
  "trailingComma": "all",
  "tabWidth": 2,
  "useTabs": false,
  "printWidth": 80
}
```

**lint-staged** (runs on staged files only):

- `*.{ts,tsx}` → ESLint --fix (first), then Prettier (format last)
- `*.{json,css,md}` → Prettier

**ESLint** ignores build artifacts via `globalIgnores` in `eslint.config.mjs`:

- `.next/`, `out/`, `build/`, `dist/`, `next-env.d.ts`

---

## Git Hooks — Husky

| Hook         | Trigger                      | Action                 |
| ------------ | ---------------------------- | ---------------------- |
| `pre-commit` | Before each commit           | Runs lint-staged       |
| `commit-msg` | After writing commit message | Runs commitlint        |
| `pre-push`   | Before `git push`            | Runs `pnpm type-check` |

---

## Commit Convention — commitlint

Uses `@commitlint/config-conventional` (Conventional Commits spec).

```js
// commitlint.config.mjs
const config = { extends: ['@commitlint/config-conventional'] }
export default config
```

Examples:

```
feat: add dark mode toggle
fix: correct layout shift on mobile
chore: update dependencies
docs: update README
```

---

## Package Manager — pnpm

Use **pnpm** as the package manager. Fast, disk-efficient, and battle-tested with the Next.js + Husky + lint-staged toolchain.

`.npmrc`:

```
shamefully-hoist=false
```

## Node Version

Minimum **Node 24**. Pin via `.nvmrc`:

```
24
```

---

## Scripts

```json
"scripts": {
  "dev": "next dev",
  "build": "next build",
  "start": "next start",
  "lint": "eslint .",
  "format": "prettier --write .",
  "type-check": "next typegen && tsc --noEmit",
  "prepare": "husky"
}
```

Notes:

- `dev` — Turbopack is the default bundler in Next.js 16, no flag needed
- `lint` — `next lint` was removed in Next.js 16; use `eslint .` directly
- `type-check` — runs `next typegen` first to generate route types before checking

---

## Deployment — Vercel

GitHub integration with automatic deploys. No `vercel.json`, no GitHub Actions.

| Trigger                     | Result                                         |
| --------------------------- | ---------------------------------------------- |
| Push to `main`              | Production deploy at `your-project.vercel.app` |
| Push to any other branch    | Preview deploy at a unique Vercel URL          |
| Pull request opened/updated | Preview URL posted as PR comment               |

**Repo additions:**

- `.env.example` — documents required env vars (empty values, committed)
- `.env.local` — local secrets (gitignored via `.env*` pattern + `!.env.example` negation)

**One-time Vercel dashboard setup:**

1. Push repo to GitHub
2. Go to vercel.com → **Add New Project** → import the GitHub repo
3. Vercel auto-detects Next.js — no build settings to change
4. Set **Production Branch** to `main`
5. Add env vars from `.env.example` under **Settings → Environment Variables**

---

## Dependency Updates — Dependabot

Weekly automated PRs for npm dependency updates via `.github/dependabot.yml`:

```yaml
version: 2
updates:
  - package-ecosystem: 'npm'
    directory: '/'
    schedule:
      interval: 'weekly'
    commit-message:
      prefix: 'chore'
      include: 'scope'
```

---

## Claude Code Settings

`.claude/settings.json` with curated allow/deny permissions:

- **Allow:** git operations (add, commit, restore, rm, reset, rebase), pnpm scripts (build, lint, exec, type-check, next, eslint)
- **Deny:** destructive commands (rm -rf, reset --hard, clean), package mutations (pnpm add/remove), git push, privilege escalation (sudo, chmod 777), npx/pnpm dlx, gh destructive ops (repo delete/archive, pr merge/close, issue delete, release delete, secret set/delete, workflow run, auth token, raw api)

---

## What's Excluded

- Authentication — none (add later as needed)
- Database / ORM — none
- Component library — none (clean slate)
- Testing setup — none
