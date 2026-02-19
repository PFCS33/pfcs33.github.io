# CLAUDE.md — Codebase Context Reference

## Project Overview

This is a personal academic portfolio site built on the [al-folio](https://github.com/alshedivat/al-folio) Jekyll theme (v0.16.3). Owner: Yunshan Feng. Hosted on GitHub Pages at `pfcs33.github.io`.

## Branch Strategy

- **master** — Production branch. Tracks a specific al-folio release tag. GitHub Pages deploys from here.
- **dev** — Working branch. All customization happens here. Merge into master when stable.
- **archive-v1** — Snapshot of old content. Not actively maintained.

Upstream remote (`upstream`) points to `alshedivat/al-folio`. Origin (`origin`) points to `PFCS33/pfcs33.github.io`.

## Tech Stack

- Jekyll (static site generator) with Liquid templating
- SCSS (Bootstrap-based) for styling
- Ruby plugins for extended functionality
- Docker for local development
- GitHub Actions for CI/CD

## Key Config — `_config.yml`

The `title` field uses the literal string `blank` to trigger full-name display (not an empty string — the template checks `site.title == 'blank'`).

Current settings:

- `url`: https://pfcs33.github.io
- `baseurl`: /al-folio (change to blank for `<username>.github.io` root deployment)
- `first_name`: Yunshan, `last_name`: Feng
- Dark mode, math rendering, search, masonry layout all enabled
- Jekyll Scholar for bibliography (APA style, grouped by year descending)
- 21 Jekyll plugins active

## Directory Map

```
_config.yml          — Main site config (title, URL, features, plugins)
_pages/              — Site pages (about, blog, cv, projects, publications, etc.)
_posts/              — Blog posts (32 example posts, Markdown with YAML frontmatter)
_projects/           — Portfolio projects (9 examples)
_news/               — Homepage announcements (3 examples)
_bibliography/       — BibTeX file (papers.bib) for publications page
_data/               — YAML data files:
  ├── socials.yml    — Social media links and handles
  ├── cv.yml         — CV content (education, experience, awards)
  ├── coauthors.yml  — Co-author name-to-URL mapping
  ├── citations.yml  — Google Scholar citation data (auto-generated)
  ├── repositories.yml — GitHub repos to display
  └── venues.yml     — Publication venue info
_includes/           — Reusable Liquid components (header, footer, figures, etc.)
_layouts/            — Page templates (about, page, post, cv, bib, etc.)
_sass/               — SCSS stylesheets (_base.scss is 1528 lines, core styling)
_plugins/            — Custom Ruby plugins (external posts, scholar citations, etc.)
_scripts/            — JS (search) and Python (citation updater) scripts
_teachings/          — Teaching course materials
_books/              — Book review collection
assets/
  ├── img/           — Images (prof_pic.jpg is the profile picture)
  ├── css/           — Compiled CSS (Bootstrap, MDB, Academicons, etc.)
  ├── js/            — JavaScript (Bootstrap, theme switcher, search, etc.)
  ├── pdf/           — PDF files (CV, papers)
  ├── json/          — resume.json (JSON resume format)
  └── fonts/         — Web fonts (Font Awesome, Material Icons, etc.)
bin/
  ├── entry_point.sh — Docker container startup script
  └── deploy         — Git-based deployment script
```

## Docker Setup

- `docker-compose.yml` — Uses `amirpourmand/al-folio:v0.16.3` image
- Ports: 8080 (Jekyll), 35729 (LiveReload)
- Volume mounts project root to `/srv/jekyll`
- `docker-compose-slim.yml` — Lighter alternative image
- `Dockerfile` — Ruby:slim base, installs system deps + gems

Launch: `docker compose pull && docker compose up`
Rebuild: `docker compose up --build`
Stop: `Ctrl+C` then `docker compose down`

## Gemfile

Core gem: `jekyll`. Two plugin groups:

- `:jekyll_plugins` — 20 gems including jekyll-scholar, jekyll-terser (git source), jemoji
- `:other_plugins` — css_parser, feedjira, httparty, observer, ostruct

The `observer` and `ostruct` gems are needed for Ruby 4.0 compatibility.

## Pages and Their Layouts

| Page         | File                    | Layout  | Permalink      |
| ------------ | ----------------------- | ------- | -------------- |
| Homepage     | \_pages/about.md        | about   | /              |
| Blog         | \_pages/blog.md         | default | /blog/         |
| CV           | \_pages/cv.md           | cv      | /cv/           |
| Projects     | \_pages/projects.md     | page    | /projects/     |
| Publications | \_pages/publications.md | page    | /publications/ |
| Repositories | \_pages/repositories.md | page    | /repositories/ |
| Teaching     | \_pages/teaching.md     | page    | /teaching/     |
| Books        | \_pages/books.md        | page    | /books/        |

## Collections

Defined in `_config.yml`:

- `books` — output: true
- `news` — output: true, layout: post
- `projects` — output: true
- `teachings` — output: true

## Content Frontmatter Patterns

### Blog Post

```yaml
layout: post
title: Post Title
date: YYYY-MM-DD HH:MM:SS
description: Brief description
tags: tag1 tag2
categories: category_name
```

### Project

```yaml
layout: page
title: Project Title
description: Short description
img: assets/img/image.jpg
importance: 1
category: work
```

## Customization Priorities

Files to edit for personalization (in order):

1. `_config.yml` — Site identity, URL, features
2. `_pages/about.md` — Homepage content
3. `_data/socials.yml` — Social links
4. `_data/cv.yml` — CV data
5. `assets/img/prof_pic.jpg` — Profile picture
6. `_bibliography/papers.bib` — Publications
7. `_posts/` — Replace example posts with real content
8. `_projects/` — Replace example projects
9. `_news/` — Replace example announcements

## CI/CD Workflows (`.github/workflows/`)

15 workflows total. Key ones:

- `deploy.yml` — Build and deploy to GitHub Pages on push to main/master
- `prettier.yml` — Code formatting checks
- `broken-links.yml` — Scheduled link checking
- `update-citations.yml` — Scheduled Google Scholar citation updates
- `axe.yml` — Accessibility testing

## Template Logic Notes

- `site.title == 'blank'` triggers full-name display (header.liquid, metadata.liquid)
- `_includes/header.liquid` — Navigation bar, uses `site.title` or `first_name + last_name`
- `_includes/metadata.liquid` — SEO metadata, same title logic
- Third-party libraries (50+) configured in `_config.yml` with CDN URLs and integrity hashes
- Image processing: ImageMagick generates responsive images at 480/800/1400px widths in WebP

## Known Gotchas

- `title` must be the literal string `blank` (not empty string) to use name fallback
- `Gemfile.lock` tracked by git causes Docker issues — entry_point.sh skips `bundle install` when it detects git tracking
- `jekyll-terser` uses a git source, needs `--build` flag if building from Dockerfile
- `observer` and `ostruct` gems required for Ruby 4.0 (removed from stdlib in 3.4+)
- `baseurl` must match repo name for project pages, or be blank for `<username>.github.io`
