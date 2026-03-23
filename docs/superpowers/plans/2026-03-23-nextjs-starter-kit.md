# Next.js Starter Kit Implementation Plan

> **Status: Complete.** This plan documents how the starter kit was built. Use it as a reference if recreating from scratch.

**Goal:** Scaffold a minimal, opinionated Next.js starter kit for personal/side projects with TypeScript strict mode, Tailwind v4, pnpm, ESLint, Prettier, Husky, lint-staged, and commitlint.

**Architecture:** Bootstrap with `create-next-app` (without Tailwind, to avoid v3 being installed), then manually layer in Tailwind v4, strict TypeScript config, Prettier, lint-staged, and Husky hooks. No auth, no DB, no component library.

**Tech Stack:** Next.js 16 (App Router), TypeScript 5, Tailwind CSS v4, pnpm, ESLint 9 (flat config), Prettier, Husky, lint-staged, commitlint

---

## File Map

| File                      | Action | Responsibility                                            |
| ------------------------- | ------ | --------------------------------------------------------- |
| `package.json`            | modify | scripts, dependencies                                     |
| `tsconfig.json`           | modify | strict TS flags                                           |
| `src/app/globals.css`     | modify | Tailwind v4 import + @theme                               |
| `src/app/layout.tsx`      | modify | clean root layout                                         |
| `src/app/page.tsx`        | modify | minimal home page                                         |
| `postcss.config.mjs`      | create | Tailwind v4 PostCSS plugin                                |
| `.prettierrc`             | create | Prettier config                                           |
| `eslint.config.mjs`       | modify | add prettier config + ignorePatterns (flat config format) |
| `lint-staged.config.mjs`  | create | staged file lint/format pipeline                          |
| `commitlint.config.mjs`   | create | conventional commits config                               |
| `.husky/pre-commit`       | create | runs lint-staged                                          |
| `.husky/commit-msg`       | create | runs commitlint                                           |
| `.husky/pre-push`         | create | runs type-check                                           |
| `.nvmrc`                  | create | Node 24 pin                                               |
| `.npmrc`                  | create | pnpm config                                               |
| `src/components/.gitkeep` | create | placeholder                                               |
| `src/hooks/.gitkeep`      | create | placeholder                                               |
| `src/lib/.gitkeep`        | create | placeholder                                               |
| `.env.example`            | create | documents required env vars (empty)                       |
| `.gitignore`              | modify | add `!.env.example` negation                              |
| `.github/dependabot.yml`  | create | weekly npm dependency updates                             |
| `.claude/settings.json`   | create | Claude Code allow/deny permissions                        |

---

## Task 1: Bootstrap with create-next-app

**Files:**

- Create: all scaffolded files

- [x] **Step 1: Scaffold the project**

Run from the **parent directory** of where you want the project created:

```bash
pnpm create next-app nextjs-starter-kit \
  --typescript \
  --eslint \
  --no-tailwind \
  --src-dir \
  --app \
  --import-alias "@/*" \
  --no-git
```

> `--no-tailwind` — we install v4 manually in the next task
> `--no-git` — we'll init git ourselves after setup

- [x] **Step 2: Verify scaffold**

```bash
cd nextjs-starter-kit
ls src/app
```

Expected output includes: `layout.tsx  page.tsx  globals.css`

- [x] **Step 3: Verify dev server starts**

```bash
pnpm dev
```

Open `http://localhost:3000` — should show the default Next.js welcome page. Stop with `Ctrl+C`.

- [x] **Step 4: Commit**

```bash
git init
git add .
git commit -m "chore: initial next.js scaffold"
```

---

## Task 2: Add pnpm config and Node version pin

**Files:**

- Create: `.npmrc`
- Create: `.nvmrc`

- [x] **Step 1: Create `.npmrc`**

```
shamefully-hoist=false
```

- [x] **Step 2: Create `.nvmrc`**

```
24
```

- [x] **Step 3: Commit**

```bash
git add .npmrc .nvmrc
git commit -m "chore: add pnpm config and pin node to 24"
```

---

