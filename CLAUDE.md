# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

- `npm run dev` — start Astro dev server
- `npm run build` — runs `astro check` (type check) then `astro build` to `./dist/`
- `npm run preview` — preview the production build locally
- `npm run astro -- <cmd>` — Astro CLI (e.g. `astro add`, `astro check`)

There is no test runner configured. Type-checking happens as part of `npm run build` via `astro check`; run it directly with `npm run astro -- check` to validate types without building.

## Architecture

Personal portfolio site built with **Astro 5** (static output, `output: 'static'`), deployed to **Vercel** via `@astrojs/vercel` with web analytics enabled.

- **Styling**: Tailwind CSS **v4** wired through `@tailwindcss/vite` in `astro.config.mjs` — *not* `@astrojs/tailwind` (the README is outdated on this point). `tailwind.config.mjs` exists for the typography plugin and `darkMode: 'class'`, but Tailwind v4 is primarily configured via the Vite plugin and `src/styles/global.css`. Dark mode toggles via a `dark` class on the root.
- **React**: `@astrojs/react` is installed and configured, but all current components are `.astro` files. React is available for client-island components when needed.
- **Markdown / code blocks**: Astro's built-in syntax highlighter is disabled; `rehype-pretty-code` is used instead (see `options` in `astro.config.mjs`). Empty lines are preserved and highlighted lines get a `highlighted` class.
- **Path alias**: `@/*` → `./src/*` (configured in `tsconfig.json`). Use `@/components/Foo.astro`, `@/utils/AppConfig`, etc. rather than relative paths.
- **Site metadata**: centralized in `src/utils/AppConfig.ts` (title, description, author, locale). `src/layouts/Base.astro` is the single layout — pages compose section components inside it.

### TypeScript

`tsconfig.json` is strict-plus: `strict`, `noUncheckedIndexedAccess`, `noUnusedLocals`, `noUnusedParameters`, `noImplicitReturns`, `allowUnreachableCode: false`. Indexed access returns `T | undefined` — handle accordingly.

### Formatting

Prettier with `prettier-plugin-astro` and `prettier-plugin-tailwindcss`: **tabs**, single quotes, no trailing commas, semicolons, 100-col print width. The Tailwind plugin auto-sorts class lists, so don't hand-order classes.
