# Portfolio Website — Project Brief & Build Plan

> **Purpose of this document.** This is a complete handoff brief. It is written
> so that an agent (Sonnet) starting in a fresh context window can read this
> single file and begin building immediately, with no prior conversation
> needed. It captures every decision made during planning with Jakob.
>
> **Owner:** Jakob (GitHub username: `Jakob-Muel`, email: highonkola@gmail.com)
> **Date of planning:** 2026-05-21
> **Workspace folder:** `Portfolio Website` (this folder).

---

## 1. What we are building

A personal portfolio website for Jakob. It has four jobs:

1. A **landing page** that introduces Jakob.
2. A **projects section** showing apps Jakob has built, with links to use them.
3. A **blog**, with posts authored as Markdown files.
4. **Links to Jakob's music** (hosted elsewhere — Spotify / Bandcamp / etc.).

The site is **static** and hosted on **GitHub Pages**.

---

## 2. Aesthetic direction

The reference is **Daring Fireball** (daringfireball.net): simple, chic,
text-first, and **focused on the centre of the screen**. Jakob explicitly wants
the eye not to have to wander — empty air on the left and right is fine and
desired.

Concrete direction:

- A **single centred column**, roughly **680px** max-width. Generous empty
  margins left and right on desktop.
- On mobile the column simply becomes full width (with small side padding).
  No separate mobile layout is needed — the centred-column design is naturally
  responsive.
- **Serif body text** at a comfortable reading size (~18–20px), generous line
  height (~1.6).
- Restrained palette: near-black text on a warm off-white background, **one**
  accent colour used for links. Suggested starting values (adjust to taste):
  background `#fdfcf7`, text `#1a1a1a`, accent `#3b6ea5`.
- Minimal chrome: a small, quiet navigation at the top; no sidebars; no heavy
  header; no clutter.
- It should look great on both mobile and desktop.

---

## 3. Tech stack (decided)

| Choice | Decision | Why |
|---|---|---|
| Site generator | **Astro** | Static output, near-zero JS, built-in Markdown content collections — ideal for a fast text-focused site. Jakob has *some* web dev experience (plain HTML + some React); Astro is approachable and not over-magical. |
| Hosting | **GitHub Pages** | Free static hosting, Jakob's stated preference. |
| Deployment | **GitHub Actions** | A workflow builds the Astro site and deploys on every push to `main` — no manual steps. |
| Blog authoring | **Markdown files in the repo** | Posts are `.md` files committed to the repo; Astro content collections turn them into pages. Version-controlled, no external CMS. |

---

## 4. Repository & hosting setup

- The portfolio repo should be named **`Jakob-Muel.github.io`**.
  GitHub serves a repo of that name at the **root** of the domain
  (`https://jakob-muel.github.io/`), which keeps URLs clean and makes adding a
  custom domain easy later.
- Because the site is served from the root, Astro's `base` is simply `/` — no
  base-path complexity for the portfolio itself.
- A single GitHub Actions workflow (`.github/workflows/deploy.yml`) builds and
  deploys on every push to `main`.

---

## 5. The two apps — how each is handled

Jakob has built two apps, each in its own repo. They are handled **differently**
because of a fundamental technical difference (full analysis came from a
`PORTFOLIO_INTEGRATION.md` file generated inside each repo).

### 5a. PkmChain-athon — static game

- **What it is:** a browser-based Pokémon stat-battle game (Top Trumps style),
  Gen 1 only. Plain HTML/CSS/vanilla JS, **no build step, no framework**.
- **Repo:** `https://github.com/Jakob-Muel/Pkm-Infinite-Fights.git`
  ⚠️ **The repo was renamed.** The git remote is now `Pkm-Infinite-Fights`,
  but its current standalone live URL is still
  `https://jakob-muel.github.io/Pkm-Chain-Athon/`. Verify the actual live URL
  and the correct remote before wiring anything up.
- **Paths:** all relative (`./style.css`, `fetch('./pkm/pokemon.json')`), so it
  works correctly when served from a subpath — no code changes needed.
- **Backend:** talks to Supabase directly from the browser (auth + leaderboard).
  This is fine for a static host — the Supabase URL and anon key are
  intentionally public (protected by Row Level Security).

**Plan (primary): include as a git submodule, serve at a subpath.**
Add the repo as a git submodule of the portfolio (e.g. at `apps/pkm-chain-athon/`).
The deploy workflow copies its static files into the built site at
`/pkm-chain-athon/`, so it lives at `jakob-muel.github.io/pkm-chain-athon/` —
the unified `website/app` shape Jakob wanted.
When copying, **exclude** non-runtime files: `venv/`, `.idea/`, `.git`,
`fetch_pokemon.py`, `README.md`, `CLAUDE.md`, `PORTFOLIO_INTEGRATION.md`.

**Documented alternative (to confirm with Jakob):** the repo's own integration
analysis recommended simply *linking out* to its existing standalone GitHub
Pages deployment instead of embedding it, since the app is already deployed and
self-contained. This is lighter-weight but means the app keeps its own URL
rather than living under the portfolio. **Jakob should confirm which he wants.**
The primary plan above (submodule subpath) matches his stated goal of apps at
`website/appN`.

