# Plan — ddot.dev portfolio refactor

> Plan written by Opus for Sonnet to execute in a fresh session.
> Goal: refactor the current Astro template into a portfolio aimed at big tech recruiters / hiring managers in London. Audience scans in under 30 seconds.

## Decisiones cerradas (no replantear)

- **Single-page** with anchor nav: `#about`, `#experience`, `#projects`, `#skills`, `#contact`. The standalone `/about` page is **removed**.
- **Visual direction: editorial / terminal-dev.** Mono font everywhere, README-style density, prompt-like accents (`>`, `$`, `//`), numbered section headers (`01.`, `02.`). References: paco.me, fly.io/blog.
- **English only**, no i18n, **no blog / writing** section.
- Projects: structured **placeholders** — the user does not yet have a final project list.

---

## 1. Design system

**Typography** — the default system mono is poor. Load **JetBrains Mono** (or Geist Mono) via `<link>` in `Base.astro` with `rel="preconnect"` and `font-display: swap`. Weights 400 / 500 / 700. Single family for the entire site.

**Palette** — drop all gradients and `bg-gradient-to-r` hovers.

| Token  | Light       | Dark                  |
| ------ | ----------- | --------------------- |
| bg     | `stone-50`  | `stone-950` (not 900) |
| text   | `stone-900` | `stone-100`           |
| dim    | `stone-500` | `stone-400`           |
| border | `stone-200` | `stone-800`           |
| accent | `orange-600`| `orange-500`          |

**Layout** — `Section.astro` shrinks from `max-w-screen-lg` (1024px) to `max-w-2xl` (672px). Narrow column reads better in editorial style. Vertical padding `py-16 md:py-24`. Line-height `~1.7` for prose.

**Separators** — thin `<hr>` with `border-stone-200/dark:border-stone-800`, plus numbered headers (`01. about`) as visual anchors. Zero shadows, zero decorative gradients.

---

## 2. `AppConfig.ts` — single source of truth

Extend with everything referenced from multiple components:

```ts
export const AppConfig = {
  site_name: 'ddot.dev',
  title: 'Diego Olivas — Software Engineer',
  description: '...',
  author: 'Diego Olivas',           // confirm: currently says "Daniel"
  location: 'Madrid, relocating to London',
  email: 'diegodavid71@gmail.com',
  github: 'https://github.com/<real-username>',  // current value is '#'
  linkedin: 'https://www.linkedin.com/in/ddot/',
  cv_path: '/resume.pdf',
  locale: 'en',
  locale_region: 'en-us'
};
```

---

## 3. Components — changes and additions

| Component             | Action     | Key notes                                                                                                                                                                                                                                                                                                       |
| --------------------- | ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Section.astro`       | modify     | `max-w-2xl`, add props `id` and `number` for the `01.` prefix                                                                                                                                                                                                                                                  |
| `Navbar.astro`        | modify     | Sticky with `backdrop-blur bg-stone-50/80 dark:bg-stone-950/80 border-b`. Links: `about / work / projects / contact` with smooth scroll. Branding: `$ ddot.dev` (drop the 💻 emoji). Keep theme switcher.                                                                                                       |
| `Hero.astro`          | rewrite    | `> Hi, I'm Diego` (prompt prefix). Two-line tagline: role + what you're looking for. `currently:` line for current status. Two CTAs `[Get in touch]` `[Download CV]` rendered as **text links, not buttons**.                                                                                                  |
| `About.astro`         | **new**    | 2–3 personal paragraphs. User supplies copy.                                                                                                                                                                                                                                                                    |
| `Experience.astro`    | **new**    | `<dl>` with `grid-cols-[120px_1fr]`. Format:<br>`2023 — Present   Senior SWE @ Accenture`<br>`                 One-line impact.`                                                                                                                                                                              |
| `Projects.astro`      | rewrite    | Vertical readme-style list, **no gradient-border cards**:<br>`01. Project name                          [↗ live] [↗ code]`<br>`    One-paragraph: problem · solution · impact.`<br>`    tags: react · typescript · postgres`<br>Ship 3–4 placeholders.                                                          |
| `Skills.astro`        | **new**    | Two-column table: category / list (Languages, Frontend, Backend, Tooling, Infra).                                                                                                                                                                                                                              |
| `Contact.astro`       | simplify   | Single inline line: `Reach me at email · github · linkedin`. No card.                                                                                                                                                                                                                                          |
| `Social.astro`        | modify     | From chunky buttons to inline text links with `h-3 w-3` icons.                                                                                                                                                                                                                                                  |
| `Footer.astro`        | modify     | `built with astro · last updated YYYY-MM · view source` (link to repo). Drop the `/about` link.                                                                                                                                                                                                                |

