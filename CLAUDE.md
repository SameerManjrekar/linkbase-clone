@AGENTS.md

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm run dev     # dev server on http://localhost:3000 (Turbopack is the default in Next 16)
npm run build   # production build — does NOT run lint
npm start       # serve the production build
npm run lint    # ESLint CLI (flat config); `next lint` no longer exists in Next 16
npx tsc --noEmit  # typecheck; the build also typechecks, nothing else does
```

No test runner is configured yet. If you add one, document the single-test invocation here.

## State of the project

A `create-next-app` scaffold (one commit, `Initial commit from Create Next App`) intended to become a Linktree clone. `src/app/page.tsx` is still the default template page and `layout.tsx` still carries the `Create Next App` metadata — replace both rather than building around them. There is no data layer, auth, or component directory yet, so decisions about those are still open.

## Stack and conventions

- **Next.js 16.3.5 App Router**, React 19.2, TypeScript `strict`. All code lives under [src/app/](src/app/); `@/*` resolves to `./src/*`.
- **Route props come from generated global types**, not hand-written interfaces — see `LayoutProps<"/">` in [src/app/layout.tsx](src/app/layout.tsx#L20). Use `PageProps<"/route">` / `LayoutProps<"/route">` for new routes; these regenerate from the filesystem when `next dev`/`next build` runs, so a new route's types only exist after one of those has run.
- **Tailwind v4, CSS-first.** There is no `tailwind.config.*` — design tokens are declared in the `@theme inline` block in [src/app/globals.css](src/app/globals.css) and Tailwind is wired in through `@tailwindcss/postcss` in [postcss.config.mjs](postcss.config.mjs). Add tokens to `globals.css`, not a JS config.
- **Gotcha in the scaffold:** `layout.tsx` loads Geist into `--font-geist-sans`/`--font-geist-mono` and maps them to `--font-sans`/`--font-mono`, but `globals.css` then hardcodes `body { font-family: Arial, Helvetica, sans-serif }`, which wins. Delete that rule (or switch it to `var(--font-sans)`) before assuming the font pipeline works.
- Dark mode is `prefers-color-scheme`-driven via the `--background`/`--foreground` vars in `globals.css`; there is no theme toggle or `data-theme` hook.

## Next 16 specifics worth checking before writing code

Per [AGENTS.md](AGENTS.md), read the relevant page under `node_modules/next/dist/docs/` first — this version differs from older Next.js in ways that break familiar code:

- `params`, `searchParams`, `cookies()`, `headers()`, `draftMode()` are **async** — await them.
- `middleware.ts` is now the `proxy` convention.
- `next/image`: `images.domains` is deprecated (use `remotePatterns`), and the `qualities`/`imageSizes`/`minimumCacheTTL` defaults changed — relevant once avatars and user-supplied link images land.
- Caching: `revalidateTag`/`updateTag`/`refresh`, `cacheLife`/`cacheTag`. `node_modules/next/dist/docs/01-app/02-guides/upgrading/version-16.md` is the consolidated list of what changed.