## Task 3: Configure TypeScript strict mode

**Files:**

- Modify: `tsconfig.json`

- [x] **Step 1: Open `tsconfig.json` and update `compilerOptions`**

Add the two extra flags inside the existing `compilerOptions` object:

```json
"noUncheckedIndexedAccess": true,
"exactOptionalPropertyTypes": true
```

The `strict` flag is already set by `create-next-app`. The result should include:

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    ...
  }
}
```

- [x] **Step 2: Verify TypeScript compiles**

```bash
pnpm type-check
```

- [x] **Step 3: Commit**

```bash
git add tsconfig.json
git commit -m "chore: enable strict typescript flags"
```

---

## Task 4: Install and configure Tailwind v4

**Files:**

- Create: `postcss.config.mjs`
- Modify: `src/app/globals.css`

- [x] **Step 1: Install Tailwind v4 packages**

```bash
pnpm add -D tailwindcss @tailwindcss/postcss
```

- [x] **Step 2: Create `postcss.config.mjs`**

```js
const config = {
  plugins: {
    '@tailwindcss/postcss': {},
  },
}
export default config
```

> If `create-next-app` generated a `postcss.config.js` or `postcss.config.mjs`, replace its contents entirely.

- [x] **Step 3: Replace `src/app/globals.css`**

```css
@import 'tailwindcss';

@theme {
  /* Add custom design tokens here, e.g.: */
  /* --color-brand: #6366f1; */
}
```

- [x] **Step 4: Verify Tailwind works**

```bash
pnpm dev
```

In `src/app/page.tsx`, temporarily add a Tailwind class (e.g. `className="text-red-500"`) and verify the color appears at `http://localhost:3000`. Revert the change. Stop with `Ctrl+C`.

- [x] **Step 5: Commit**

```bash
git add postcss.config.mjs src/app/globals.css
git commit -m "chore: install and configure tailwind v4"
```

---

## Task 5: Configure Prettier

**Files:**

- Create: `.prettierrc`

- [x] **Step 1: Install Prettier**

```bash
pnpm add -D prettier
```

- [x] **Step 2: Create `.prettierrc`**

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

- [x] **Step 3: Verify Prettier runs**

```bash
pnpm exec prettier --check .
```

Fix any formatting issues:

```bash
pnpm exec prettier --write .
```

- [x] **Step 4: Commit**

```bash
git add .prettierrc package.json pnpm-lock.yaml
git commit -m "chore: add prettier config"
```

---

## Task 6: Configure ESLint with Prettier

**Files:**

- Modify: `eslint.config.mjs`

> Note: The scaffold generated `eslint.config.mjs` (ESLint 9 flat config format), not `.eslintrc.json`.
> The flat config format is used throughout this task.

- [x] **Step 1: Install eslint-config-prettier**

```bash
pnpm add -D eslint-config-prettier
```

- [x] **Step 2: Update `eslint.config.mjs`**

Replace the contents with:

```js
import { defineConfig, globalIgnores } from 'eslint/config'
import nextVitals from 'eslint-config-next/core-web-vitals'
import nextTs from 'eslint-config-next/typescript'
import prettier from 'eslint-config-prettier'

const eslintConfig = defineConfig([
  ...nextVitals,
  ...nextTs,
  prettier,
  globalIgnores(['.next/**', 'out/**', 'build/**', 'dist/**', 'next-env.d.ts']),
])

export default eslintConfig
```

> `prettier` must be **after** the Next.js configs so it overrides any formatting rules.
> `globalIgnores` covers build artifacts including `dist/`.

- [x] **Step 3: Verify ESLint runs cleanly**

```bash
pnpm lint
```

Expected: no errors.

- [x] **Step 4: Commit**

```bash
git add eslint.config.mjs package.json pnpm-lock.yaml
git commit -m "chore: configure eslint with prettier compat"
```

---

## Task 7: Configure lint-staged

**Files:**

- Create: `lint-staged.config.mjs`

- [x] **Step 1: Install lint-staged**

```bash
pnpm add -D lint-staged
```