### 5b. QuiznessBusiness — Next.js app (link out only)

- **What it is:** a Sporcle-style "name them all" quiz app. Next.js 16 (App
  Router), TypeScript, Tailwind, Supabase.
- **Repo:** `https://github.com/Jakob-Muel/QuiznessBusiness.git`
- **Critical constraint:** this app **requires a Node.js server runtime.** It
  uses Next.js middleware, server actions, and server components that read
  cookies. It **cannot** be exported to static files and therefore **cannot**
  be hosted on GitHub Pages or embedded as a portfolio subpath.
- **Decision:** Quizness is deployed **separately on Vercel** (its intended
  host). The portfolio simply **links to it** with a project card. Jakob
  confirmed he is hosting Quizness on Vercel himself.
- Note: the *database* for both apps is Supabase — that is not the issue. The
  issue is the *application server* layer that Next.js needs and GitHub Pages
  cannot provide.

---

## 6. Site structure

```
Jakob-Muel.github.io/                 (the portfolio repo)
├── .github/workflows/deploy.yml       build + deploy on push to main
├── apps/
│   └── pkm-chain-athon/               git submodule → Pkm-Infinite-Fights
├── src/
│   ├── content/blog/                  blog posts as .md files
│   ├── layouts/                       shared page shell / layout component
│   ├── pages/
│   │   ├── index.astro                landing page
│   │   ├── projects.astro             the apps, with links
│   │   ├── music.astro                links to Jakob's music
│   │   └── blog/                      blog index + per-post pages
│   └── styles/global.css              the Daring Fireball styling
├── public/                            favicon, images, static assets
├── astro.config.mjs
└── package.json
```

**Pages:**

- **Landing / home** — a short intro to Jakob and a few links. About-info can
  fold in here rather than being a separate page (keeps the site lean).
- **Projects** — cards for PkmChain (link to `/pkm-chain-athon/`) and Quizness
  (link to its Vercel URL). Each card: name, short description, tech tags,
  GitHub link, and a "play / open" button.
- **Blog** — an index listing posts, and one page per post rendered from its
  Markdown file.
- **Music** — links out to wherever Jakob's music lives.

---

## 7. Build plan — phases

Work through these in order. Each phase should leave the site in a working
state.

**Phase 1 — Scaffold.** Create the Astro project in this folder. Set up
`astro.config.mjs` (site = `https://jakob-muel.github.io`, base = `/`),
`package.json`, `.gitignore`. Initialise the git repo.

**Phase 2 — Layout & design system.** Build `global.css` and the shared layout
component implementing the Daring Fireball aesthetic from section 2: centred
~680px column, serif body, palette, quiet top nav, footer. Verify it is
responsive on mobile and desktop.

**Phase 3 — Core pages.** Build the landing page, projects page (with the two
app cards), and music page.

**Phase 4 — Blog.** Set up an Astro content collection for the blog, the blog
index page, and the per-post template. Add one sample Markdown post so the
flow is demonstrable.

**Phase 5 — PkmChain integration.** Add `Pkm-Infinite-Fights` as a git
submodule at `apps/pkm-chain-athon/`. Write the GitHub Actions workflow so it
checks out submodules, builds the Astro site, and copies PkmChain's runtime
files into `dist/pkm-chain-athon/` (excluding the files listed in 5a).

**Phase 6 — Deploy.** Finish `.github/workflows/deploy.yml` to deploy the built
site to GitHub Pages. Configure the repo's Pages settings (source = GitHub
Actions). Push and verify the live site, including `/pkm-chain-athon/`.

**Phase 7 — Polish.** Favicon, page `<title>`s and meta description, social /
OG tags, a 404 page, and a final responsive pass.

**Verification:** after Phase 6, confirm the live site loads, the blog renders,
both project links work, and PkmChain plays correctly at its subpath.

---

## 8. Open decisions — confirm with Jakob before/while building

1. **PkmChain: submodule subpath vs. link-out.** Primary plan is the submodule
   subpath (section 5a); the alternative is linking to its existing deploy.
   Confirm Jakob's preference.
2. **Repo name.** `Jakob-Muel.github.io` is recommended (root domain). Confirm.
3. **Custom domain.** None planned yet. Ask if Jakob wants one — it changes
   nothing structural but is easiest to set up early.
4. **Page content.** Needs Jakob's actual words: the landing-page intro/bio,
   project descriptions, and the music links/platforms.
5. **Quizness live URL.** The Vercel URL for Quizness, once deployed, for the
   project card link.

---

## 9. Quick reference

- **Jakob's GitHub:** `Jakob-Muel`
- **Portfolio repo (to create):** `Jakob-Muel.github.io` → `https://jakob-muel.github.io/`
- **PkmChain repo:** `https://github.com/Jakob-Muel/Pkm-Infinite-Fights.git` (renamed; live at `/Pkm-Chain-Athon/`)
- **Quizness repo:** `https://github.com/Jakob-Muel/QuiznessBusiness.git` (Next.js, hosted on Vercel, link-out only)
- Each app repo contains a local-only `PORTFOLIO_INTEGRATION.md` with full
  technical detail — read it if deeper integration questions come up.
