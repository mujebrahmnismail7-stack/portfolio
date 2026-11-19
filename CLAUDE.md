# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Static personal portfolio site for a videographer, served at `mujeebrahmanismail.com` (see `CNAME`, so likely GitHub Pages). There is no build step, package manager, linter or test suite: just two self-contained HTML files with inline `<style>` and `<script>`. The only external dependency is Google Fonts (Archivo + Inter).

To preview locally: `python3 -m http.server` from the repo root, then open `http://localhost:8000/`.

## Architecture

- `index.html` is the main page (hero, Services, Work, Process, Experience, CTA).
- `work-with-me.html` is the testimonials, packages/pricing and contact page.
- **The two files duplicate the same CSS and the same JS (data and rendering code).** Each page renders only the sections whose container elements it has. Render code guards with `if (el)` checks on `getElementById`, so a missing section is skipped rather than throwing. Any change to shared styles, content data or rendering logic usually has to be made in **both** files. After editing, diff the corresponding blocks to make sure they still match. The one intended difference is the pricing "Get a quote" button: on `work-with-me.html` it opens a WhatsApp chat (`wa.me/971547170476?text=...`) pre-filled with the package details, and on `index.html` it links to `#contact`.
- **Content is data-driven.** Most visible content is not written as HTML. It lives in JS constants at the top of the `<script>` block (marked `/* ===== EDIT YOUR CONTENT HERE ===== */`) and is rendered into empty containers (`#projects`, `#steps`, `#prices`, `#tgrid`, `#jobs`, `#tools`, `#skills`, `#mtrack`, `#ring`). The constants are `SHOWREEL`, `TESTIMONIALS`, `BRANDS`, `PROJECTS`, `RING`, `STEPS`, `PACKAGES`, `ADDONS`, `TERMS`, `JOBS`, `TOOLS` and `SKILLS`. To change content, edit these constants rather than the markup.
  - `PROJECTS` entries are `{tag, title, link, thumb}`. `tag` drives the auto-generated filter chips. `link` is usually a Google Drive `/file/d/<id>/view` URL. `thumb` is a `lh3.googleusercontent.com/d/<id>=w900` URL.
  - `RING` lists project **titles**, which are looked up in `PROJECTS` by exact `title` match. Renaming a project silently drops it from the 3D ring.
  - `PACKAGES` is keyed by category name, which becomes the pricing tab label. Each category has `tiers`, and a tier with `hot: true` is shown as "Recommended".
- Interpolated strings go through the `esc()` helper before being inserted with `innerHTML`. Keep doing this for any new data fields.
- Styling uses CSS custom properties on `:root` (a monochrome dark palette: `--bg`, `--text`, `--muted`, `--accent`, and so on). Responsive breakpoints sit near the end of the `<style>` block (`max-width:760px`, plus smaller ones down to `380px`). Animations respect `prefers-reduced-motion`, and the hover and tilt effects only run when `(pointer:fine)` matches.
- Navigation order, kept the same on both pages: Services, Work, Process, Work with me, then the "Hire me" button (`work-with-me.html#contact`).