---

## 4. Pages

- `src/pages/index.astro` — composes Hero, About, Experience, Projects, Skills, Contact in order
- `src/pages/about.astro` — **delete**

---

## 5. SEO / meta in `Base.astro`

- Open Graph: `og:title`, `og:description`, `og:image`, `og:type=website`, `og:url`
- Twitter: `twitter:card=summary_large_image`
- `<link rel="canonical">`
- **JSON-LD `Person` schema** — Google understands who you are → richer SERP, real differentiator for a personal portfolio
- `og.png` 1200×630 in `public/`. Sonnet can generate a simple text-based version ("ddot.dev · Diego Olivas · Software Engineer" on dark + orange accent), or the user supplies one.

---

## 6. Technical polish (low cost, high impact)

- **Astro View Transitions** (`<ClientRouter />` in `Base.astro`) — two lines, smooth transitions for free
- Honor `prefers-reduced-motion` on any animation added
- `astro check` clean before commit (already part of `npm run build`)
- Lighthouse mobile target: 95+ (trivial with this stack)
- Confirm `@astrojs/sitemap` still generates correctly after changes
- `public/resume.pdf` placeholder file (user replaces)

---

## 7. User-supplied content (NOT inventable)

Sonnet should request these from the user as it executes, or read them from a `content.md` if provided ahead of time:

- [ ] Confirm name: **Diego** vs Daniel in `AppConfig.ts`
- [ ] `public/resume.pdf` — actual CV
- [ ] Hero pitch: role, years of experience, primary stack, what they're looking for, location / visa status
- [ ] About: 2–3 paragraphs in English with personality (not LinkedIn boilerplate)
- [ ] Experience: company, role, dates, 1–2 lines of impact per position
- [ ] Real skills list by category
- [ ] Real GitHub URL (current value is `#`)
- [ ] Projects (when available — structured placeholders in the meantime)

---

## 8. Suggested execution order for Sonnet

1. Font + palette + extended `AppConfig.ts` + meta tags in `Base.astro`
2. `Section.astro` (narrow + `number` prop) + `Navbar.astro` (sticky + anchors + branding)
3. `Hero.astro` + `Footer.astro` (above-the-fold)
4. `About.astro` + `Experience.astro` (with user data)
5. `Projects.astro` with placeholders
6. `Skills.astro`
7. `Contact.astro` + `Social.astro` simplified
8. OG image + `resume.pdf` placeholder
9. Delete `src/pages/about.astro`
10. View transitions + reduced-motion check
11. `npm run build` (runs `astro check`) and verify Lighthouse on `npm run preview`

---

## 9. Out of scope (explicit)

Blog / writing · multi-language · contact form (mailto is enough) · CMS / Content Collections · aggressive animations (parallax, custom cursor) · per-project detail pages.

---

## Notes for Sonnet

- The repo uses **Tailwind v4** via `@tailwindcss/vite` (not `@astrojs/tailwind` — the README is stale on this). Tailwind config primarily lives in `src/styles/global.css` and `astro.config.mjs`.
- Path alias `@/*` → `./src/*`. Use it (no relative imports).
- `tsconfig.json` is strict-plus (`noUncheckedIndexedAccess`, `noUnusedLocals`, etc.) — write code that passes `astro check`.
- Prettier: tabs, single quotes, no trailing commas, 100-col width. The Tailwind plugin auto-sorts class lists — don't hand-order.
- The user prefers planning with Opus and executing with Sonnet (this file is the handoff). When something in the plan turns out to be wrong or impractical mid-execution, push back in the conversation rather than silently deviating.
