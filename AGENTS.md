<!-- BEGIN:nextjs-agent-rules -->

# This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` before writing any code. Heed deprecation notices.

## Key Next.js 16 differences

- `next lint` is removed — use `eslint .` directly
- Turbopack is the default bundler — no `--turbopack` flag needed on `dev`
- `next typegen` must run before `tsc` to generate route types — use `pnpm type-check`

## Stack

- **Next.js 16** (App Router, `src/` directory)
- **TypeScript** strict mode + `noUncheckedIndexedAccess` + `exactOptionalPropertyTypes`
- **Tailwind CSS v4** — CSS-based config, no `tailwind.config.ts`. Import via `@import "tailwindcss"` in `globals.css`, tokens in `@theme {}`
- **pnpm** (not npm or yarn). Node 24 pinned via `.nvmrc`
- **ESLint 9** flat config in `eslint.config.mjs` — not `.eslintrc.json`
- **Prettier** — config in `.prettierrc`

## Scripts

```bash
pnpm dev          # start dev server (Turbopack default)
pnpm build        # production build
pnpm lint         # eslint .
pnpm format       # prettier --write .
pnpm type-check   # next typegen && tsc --noEmit
```

## Git hooks (Husky)

- `pre-commit` — runs lint-staged (ESLint --fix + Prettier on staged files)
- `commit-msg` — runs commitlint (Conventional Commits required)
- `pre-push` — runs `pnpm type-check`

Commit messages must follow Conventional Commits: `feat:`, `fix:`, `chore:`, `docs:`, etc.

## Directory structure

```
src/
├── app/          # Next.js App Router pages and layouts
├── components/   # shared UI components
├── hooks/        # custom React hooks
└── lib/          # shared utilities
```

<!-- END:nextjs-agent-rules -->
