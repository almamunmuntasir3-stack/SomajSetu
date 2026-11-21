# AGENTS.md

## Repository layout
- Root contains a separate `package.json`/lock and `node_modules` (`lucide-react`, `react-hot-toast`); the actual application lives in `shomajsetu/`.
- `shomajsetu/` is a Next.js app (App Router) with its own `package.json`/lock, `node_modules`, `.next/`, and `.gitignore`.
- Run app commands from `shomajsetu/` unless explicitly working on root dependencies.

## `shomajsetu/` app (verified)
- Stack: Next.js `16.4.6`, React `19.3.0`, JavaScript (`.js`/`.jsx`), Tailwind CSS `4.3.3`, ESLint 9 flat config. `tsconfig.json` still exists (strict, generates route types) but contains no app source.
- Scripts (`shomajsetu/package.json`): `npm run dev` (`next dev`), `npm run build` (`next build`), `npm run start` (`next start`), `npm run lint` (`eslint`). No test script is defined.
- App Router lives in `src/app/` (not `app/`); do not recreate a root-level `app/` directory — Next.js will silently prefer it over `src/app/` and serve the wrong pages.
- Entrypoints: `src/app/layout.jsx`, `src/app/page.jsx`, styles in `src/app/globals.css` (`@import "tailwindcss";`). App code is JavaScript-only (`.js`/`.jsx`); no `.ts`/`.tsx` files under `src/`.
- Path alias `@/*` -> `./*` (from `tsconfig.json`), so imports look like `@/src/components/Navbar`.
- `next.config.ts` allows remote images from `images.unsplash.com` via `images.remotePatterns`; external image hosts must be added there.
- `next.config.ts`: enables `experimental.agentFeedback`, `cacheComponents`, `partialPrefetching`, and configures Turbopack to load `*.css` via `@tailwindcss/turbopack`.
- ESLint config (`eslint.config.mjs`) extends `eslint-config-next/core-web-vitals` and `eslint-config-next/typescript`.
- shadcn/ui is configured JS-only via `components.json` (`tsx: false`); primitives live in `src/components/ui/`, `cn()` in `src/lib/utils.js`. Add more with `npx shadcn@latest add <name>`.
- Event routes are `/events` and `/events/[id]`; `/upcoming-events` only redirects to `/events`.
- `src/data/mockEvents.js` uses `eventDate` (not `date`); events also carry `categoryGroup` (All/Environment/Health/Education/Relief/Community), `organizer`, and `currentVolunteers`. `getUpcomingEvents(limit)` is cached: pass a limit for `slice(0, limit)`, omit it for all.
- Event listing search/category filtering is client-side in `EventsExplorer.jsx`; `events/loading.jsx` previews states with `EventSkeleton.jsx`. Join is demo-only local state in `EventJoinPanel.jsx`.

## Important quirks
- `shomajsetu/AGENTS.md` is managed by `next dev` (`node_modules/next/dist/server/lib/generate-agent-files.js`): it writes/restores `<!-- BEGIN/END:nextjs-agent-rules -->` and `<!-- BEGIN/END:nextjs-agent-feedback -->` blocks. Do not remove these managed blocks from diffs; follow `shomajsetu/AGENTS.md` instructions when working in that app.
- Next.js 16 APIs/conventions may differ from older training data; when coding in `shomajsetu/`, consult bundled docs in `shomajsetu/node_modules/next/dist/docs/` (per managed agent rules) before guessing.
- `shomajsetu/package.json` declares `lucide-react`, `next-themes`, and `sonner`; the root `package.json` separately declares `lucide-react` and `react-hot-toast` (`react-hot-toast` is not used by the app — Sonner's `<Toaster />` is wired in `src/app/layout.jsx`). Install app dependencies in `shomajsetu/`, not at the repo root.
- With `cacheComponents: true`, calling `new Date()`/`Date.now()` during render fails prerendering. Use a static value, `"use cache"` (as `getUpcomingEvents` does), or a client component instead.
- Dynamic routes that read runtime `params` need `export const instant = false` (see `src/app/events/[id]/page.jsx`); otherwise the Navbar's `usePathname()` blocks prerendering.
- Verify changes with `npm run lint` then `npm run build` from `shomajsetu/`; build catches prerender/config errors that lint does not.
- `.gitignore` in `shomajsetu/` ignores `.env*`, `.next/`, `node_modules/`, and `next-env.d.ts`.
