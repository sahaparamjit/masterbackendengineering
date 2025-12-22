# Master Backend Engineering Blog System Documentation

This document provides comprehensive documentation for the markdown-based blog system implemented for the Master Backend Engineering website. It consolidates architecture details, setup instructions, management guides, and implementation summaries.

## Table of Contents
1. [Overview](#overview)
2. [Architecture](#architecture)
3. [Quick Start: Adding a New Post](#quick-start-adding-a-new-post)
4. [Detailed Management Guide](#detailed-management-guide)
5. [Implementation Details](#implementation-details)
6. [Troubleshooting & FAQ](#troubleshooting--faq)

---

## 1. Overview

The blog system is a lightweight, client-side solution that renders Markdown files directly in the browser. It is designed to be:
- **Static**: Hosted entirely on GitHub Pages with no backend server.
- **Maintainable**: Metadata (title, date, tags) lives directly inside the Markdown files using YAML frontmatter.
- **Fast**: Uses `marked.js` for client-side parsing and rendering.
- **SEO-Friendly**: Dynamic title and meta description updates.

---

## 2. Architecture

### Project Structure
```
masterbackendengineering/
├── index.html                          # Main homepage (links to Blog)
├── blog.html                           # Blog listing page
├── blog-post.html                      # Blog post viewer
├── blogs/                              # Content folder containing .md files
│   ├── designing-scalable-systems.md
│   ├── java-spring-boot-performance.md
│   └── senior-engineer-skills.md
└── docs/
    └── blog/                           # This documentation
```

### Data Flow

1.  **Blog Listing (`blog.html`)**:
    *   Loads the list of registered markdown files.
    *   Fetches each file and parses the **YAML Frontmatter** to extract metadata (title, date, excerpt, tags).
    *   Renders a card grid of all posts, sorted by date (newest first).

2.  **Blog Post Viewer (`blog-post.html`)**:
    *   Reads the `?id=post-id` query parameter from the URL.
    *   Scans the registered markdown files to find one with a matching `id` in its frontmatter.
    *   Fetches the content, separates metadata from body, and uses `marked.js` to render the Markdown as HTML.

---

## 3. Quick Start: Adding a New Post

Adding a post requires just two steps.

### Step 1: Create the Markdown File
Create a new file in the `blogs/` directory (e.g., `blogs/my-new-post.md`). It **must** start with the following YAML frontmatter:

```markdown
---
id: my-new-post
title: The Title of My Post
excerpt: A brief 1-2 sentence summary for the listing page.
date: 2025-01-22
author: Paramjit Saha
tags:
  - Backend
  - Architecture
readTime: 5
---

# The Title of My Post

Write your content here using standard Markdown...
```

### Step 2: Register the File
Open **`blog.html`** AND **`blog-post.html`** and add your new file path to the `markdownFiles` array (found around line 150 in `blog.html` and line 290 in `blog-post.html`).

```javascript
const markdownFiles = [
  'blogs/designing-scalable-systems.md',
  'blogs/java-spring-boot-performance.md',
  'blogs/senior-engineer-skills.md',
  'blogs/my-new-post.md'  // <--- Add your new file here
];
```

*Note: In a future update, this list could be moved to a single `index.json` file to avoid editing HTML.*

### Step 3: Publish
Commit and push your changes to the `main` branch.
```bash
git add blogs/my-new-post.md blog.html blog-post.html
git commit -m "feat: add new blog post"
git push origin main
```

---

## 4. Detailed Management Guide

### YAML Frontmatter Reference
Every post must include these fields:

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | **Unique** URL-friendly identifier (kebab-case). Matches the URL `?id=...`. |
| `title` | string | The headline of the post. |
| `excerpt` | string | Short summary shown on the card in the blog list. |
| `date` | string | Publication date in `YYYY-MM-DD` format. |
| `author` | string | Name of the author. |
| `tags` | array | List of related topics. Use YAML list syntax (`- Tag`). |
| `readTime` | number | Estimated reading time in minutes (~200 words/min). |

### Markdown Features
The system uses `marked.js`, supporting:
- **Headings**: `#`, `##`, `###`
- **Formatting**: `**bold**`, `*italic*`, `> blockquote`
- **Lists**: Ordered (`1.`) and unordered (`-`)
- **Code**: Inline and fenced code blocks with language hints (e.g., ` ```java `)
- **Links & Images**: `[text](url)` and `![alt](url)`
- **Tables**: Standard Markdown table syntax

### Styling
Content styling is handled by the `.blog-content` CSS class in `blog-post.html`. It provides:
- Responsive typography matching the site's theme.
- Dark backgrounds for code blocks.
- Accent colors (#06b6d4) for links and highlights.

---

## 5. Implementation Details

### Core Technologies
- **HTML5/CSS3**: Semantic markup and Tailwind CSS for styling.
- **JavaScript (ES6+)**: Handles fetching, parsing, and DOM manipulation.
- **marked.js**: Third-party library for Markdown-to-HTML conversion.

### Key Refactoring (Move to Frontmatter)
Initially, metadata was hardcoded in JavaScript arrays. This was refactored to use **YAML Frontmatter**, making the system scalable. Now, the "source of truth" for a post's metadata is the file itself, not a central registry.

### Parsing Logic
A custom `parseFrontmatter` function was implemented to:
1.  Detect the `---` delimiters at the start of the file.
2.  Extract key-value pairs.
3.  Handle YAML list syntax for `tags`.
4.  Separate the metadata object from the content body.

### Deployment Configuration
- A `.nojekyll` file was added to the root. This tells GitHub Pages NOT to use Jekyll processing, ensuring that folders like `blogs/` (which might be ignored by Jekyll rules) are served correctly as static assets.
- Path resolution logic in the JavaScript was improved to handle relative paths and absolute URLs robustly, preventing 404 errors on deployment.

---

## 6. Troubleshooting & FAQ

**Q: My post isn't showing up.**
A: Check the console for 404 errors. Ensure you added the *exact* file path to the `markdownFiles` array in both HTML files.

**Q: The content is empty or formatting is broken.**
A: Ensure your YAML frontmatter is valid (no tabs, proper indentation) and that the `---` separators are on their own lines.

**Q: Can I use HTML inside the Markdown?**
A: Yes, `marked.js` supports raw HTML, so you can embed YouTube videos or complex layouts if needed.

**Q: How do I change the sorting?**
A: In `blog.html`, modify the `sort` function:
```javascript
// Current: Newest first
blogPosts.sort((a, b) => new Date(b.date) - new Date(a.date));
```

**Q: How do I track analytics?**
A: Google Analytics 4 is integrated. Page views are tracked automatically. Custom event tracking for specific posts can be added to the loading logic in `blog-post.html`.