- [x] **Step 2: Create `lint-staged.config.mjs`**

```js
const config = {
  '*.{ts,tsx}': ['eslint --fix', 'prettier --write'],
  '*.{json,css,md}': ['prettier --write'],
}
export default config
```

- [x] **Step 3: Verify lint-staged runs**

Stage a file and run manually:

```bash
git add src/app/page.tsx
pnpm exec lint-staged
```

Expected: runs ESLint and Prettier on the staged file with no errors.

- [x] **Step 4: Commit**

```bash
git add lint-staged.config.mjs package.json pnpm-lock.yaml
git commit -m "chore: configure lint-staged"
```

---

## Task 8: Configure Husky hooks

**Files:**

- Create: `.husky/pre-commit`
- Create: `.husky/commit-msg`
- Create: `.husky/pre-push`

- [x] **Step 1: Install Husky and commitlint**

```bash
pnpm add -D husky @commitlint/cli @commitlint/config-conventional
```

- [x] **Step 2: Initialise Husky**

```bash
pnpm exec husky init
```

This creates `.husky/` and updates the `prepare` script in `package.json`.

- [x] **Step 3: Create `commitlint.config.mjs`**

```js
const config = { extends: ['@commitlint/config-conventional'] }
export default config
```

- [x] **Step 4: Write `.husky/pre-commit`**

Replace any existing content with:

```bash
pnpm exec lint-staged
```

- [x] **Step 5: Write `.husky/commit-msg`**

```bash
pnpm exec commitlint --edit "$1"
```

- [x] **Step 6: Write `.husky/pre-push`**

```bash
pnpm type-check
```

- [x] **Step 7: Verify Husky is wired up**

```bash
cat .husky/pre-commit
```

Expected output: `pnpm exec lint-staged`

- [x] **Step 8: Commit**

```bash
git add .husky commitlint.config.mjs package.json pnpm-lock.yaml
git commit -m "chore: configure husky hooks and commitlint"
```

---

## Task 9: Add package.json scripts

**Files:**

- Modify: `package.json`

- [x] **Step 1: Update the `scripts` section in `package.json`**

Merge/replace with:

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

> `prepare` should already exist from `husky init` — verify it's `"husky"`.
> `lint` — `next lint` was removed in Next.js 16; use `eslint .` directly.
> `type-check` — `next typegen` generates route types before the type check.
> `dev` — Turbopack is the default in Next.js 16, no flag needed.

- [x] **Step 2: Verify all scripts work**

```bash
pnpm type-check
```

Expected: exits with no errors.

```bash
pnpm lint
```

Expected: no errors.

```bash
pnpm format
```

Expected: formats all files cleanly.

- [x] **Step 3: Commit**

```bash
git add package.json
git commit -m "chore: add dev scripts"
```

---

## Task 10: Clean up boilerplate and create directory structure

**Files:**

- Modify: `src/app/layout.tsx`
- Modify: `src/app/page.tsx`
- Create: `src/components/.gitkeep`
- Create: `src/hooks/.gitkeep`
- Create: `src/lib/.gitkeep`

- [x] **Step 1: Replace `src/app/layout.tsx`**

```tsx
import type { Metadata } from 'next'
import './globals.css'

export const metadata: Metadata = {
  title: 'My App',
  description: 'Built with Next.js',
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  )
}
```

- [x] **Step 2: Replace `src/app/page.tsx`**

```tsx
export default function Home() {
  return (
    <main>
      <h1>Hello World</h1>
    </main>
  )
}
```

- [x] **Step 3: Create placeholder directories**

```bash
touch src/components/.gitkeep src/hooks/.gitkeep src/lib/.gitkeep
```

- [x] **Step 4: Verify dev server still works**

```bash
pnpm dev
```

Open `http://localhost:3000` — should show "Hello World". Stop with `Ctrl+C`.

- [x] **Step 5: Verify full build passes**

```bash
pnpm build
```

Expected: build completes with no errors.

- [x] **Step 6: Commit**

```bash
git add src/
git commit -m "chore: clean up boilerplate and scaffold directory structure"
```

