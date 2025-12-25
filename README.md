# Muhammad Karim — Jekyll + Chirpy site

Static site built with Jekyll 4.3 and the Chirpy theme. Drop Markdown writeups into `_posts/` and they will render with SEO, RSS, archives, categories, and tags. Deployed on GitHub Pages.

## Prerequisites (Windows-friendly)
- Ruby 3.1+ with DevKit (ensure `ruby` and `bundle` are on PATH)
- Git
- Optional: run `ridk install` if Ruby prompts for MSYS2 components during bundle install

## Setup and local preview
1. Install gems: `bundle install`
2. Serve locally: `bundle exec jekyll serve`
3. Open http://localhost:4000/ (or the host/port shown in the terminal)

## Add a Markdown writeup
1. Create a file in `_posts/` named `YYYY-MM-DD-title.md`
2. Use front matter like:
	```yaml
	---
	layout: post
	title: My New Writeup
	date: 2025-12-26 12:00:00 +0500
	categories: [Writeups]
	tags: [cloud, security, notes]
	---
	```
3. Write in Markdown. Place images under `assets/img/posts/` and reference them as `/assets/img/posts/<file>.png`.

## Deploy to GitHub Pages
1. Set `url` and `baseurl` in `_config.yml` to match your repo (set `baseurl: ""` for a user/organization pages repo)
2. Commit and push to GitHub
3. In the repo settings → Pages, select `main` and the `/` (root) folder
4. GitHub will build and publish to `https://<username>.github.io/<repo>/`

## Sanity checks
- `bundle exec jekyll doctor` to catch common issues
- `bundle exec htmlproofer ./_site` after a build for broken-link checks

## Notes
- `blog.html`, `projects.html`, and `writeups.html` remain as legacy static pages; the primary site now uses the Chirpy theme and `_posts/` content.
