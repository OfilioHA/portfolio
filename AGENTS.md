# AGENTS.md

Personal portfolio for Ofilio Herdocia — Astro 7 + Tailwind 4, single-page with anchor sections, dark UI with brand-blue accents.

## Setup commands

- Install deps: `npm install`
- Start dev:    `npx astro dev` (use `astro dev --background` if your host requires it; manage with `astro dev stop | status | logs`)
- Build:        `npx astro build`
- Preview:      `npx astro preview`

> Node `>=22.22.0` per `package.json` engines. Lockfile is `package-lock.json` (npm, not pnpm/yarn).

## Project layout

```
src/
├─ data/                ← all editable content lives here as JSON
│  ├─ site.json         ← brand, contact, social, CV path
│  └─ profile.json      ← hero, about, skills, projects, experience, education, languages, contact
├─ layouts/
│  └─ Layout.astro      ← <head>, NavBar + Footer + slot, IntersectionObserver + active nav
├─ pages/
│  └─ index.astro       ← composes the six sections in order
├─ components/
│  ├─ ui/               ← primitives: Button, Container, Eyebrow, Icon, Section
│  ├─ layout/           ← NavBar, Footer
│  └─ sections/         ← Hero, About, Skills, Projects, Experience, Contact
└─ styles/
   └─ global.css        ← @import "tailwindcss" + @theme tokens (brand blues, surfaces, fonts, radii, shadows)

public/
└─ cv-ofilio-herdocia.pdf   ← the original CV, served as a static asset
```

## Code style

- **Astro components only** — no React/Vue/Svelte islands. Keep the bundle zero-JS by default; add `<script>` tags only when behavior is needed (NavBar mobile menu, IntersectionObserver reveal, active-section tracking, skill-bar fill animation).
- **Tailwind 4 utilities** — colors, spacing, typography all use the design tokens declared in `src/styles/global.css` under `@theme` (e.g. `bg-bg-base`, `text-text`, `text-brand-400`, `border-border`). Do **not** introduce `tailwind.config.js`; Tailwind 4 reads `@theme` from CSS.
- **Brand palette** — primary `#3b82f6` (`brand-500`). Do not introduce purple/violet accents; keep the cool-blue family.
- **Fonts** — Inter Variable + JetBrains Mono Variable via `@fontsource-variable/*`. Imported once in `global.css`. Reference with `font-display` / `font-mono` (Tailwind tokens) or `var(--font-display)` / `var(--font-mono)` in custom CSS.
- **TypeScript strict** — `tsconfig.json` extends `astro/tsconfigs/strict`. No `any` in committed code; prefer typed props via `interface Props`.
- **Icons** — add new icons to `src/components/ui/Icon.astro` as inline SVGs (`stroke="currentColor"`, `stroke-width={stroke}`). No external icon library.
- **Animations** — keep `.reveal` and `.skill-bar` patterns intact; respect `prefers-reduced-motion` (already handled globally in `global.css`).

## Content editing

Most user-facing copy lives in `src/data/`, **not** in component files. Edit there first:

- Personal info, social handles, CV path → `src/data/site.json` (handles are placeholders like `{{HANDLE}}` until filled in)
- Hero copy, stats, skills, projects, experience, education, languages, contact → `src/data/profile.json`

When adding a new section:

1. Create `src/components/sections/<Name>.astro`.
2. Import the data you need from `src/data/profile.json`.
3. Compose it into `src/pages/index.astro` and add a matching nav entry in `src/components/layout/NavBar.astro` (`navItems` array).
4. Use the `Section` UI primitive or copy the `py-20 sm:py-24` padding convention.

## Testing instructions

There is no test runner configured yet. Manual smoke checklist before considering a change done:

- `npx astro build` exits 0 with no warnings about missing assets or invalid props.
- Dev server returns 200 on `/` and on `/cv-ofilio-herdocia.pdf`.
- All six sections render (`#inicio`, `#sobre-mi`, `#habilidades`, `#proyectos`, `#experiencia`, `#contacto`).
- Reveal animation: scroll a section into view and verify it fades in (the `.is-visible` class is added by `IntersectionObserver` in `Layout.astro`).
- Mobile menu opens and closes (viewport < `md`).
- Reduced-motion preference disables transitions (`prefers-reduced-motion: reduce` in `global.css` already covers this).

## PR & commit conventions

- Default branch: `main` (or whichever Git reports — check with `git symbolic-ref --short refs/remotes/origin/HEAD`).
- Commit style: short imperative subject (e.g. `hero: swap avatar placeholder for <img>`, `skills: bump Laravel to 95`). One logical change per commit.
- Do not commit `node_modules/`, `dist/`, `.astro/`. The starter `.gitignore` already covers these.
- Do not commit `cv-original.pdf` if it ever reappears in the root — only `public/cv-ofilio-herdocia.pdf` is the deliverable.

## Security

- No secrets, API keys, or tokens in this repo. The only PII is the public contact email/phone in `src/data/site.json` (intentional).
- `public/` files are served as-is; never put a sensitive file there.
- `npm` dependencies are pinned by `package-lock.json`; review `npm audit` output before adding new deps.

## Notes for AI agents

- The user prefers changes that preserve originals (e.g. write new JSON files instead of overwriting the CV).
- Spanish is the working language for the conversation; UI copy is Spanish. Future work may add i18n (Astro's `@astrojs/starlight` or `astro-i18n`) — do not scaffold i18n preemptively.
- No image/logo assets are committed yet. The hero avatar is a generated "OH" gradient circle in `Hero.astro`; replace the inner `<div class="absolute inset-[6px] grid place-items-center...">OH</div>` block with `<img src="/me.jpg" ... />` when a photo is available.
- If the user asks to add a section, prefer data-driven composition from `profile.json` over hard-coded content in the component.
