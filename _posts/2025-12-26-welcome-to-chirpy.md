---
layout: post
title: "Welcome to the Chirpy-powered portfolio"
date: 2025-12-26 10:00:00 +0500
categories: [Writeups]
tags: [jekyll, chirpy, setup]
description: "The site now runs on Jekyll + Chirpy so Markdown writeups render as full pages."
---

The repo has been rebuilt with Jekyll 4.3 and the Chirpy theme. Every Markdown file you drop in `_posts/` becomes a full post page with SEO, RSS, archives, categories, and tags.

## Add a Markdown writeup
1. Create a file in `_posts/` named `YYYY-MM-DD-title.md`.
2. Include front matter:
   ```yaml
   ---
   layout: post
   title: My New Writeup
   date: 2025-12-26 12:00:00 +0500
   categories: [Writeups]
   tags: [cloud, security, notes]
   ---
   ```
3. Write in plain Markdown. Images go in `assets/img/posts/` and can be referenced as `/assets/img/posts/<file>.png`.

## Preview locally
- Install Ruby 3.1+ and Bundler.
- Run `bundle install` once.
- Start the dev server with `bundle exec jekyll serve` and open http://localhost:4000/.

## Deploy to GitHub Pages
Set `url` and `baseurl` in `_config.yml` to match your repo, commit, and push. Enable GitHub Pages on the `main` branch and GitHub will build the site automatically.

## Troubleshooting
- If Bundler asks for MSYS2 on Windows, run `ridk install` from a Ruby prompt.
- Use `bundle exec jekyll doctor` for quick checks and `bundle exec htmlproofer ./_site` after a build for broken links.
