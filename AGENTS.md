# AGENTS.md

## Repository layout
- Root contains a separate `package.json`/lock and `node_modules` (`lucide-react`, `react-hot-toast`); the actual application lives in `shomajsetu/`.
- `shomajsetu/` is a Next.js app (App Router) with its own `package.json`/lock, `node_modules`, `.next/`, and `.gitignore`.
- Run app commands from `shomajsetu/` unless explicitly working on root dependencies.

## `shomajsetu/` app (verified)
- Stack: Next.js `16.4.6`, React `19.3.0`, TypeScript (strict), Tailwind CSS `4.3.3`, ESLint 9 flat config.
- Scripts (`shomajsetu/package.json`): `npm run dev` (`next dev`), `npm run build` (`next build`), `npm run start` (`next start`), `npm run lint` (`eslint`). No test script is defined.
- App Router entrypoints: `app/layout.tsx`, `app/page.tsx`, global styles in `app/globals.css` (`@import "tailwindcss";`).
- TS path alias: `@/*` -> `./*` (`tsconfig.json`).
- `next.config.ts`: enables `experimental.agentFeedback`, `cacheComponents`, `partialPrefetching`, and configures Turbopack to load `*.css` via `@tailwindcss/turbopack`.
- ESLint config (`eslint.config.mjs`) extends `eslint-config-next/core-web-vitals` and `eslint-config-next/typescript`.

## Important quirks
- `shomajsetu/AGENTS.md` is managed by `next dev` (`node_modules/next/dist/server/lib/generate-agent-files.js`): it writes/restores `<!-- BEGIN/END:nextjs-agent-rules -->` and `<!-- BEGIN/END:nextjs-agent-feedback -->` blocks. Do not remove these managed blocks from diffs; follow `shomajsetu/AGENTS.md` instructions when working in that app.
- Next.js 16 APIs/conventions may differ from older training data; when coding in `shomajsetu/`, consult bundled docs in `shomajsetu/node_modules/next/dist/docs/` (per managed agent rules) before guessing.
- `shomajsetu/package.json` does not currently declare `lucide-react` or `react-hot-toast` even though they exist at the repo root; verify/install dependencies in the correct package before using them in the app.
- `.gitignore` in `shomajsetu/` ignores `.env*`, `.next/`, `node_modules/`, and `next-env.d.ts`.
