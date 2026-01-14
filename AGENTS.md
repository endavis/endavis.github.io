# AGENTS.md

Guidelines for AI agents working on this repository.

## Project Context

This is a personal academic website built with the **al-folio** Jekyll theme, forked from [alshedivat/al-folio](https://github.com/alshedivat/al-folio). It includes custom GitHub stats and trophy generation functionality.

## Key Remotes

- `origin` - Personal site repository (endavis/endavis.github.io)
- `upstream` - Original al-folio theme (alshedivat/al-folio)
- `al-folio-fork` - Fork for PRs to upstream (endavis/al-folio)

## Working with Branches

- `main` - Production branch for personal site
- `feature/*` - Feature branches for upstream PRs (push to `al-folio-fork`)
- `pr/*` - Development branches for testing features

When creating PRs to upstream al-folio:

1. Create feature branch from `upstream/main`
2. Push to `al-folio-fork` remote
3. Create PR from `endavis/al-folio` to `alshedivat/al-folio`

## GitHub Stats System

### Architecture

The stats system has three display modes configured in `_config.yml`:

1. **external** (default) - Uses Vercel API services, no setup required
2. **local** - Stores generated SVGs in `assets/img/stats/` on main branch
3. **branch** - Stores SVGs in separate `stats` branch

### Key Files

- `.github/workflows/update-stats.yml` - Main workflow (710+ lines)
- `.config/stats-metrics.yml` - Metrics plugin configuration
- `.config/trophies.yml` - Trophy display settings
- `_data/repositories.yml` - Users and repos to generate stats for
- `_includes/repository/repo*.liquid` - Display templates

### Bug-Fix Fork

The workflow uses `endavis/metrics@fix/habits-activity-typeerror-bugs` instead of `lowlighter/metrics@latest` due to upstream bugs. See `docs/stats.md` for details.

### Token Requirements

The `METRICS_TOKEN` secret requires these scopes:

- `read:user` - Basic user information
- `repo` - Repository access
- `read:org` - Organization membership data
- `read:project` - GitHub Projects v2 (optional)

### Testing Stats Workflow

1. Set `repo_stats_type: branch` in `_config.yml`
2. Configure `stats_branch_url` to point to raw GitHub content
3. Add `METRICS_TOKEN` secret to repository
4. Trigger workflow: `gh workflow run update-stats.yml`
5. Monitor: Check Actions tab or use `gh run view <id>`

**Warning**: Users with extensive GitHub history (like `torvalds`) can exhaust the GraphQL API rate limit (5000 requests/hour). Use moderate-activity users for testing.

## Temporary Files

Use the `tmp/` directory in the repo root for temporary files and clones. This directory is in `.gitignore` and persists across sessions (unlike `/tmp/`).

```bash
# Good
git clone <repo> tmp/my-clone

# Avoid
git clone <repo> /tmp/my-clone  # Gets cleaned up
```

## Code Formatting

Run prettier before committing:

```bash
npx prettier --write <files>
```

Pre-commit hooks will check formatting automatically.

## Common Tasks

### Updating the al-folio Fork

```bash
git fetch upstream
git checkout main
git merge upstream/main
```

### Creating a PR to Upstream

```bash
git fetch upstream
git checkout -b feature/my-feature upstream/main
# Make changes
git push al-folio-fork feature/my-feature
# Create PR via GitHub web UI
```

### Testing Jekyll Build

```bash
bundle exec jekyll build
bundle exec jekyll serve  # Local preview at http://localhost:4000
```

### Triggering GitHub Actions

```bash
# Stats workflow
gh workflow run update-stats.yml --repo <owner>/<repo>

# Deploy workflow
gh workflow run deploy.yml --repo <owner>/<repo>

# Check status
gh run list --repo <owner>/<repo> --workflow=<name>.yml --limit 1
```

## Important Patterns

### Liquid Templates

- Use `site.repo_stats_type` to check display mode
- Support all three modes (external/local/branch) in templates
- Use `relative_url` filter for local assets

### Workflow Artifacts

Stats workflow uploads artifacts per job, then downloads and commits in final job. This enables parallel generation.

### Rate Limiting

GitHub API limits:

- REST API: 5000 requests/hour
- GraphQL API: 5000 requests/hour

Check limits: `gh api rate_limit`

## Debugging

### Workflow Issues

1. Check job logs: `gh run view <id> --log`
2. Look for "Unexpected error" in generated SVGs
3. Verify token scopes match requirements
4. Check rate limit status

### Jekyll Build Issues

1. Run with verbose: `bundle exec jekyll build --verbose`
2. Check `_site/` output
3. Liquid errors show line numbers in templates

## Build Dependencies

- **Ruby 3.3.5+** with Bundler - Jekyll and plugins
- **Python 3.13+** - Jupyter notebook support
- **Node.js** - Prettier and PurgeCSS
- **ImageMagick** - Responsive image generation (`sudo apt-get install imagemagick`)

## Docker Development

Alternative to local Ruby/Jekyll setup:

```bash
# Full development environment
docker-compose up

# Slim version (faster)
docker-compose -f docker-compose-slim.yml up
```

## Content Management

### Adding Blog Posts

1. Create file in `_posts/` with format: `YYYY-MM-DD-title.md`
2. Include front matter (layout, title, date, categories, tags)
3. Use Markdown with optional Distill components for scientific posts

### Adding Publications

1. Edit `_bibliography/papers.bib` with BibTeX entry
2. Optional: Add PDF to `assets/pdf/` (reference with `pdf` field)
3. Optional: Add preview image (reference with `preview` field)
4. Publications page auto-generates from BibTeX

### Managing Social Links

1. Edit `_data/socials.yml`
2. Icons: Font Awesome, Academicons, or Tabler Icons
3. Update `_includes/social.liquid` for custom rendering

## Jekyll Plugins

Custom plugins in `_plugins/`:

- `external-posts.rb` - Fetches external blog posts from RSS
- `google-scholar-citations.rb` - Citation counts from Google Scholar
- `inspirehep-citations.rb` - Citation counts from InspireHEP
- `cache-bust.rb` - Cache-busting for assets
- `file-exists.rb` - Template helper for file existence checks

## Image Processing

ImageMagick generates responsive images automatically:

- Creates WebP versions
- Multiple sizes: 480px, 800px, 1400px
- Configured in `_config.yml` under `imagemagick`

Verify installation: `convert -version`

## Jekyll Scholar

Publications managed by jekyll-scholar plugin:

- Source: `_bibliography/` directory
- Default file: `papers.bib`
- Supports multiple `.bib` files
- Sorting: by year (descending)

## Third-Party Libraries

Configured in `_config.yml` under `third_party_libraries`:

- Can use CDN or bundle locally (`download: true`)
- Includes integrity hashes for security
- MathJax, Mermaid, Chart.js, etc.

## All Workflows

| Workflow                | Purpose                          | Trigger            |
| ----------------------- | -------------------------------- | ------------------ |
| `deploy.yml`            | Build and deploy to GitHub Pages | Push to main       |
| `update-stats.yml`      | Generate GitHub statistics       | Daily cron, manual |
| `prettier.yml`          | Check code formatting            | Push/PR            |
| `broken-links.yml`      | Check for broken links           | Schedule           |
| `update-citations.yml`  | Update publication citations     | Schedule           |
| `lighthouse-badger.yml` | Performance testing (optional)   | Manual             |

## Don't Forget

- Run prettier before commits
- Test workflow changes in fork first
- Update both `docs/stats.md` and `docs/STATS.md` for documentation changes
- The `stats` branch is auto-generated - don't edit directly
- Use `JEKYLL_ENV=production` for production builds
- Check `_site/` folder to verify generated output