---

## Task 11: Final verification

- [x] **Step 1: Test pre-commit hook**

Make a small edit to `src/app/page.tsx` (e.g. add a blank line), stage it, and commit:

```bash
git add src/app/page.tsx
git commit -m "chore: test pre-commit hook"
```

Expected: lint-staged runs ESLint and Prettier on the staged file, commit succeeds.

- [x] **Step 2: Test commitlint hook**

Try a bad commit message:

```bash
git commit --allow-empty -m "bad commit message"
```

Expected: commitlint rejects it with an error about commit message format.

Try a good commit message:

```bash
git commit --allow-empty -m "chore: test commitlint"
```

Expected: commit succeeds.

- [x] **Step 3: Test pre-push hook**

```bash
git push --dry-run
```

Expected: `pnpm type-check` runs and passes before the push is attempted (or rejected by remote — that's fine, the hook ran).

- [x] **Step 4: Run full type-check**

```bash
pnpm type-check
```

Expected: exits cleanly.

- [x] **Step 5: Run full build one final time**

```bash
pnpm build
```

Expected: build succeeds with no type errors or lint warnings.

---

## Task 12: Add .env.example for Vercel deployment

**Files:**

- Create: `.env.example`
- Modify: `.gitignore`

- [x] **Step 1: Create `.env.example`**

```bash
# Environment variables
# Copy this file to .env.local and fill in the values

# Example:
# DATABASE_URL=
# NEXT_PUBLIC_API_URL=
```

- [x] **Step 2: Add `!.env.example` negation to `.gitignore`**

`create-next-app` adds `.env*` which would also ignore `.env.example`. Add the negation:

```
!.env.example
```

- [x] **Step 3: Verify `.env.example` is trackable**

```bash
git check-ignore -v .env.example
```

Expected: no output (file is not ignored).

- [x] **Step 4: Commit**

```bash
git add .env.example .gitignore
git commit -m "feat: configure vercel deployment"
```

---

## Task 13: Add Dependabot

**Files:**

- Create: `.github/dependabot.yml`

- [x] **Step 1: Create `.github/dependabot.yml`**

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

- [x] **Step 2: Commit**

```bash
git add .github/dependabot.yml
git commit -m "chore: add dependabot for weekly package updates"
```

---

## Task 14: Configure Claude Code settings

**Files:**

- Create: `.claude/settings.json`

- [x] **Step 1: Create `.claude/settings.json`**

```json
{
  "permissions": {
    "allow": [
      "Bash(git add:*)",
      "Bash(git commit:*)",
      "Bash(git restore:*)",
      "Bash(git rm:*)",
      "Bash(git reset:*)",
      "Bash(git rebase:*)",
      "Bash(pnpm build:*)",
      "Bash(pnpm lint:*)",
      "Bash(pnpm exec:*)",
      "Bash(pnpm type-check:*)",
      "Bash(pnpm next:*)",
      "Bash(pnpm eslint:*)"
    ],
    "deny": [
      "Bash(rm -rf:*)",
      "Bash(rm -r:*)",
      "Bash(rm -f:*)",
      "Bash(pnpm add:*)",
      "Bash(pnpm remove:*)",
      "Bash(git push:*)",
      "Bash(git reset --hard:*)",
      "Bash(git clean:*)",
      "Bash(npx:*)",
      "Bash(pnpm dlx:*)",
      "Bash(chmod 777:*)",
      "Bash(sudo:*)",
      "Bash(gh repo delete:*)",
      "Bash(gh repo archive:*)",
      "Bash(gh pr merge:*)",
      "Bash(gh pr close:*)",
      "Bash(gh issue delete:*)",
      "Bash(gh release delete:*)",
      "Bash(gh secret set:*)",
      "Bash(gh secret delete:*)",
      "Bash(gh workflow run:*)",
      "Bash(gh auth token:*)",
      "Bash(gh api:*)"
    ]
  }
}
```

- [x] **Step 2: Commit**

```bash
git add .claude/settings.json
git commit -m "chore: update claude code settings"
```
