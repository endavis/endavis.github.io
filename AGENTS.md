# Project Context: al-folio (v0.15.0)

## Overview

`al-folio` is a simple, clean, and responsive Jekyll theme designed specifically for academics. It serves as a template for creating personal websites, portfolios, or lab pages. It features a built-in CV, publication lists generated from BibTeX, news sections, and support for math and code.

**Current Version:** `v0.15.0` (Updated: January 10, 2026)

## Architecture & Technologies

- **Framework:** [Jekyll](https://jekyllrb.com/) (Static Site Generator).
- **Language:** Ruby (for Jekyll and plugins), Liquid (templating), SCSS (styling).
- **Dependencies:** Managed via `Gemfile` (Ruby gems) and `package.json` (Node.js for development tools like Prettier).
- **Theme:** Custom theme with support for Light/Dark modes, responsive design, and integration with third-party services (Google Analytics, Disqus/Giscus, etc.).

## Key Files & Directories

- **`_config.yml`**: The central configuration file. This is where site metadata (title, author, email), enabled features (news, projects, books, repositories), and theme settings are defined. **Start here for customizations.**
- **`_bibliography/papers.bib`**: The BibTeX file containing publications. The site automatically generates the "Publications" page from this file.
- **`_pages/`**: Markdown files representing the main pages of the site (e.g., `about.md`, `cv.md`, `publications.md`, `projects.md`).
- **`_posts/`**: Blog posts. The file name format is `YYYY-MM-DD-title.md`.
- **`_projects/`**: Markdown files for individual projects, displayed on the Projects page.
- **`_books/`**: Markdown files for book reviews/collections (New in v0.15.0).
- **`_news/`**: Markdown files for news items/announcements.
- **`_data/`**: YAML/JSON data files for structured content like `cv.yml` (fallback CV), `venues.yml`, `coauthors.yml`, etc.
- **`assets/`**: Static assets including images (`img/`), CSS (`css/`), PDFs (`pdf/`), and the JSON resume (`json/resume.json`).
- **`Gemfile`**: Lists the Ruby gems (plugins) required to build the site.
- **`docker-compose.yml`**: Configuration for running the site locally using Docker.

## Build & Development

### Recommended: Docker

The easiest way to run the site locally is using Docker to avoid environment issues.

```bash
# Build and serve the site
docker compose up

# Build with a slim image (beta)
docker compose -f docker-compose-slim.yml up

# Rebuild the image (e.g., after updating Gemfile)
docker compose up --build
```

The site will be available at `http://localhost:8080`.

### Local (Ruby Environment)

If you prefer running Jekyll directly:

1.  **Install Dependencies:**
    ```bash
    bundle install
    ```
2.  **Serve the Site:**
    ```bash
    bundle exec jekyll serve
    ```
    The site will be available at `http://localhost:4000`.

### Formatting

The project uses Prettier for code formatting.

```bash
# Install dependencies
npm install

# Check formatting
npx prettier . --check

# Fix formatting
npx prettier . --write
```

## Common Tasks

- **Update Personal Info:** Edit `_config.yml` and `_pages/about.md`.
- **Add Publication:** Add a new BibTeX entry to `_bibliography/papers.bib`.
- **Update CV:** Edit `assets/json/resume.json` (standard JSON Resume) or `_data/cv.yml` (fallback).
- **Add Blog Post:** Create a new file in `_posts/`.
- **Change Theme Color:** Edit `_sass/_themes.scss` or `_sass/_variables.scss`.

## Deployment

The site is configured for deployment to GitHub Pages.

- **Automatic:** Pushing to the `main` branch triggers a GitHub Action to build and deploy to the `gh-pages` branch.
- **Manual:** `bundle exec jekyll build` generates the static site in `_site/`.

## Interaction Guidelines

- **GitHub CLI (`gh`):** Prioritize using the `gh` CLI tool for GitHub interactions (issues, PRs, workflows, logs) over manual web scraping or assumptions.
- **Commit Protocol:** Do NOT commit changes unless explicitly instructed by the user or if the task implies a complete unit of work. When in doubt, stage changes or ask.
- **User Confirmation:** When a decision point arises or a question forms in your reasoning process, STOP immediately. Do NOT assume the answer. Explain the situation and ask the user for direction.
