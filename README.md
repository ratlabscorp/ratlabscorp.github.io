# Rat Labs

> "Don't be a rat."

Independent research in software, computing systems, and artificial intelligence.

**[ratlabscorp.github.io](https://ratlabscorp.github.io)**

---

## About

This repository contains the source code for the Rat Labs website, built with [Hugo](https://gohugo.io) using a custom theme (`ratlabs`).

## Development

### Prerequisites

- [Hugo Extended](https://gohugo.io/installation/) v0.128.0 or later

### Running locally

```bash
hugo server --buildDrafts
```

The site will be available at `http://localhost:1313`.

### Building for production

```bash
hugo --minify
```

Output goes to `public/`.

---

## Content

### Adding a blog post

```bash
hugo new content blog/my-post-title.md
```

### Adding a project

```bash
hugo new content projects/project-name.md
```

Frontmatter:

```yaml
title: "Project Name"
status: "Active"       # Active | Paused | Archived
description: ""
contributors: []
links:
  - label: "GitHub"
    url: ""
```

### Adding a member

```bash
hugo new content members/handle.md
```

### Adding a reading list item

```bash
hugo new content reading/book-or-paper-title.md
```

Frontmatter:

```yaml
title: "Title"
author: ""
category: "Systems"   # Systems | Programming Languages | Operating Systems | AI | Machine Learning | Distributed Systems | Security | Mathematics
link: ""
status: "Reading"     # Reading | Finished | Recommended | On Queue
note: ""
```

---

## Theme

The custom `ratlabs` Hugo theme is located at `themes/ratlabs/`.

**Design principles:**
- Typography-first (Source Serif 4 body, Libre Baskerville headings, JetBrains Mono for code/UI)
- Ink blue (`#1a56db`) as the single accent color
- No external dependencies beyond Google Fonts
- Dark mode toggle with `localStorage` persistence and system preference detection
- Zero JavaScript beyond the dark mode toggle and mobile nav hamburger

**Signature element:** The three-rule typographic treatment under the homepage wordmark — echoing a laboratory notebook's ruled lines.

---

## Deployment

The site deploys automatically to GitHub Pages via the workflow at `.github/workflows/deploy.yml` on every push to `main`.

To configure: go to **Settings → Pages** in this repository and set the source to **GitHub Actions**.

---

## License

Content: © Rat Labs. All rights reserved.  
Theme code: MIT License.
