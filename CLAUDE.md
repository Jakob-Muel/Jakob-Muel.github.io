# Portfolio Website — Jakob Müller

Personal portfolio site built with **Astro 4**, deployed to **GitHub Pages** via GitHub Actions.
Live at: `https://jakob-muel.github.io`

## Commands

```bash
npm run dev      # local dev server (http://localhost:4321)
npm run build    # production build → dist/
npm run preview  # preview the built site locally
```

## Project structure

```
src/
  layouts/Layout.astro        shared page shell (nav, footer, OG meta)
  styles/global.css           entire design system — Daring Fireball aesthetic
  pages/
    index.astro               landing page / home
    projects.astro            app cards (PkmChain + Quizness)
    music.astro               music platform links
    404.astro                 custom 404
    blog/
      index.astro             blog post list
      [...slug].astro         per-post template
  content/
    config.ts                 content collection schema (title, date, description, draft)
    blog/                     blog posts as .md files — add new posts here
public/
  favicon.svg
.github/workflows/deploy.yml  build + deploy on push to main
apps/
  pkm-chain-athon/            git submodule → Pkm-Infinite-Fights repo
```

## How to add a blog post

Create a new `.md` file in `src/content/blog/` with this frontmatter:

```md
---
title: "Your post title"
description: "Short summary"
date: 2026-05-21
draft: false
---

Post content here...
```

The post will appear automatically on `/blog`.

## Apps

| App | How it's served |
|---|---|
| PkmChain-athon | Git submodule at `apps/pkm-chain-athon/`, copied to `dist/pkm-chain-athon/` by the deploy workflow. Lives at `/pkm-chain-athon/`. |
| QuiznessBusiness | External link only — Next.js app, hosted on Vercel at `https://quizness-business.vercel.app/`. Cannot be static. |

## Submodule setup (one-time, if not already done)

```bash
git submodule add https://github.com/Jakob-Muel/Pkm-Infinite-Fights.git apps/pkm-chain-athon
git commit -m "Add PkmChain submodule"
```

After cloning fresh: `git submodule update --init --recursive`

## Design system

All styling lives in `src/styles/global.css`. Key tokens:

- `--bg: #fdfcf7` — warm off-white background
- `--text: #1a1a1a` — near-black
- `--accent: #3b6ea5` — link / button colour
- `--col-width: 680px` — centred content column
- Body font: Georgia / serif

## Deployment

Push to `main` → GitHub Actions builds and deploys automatically.
Requires: **Settings → Pages → Source = GitHub Actions** (one-time setup in the repo).

## TODO

- [ ] Fill in real music URLs in `src/pages/music.astro` (Spotify, SoundCloud, Bandcamp)
- [ ] Add a real OG image at `public/og.png`
- [ ] Add PkmChain submodule (see above)
- [ ] Custom domain — when ready: add `public/CNAME` with the domain, update `site` in `astro.config.mjs`
