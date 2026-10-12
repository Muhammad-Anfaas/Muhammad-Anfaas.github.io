---
date: '2026-03-04T09:24:37+05:00'
draft: false
title: 'From Zero to Hugo'
featureimage: "hugo-feature.png"

heroStyle: "background"

showTableOfContents: true
---
# From Zero to Hugo: Creating and Deploying a Blog with GitHub Actions

## Static Site Blogging Made Easy

Hugo is one of the fastest static site generators available. Combined with **GitHub Pages** and **GitHub Actions**, you can create a fully automated blogging workflow.

This guide walks you through creating, deploying, and automating your Hugo blog.

---

## Complete Guide: Hugo Blog Setup and Deployment

This guide covers:

- Installing Hugo
- Creating a site
- Adding a theme
- Creating content
- Local preview
- Deploying to GitHub Pages
- Automating deployment with GitHub Actions

---

## 📌 Requirements

Before starting, make sure you have:

- **Hugo installed** (Linux, macOS, or Windows)
- **Git installed**
- A **GitHub account**
- A Hugo theme (PaperMod recommended)
- Basic **Markdown knowledge**

---

## Step 1 — Install Hugo

### On Linux

```bash
sudo apt install hugo
```

Verify installation:

```bash
hugo version
```

---

## Step 2 — Create a New Hugo Site

Create a new site:

```bash
hugo new site myblog
cd myblog
```

Your project structure will look like this:

```text
myblog/
├── archetypes/
├── content/
├── layouts/
├── themes/
└── config.toml
```

---

## Step 3 — Add a Theme

In this example we will use **PaperMod**.

Initialize Git and add the theme:

```bash
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod themes/PaperMod
```

Update your **config.toml**:

```toml
theme = "PaperMod"
```

---

## Step 4 — Create Content

Create your first blog post:

```bash
hugo new posts/my-first-post.md
```

Edit the file:

```
content/posts/my-first-post.md
```

Example content:

```markdown
---
title: "My First Post"
date: 2026-02-19
draft: false
---

Welcome to my Hugo blog!
```

---

## Step 5 — Build and Preview Locally

Generate the static site:

```bash
hugo
```

Preview locally:

```bash
hugo server -D
```

Open your browser and visit:

```
http://localhost:1313
```

---

## Step 6 — Push Your Site to GitHub

Create a new GitHub repository (for example `username.github.io`) and push your code.

```bash
git remote add origin https://github.com/username/username.github.io.git
git add .
git commit -m "Initial Hugo site"
git push -u origin main
```

Make sure the repository is **public** for GitHub Pages.

---

## Step 7 — Configure GitHub Pages

1. Go to **Repository Settings**
2. Click **Pages**
3. Select:

```
Branch: main
Folder: /root
```

4. Save.

Your site will be available at:

```
https://username.github.io
```

---

## Step 8 — Automate Deployment with GitHub Actions

Create the workflow file:

```
.github/workflows/hugo-deploy.yml
```

Add the following configuration:

```yaml
name: Hugo Deploy

...
```

Commit and push the workflow:

```bash
git add .github/workflows/hugo-deploy.yml
git commit -m "Add GitHub Actions workflow for Hugo"
git push
```

Now **every push to the `main` branch automatically builds and deploys your site**.

---

## Step 9 — Workflow Overview

```
Write Post
     │
     ▼
Git Push to GitHub
     │
     ▼
GitHub Actions Triggered
     │
     ▼
Hugo Builds Static Site
     │
     ▼
Site Deployed to GitHub Pages
```

---

## Conclusion

You now have a fully automated Hugo blogging workflow.