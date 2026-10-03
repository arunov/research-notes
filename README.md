# Research Notes — publishing

A Jekyll blog (GitHub Pages-ready) with key concepts from CS research papers.

## Preview locally

```bash
cd research-blog
bundle exec jekyll serve   # needs Ruby + bundler
# or: python3 -m http.server  # serves raw markdown, no theme
```

## Publish to GitHub Pages

1. Create a repo on GitHub (e.g. `research-notes`).
2. Push this directory:
   ```bash
   cd research-blog
   git init && git add . && git commit -m "research notes"
   git remote add origin git@github.com:<you>/research-notes.git
   git push -u origin main
   ```
3. In repo Settings → Pages, set source to "Deploy from a branch", branch `main`, folder `/ (root)`.
4. Your site appears at `https://<you>.github.io/research-notes/` — link it from your webpage.

## Adding a paper

Add `_posts/YYYY-MM-DD-slug.md` with front matter:

```yaml
---
layout: post
title: "Paper title"
date: YYYY-MM-DD
authors: "Authors — venue"
---
```

Then add its concepts to `concepts.md` and the paper list in `index.md`.
