# Next.js Starter Kit

A minimal, opinionated Next.js starter kit for personal and side projects.

## Stack

- **Next.js 16** (App Router)
- **TypeScript** (strict mode)
- **Tailwind CSS v4**
- **pnpm**
- **ESLint 9** + **Prettier**
- **Husky** + **lint-staged** + **commitlint** (Conventional Commits)

## Getting Started

```bash
pnpm install
pnpm dev
```

Open [http://localhost:3000](http://localhost:3000).

## Scripts

| Script            | Description                  |
| ----------------- | ---------------------------- |
| `pnpm dev`        | Start dev server             |
| `pnpm build`      | Production build             |
| `pnpm start`      | Serve production build       |
| `pnpm lint`       | Run ESLint                   |
| `pnpm format`     | Run Prettier on all files    |
| `pnpm type-check` | Run TypeScript type checking |

## Environment Variables

Copy `.env.example` to `.env.local` and fill in the values:

```bash
cp .env.example .env.local
```

`.env.local` is gitignored. Never commit real secrets.

## Deploying to Vercel

This project is configured for one-click deployment via Vercel's GitHub integration.

### First-time setup

1. Push your repo to GitHub
2. Go to [vercel.com](https://vercel.com) → **Add New Project** → import your repo
3. Vercel auto-detects Next.js — leave all build settings as default
4. Confirm **Production Branch** is set to `main`
5. Click **Deploy**

### Environment variables

Add your env vars in the Vercel dashboard:

**Project → Settings → Environment Variables**

Add each variable from `.env.example` with its real value. You can scope variables per environment (Production, Preview, Development).

### Deployment behaviour

| Trigger                     | Result                           |
| --------------------------- | -------------------------------- |
| Push to `main`              | Production deploy                |
| Push to any other branch    | Preview deploy at a unique URL   |
| Pull request opened/updated | Preview URL posted as PR comment |

## Commit Convention

This project uses [Conventional Commits](https://www.conventionalcommits.org/). The `commit-msg` hook will reject non-conforming messages.

```
feat: add dark mode
fix: correct layout shift on mobile
chore: update dependencies
docs: update README
```
