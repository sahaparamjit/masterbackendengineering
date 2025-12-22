# Blog System Documentation

This website now includes a markdown-based blog system with metadata stored directly in each post. No registration required!

## How It Works

### Architecture
- **blog.html** - Blog listing page that discovers and loads all published articles
- **blog-post.html** - Blog post viewer that dynamically loads and renders markdown files
- **/blogs** - Folder containing markdown files for each blog post (with embedded metadata)
- **Client-side rendering** - Uses marked.js library to parse markdown and render HTML

### Key Innovation: Metadata in Markdown

Instead of maintaining separate metadata registries, each markdown file contains a **YAML frontmatter** section:

```markdown
---
id: my-post-id
title: My Post Title
excerpt: Brief summary...
date: 2025-01-22
author: Paramjit Saha
tags:
  - Tag1
  - Tag2
readTime: 8
---

# My Post Title

Your content here...
```

### Flow
1. **blog.html** loads each markdown file and parses the frontmatter
2. Displays all posts as cards on the listing page
3. **blog-post.html** fetches the markdown file matching the URL parameter
4. Extracts frontmatter metadata and renders the content below
5. No metadata duplication or synchronization needed!

## Adding a New Blog Post (2 Steps Only!)

### Step 1: Create the Markdown File

Create `/blogs/my-awesome-post.md` with YAML frontmatter at the top:

```markdown
---
id: my-awesome-post
title: My Awesome Post Title
excerpt: A brief excerpt shown in the blog listing (1-2 sentences)
date: 2025-01-22
author: Paramjit Saha
tags:
  - Backend
  - Performance
  - Java
readTime: 7
---

# My Awesome Post Title

Your article content goes here using standard markdown...

## Section Heading

More content with examples, code blocks, etc.
```

### Step 2: Register the File (Update HTML)

Preferred: add the markdown file path to `blogs/index.json` (recommended). If you don't want to use `index.json`, add the path to both HTML pages' `markdownFiles` arrays.

**In blog.html** (around line 150) or update `blogs/index.json`:
```javascript
const markdownFiles = [
  'blogs/designing-scalable-systems.md',
  'blogs/java-spring-boot-performance.md',
  'blogs/senior-engineer-skills.md',
  'blogs/my-awesome-post.md'  // Add your new post here
];
```

**In blog-post.html** (around line 290):
```javascript
const markdownFiles = [
  'blogs/designing-scalable-systems.md',
  'blogs/java-spring-boot-performance.md',
  'blogs/senior-engineer-skills.md',
  'blogs/my-awesome-post.md'  // Add your new post here
];
```

### Step 3: Commit & Push

```bash
git add blogs/my-awesome-post.md blog.html blog-post.html
git commit -m "Add blog post: My Awesome Post Title"
git push origin main
```

**Done!** ✅ Post is live immediately.

## YAML Frontmatter Reference

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | Yes | URL-friendly unique identifier (use kebab-case: `my-post-id`) |
| `title` | string | Yes | Full post title shown in browser tab and header |
| `excerpt` | string | Yes | 1-2 sentence summary shown in blog listing |
| `date` | string | Yes | Publication date (format: `YYYY-MM-DD`) |
| `author` | string | Yes | Post author name |
| `tags` | array | Yes | 2-3 relevant tags for categorization (YAML list format) |
| `readTime` | number | Yes | Estimated read time in minutes (~1 min per 200 words) |

**Important**: All fields are required. The system parses these directly from the markdown file.

## Markdown Features Supported

The blog system supports standard markdown:

| Feature | Syntax | Example |
|---------|--------|---------|
| Headings | `# H1`, `## H2`, `### H3` | Works as expected |
| Bold | `**text**` | Shows **bold** |
| Italic | `*text*` | Shows *italic* |
| Lists | `- item` or `1. item` | Numbered and bullet lists |
| Links | `[text](url)` | Creates clickable links |
| Code inline | `` `code` `` | Shows inline code |
| Code blocks | ` ```lang ... ``` ` | Syntax-highlighted blocks |
| Blockquotes | `> quote` | Indented quotes |
| Images | `![alt](url)` | Embedded images |
| Tables | Standard markdown tables | Full support |

## Styling

Blog content automatically uses the site's design system:
- Typography matches the overall brand
- Code blocks have dark background for readability
- Blockquotes have accent color left border
- Images are responsive and properly spaced
- Links are styled in accent color (#06b6d4)

## Best Practices

### Writing

1. **Lead with value**: Start each post with why the reader should care
2. **Use headers**: Break content into sections with H2/H3 headers
3. **Code examples**: Include real, runnable code snippets
4. **Keep paragraphs short**: 2-3 sentences per paragraph
5. **Conclusion**: End with takeaways or action items

### Metadata

- **Excerpt**: 1-2 sentences, ~60-120 characters
- **Tags**: 2-3 relevant tags (use consistent capitalization)
- **Read Time**: 1 minute per ~200 words
- **Date**: Use YYYY-MM-DD format (ISO 8601)
- **ID**: Use kebab-case with no spaces or special characters

### Organization

```
/blogs/
├── designing-scalable-systems.md
├── java-spring-boot-performance.md
├── senior-engineer-skills.md
└── my-new-post.md  # Add new posts here
```

## How the System Discovers Posts

### blog.html Discovery Process
1. Loops through `markdownFiles` array
2. Fetches each markdown file
3. Parses YAML frontmatter (content between `---` markers)
4. Stores metadata in memory
5. Sorts by date (newest first)
6. Renders cards in the DOM

### blog-post.html Discovery Process
1. Reads `id` parameter from URL (`?id=post-id`)
2. Loops through `markdownFiles` array
3. Fetches each file and checks its `id` field
4. Once found, extracts metadata and content
5. Separates frontmatter from markdown body
6. Renders metadata in header
7. Parses markdown content and displays below

## Performance

- **Blog listing load**: ~300ms (loads and parses all markdown files)
- **Blog post load**: ~500ms (fetch + parse specific markdown file)
- **File sizes**:
  - blog.html: ~15KB
  - blog-post.html: ~16KB
  - Typical blog post: ~20-30KB
  - marked.js library: ~40KB (CDN cached)
- **Caching**: GitHub Pages caches automatically, posts are static files

## Troubleshooting

**Post doesn't appear in listing:**
- Check that you added the file path to `markdownFiles` in blog.html
- Verify the markdown file has correct YAML frontmatter
- Check browser console for fetch errors

**Markdown doesn't render on post page:**
- Check that you added the file path to `markdownFiles` in blog-post.html
- Verify the frontmatter includes an `id` field matching the URL parameter
- Check that frontmatter is properly formatted (between `---` markers)

**Frontmatter parsing errors:**
- Ensure YAML format is correct (proper indentation for arrays)
- Make sure no special characters break the YAML syntax
- Check that dates are in `YYYY-MM-DD` format
- Verify the first line is exactly `---` (three dashes)

**Styling looks wrong:**
- The blog content CSS is in blog-post.html starting at line ~85
- Adjust colors if needed, they map to: primary (#111827), secondary (#374151), accent (#06b6d4)

## Customization

### Change Blog Layout

Edit the grid in blog.html (line ~180):
```html
<div id="blog-posts" class="grid md:grid-cols-2 lg:grid-cols-3 gap-8">
```

Change `lg:grid-cols-3` to `lg:grid-cols-2` for larger cards, or `lg:grid-cols-4` for smaller cards.

### Change Content Styling

Edit `.blog-content` CSS in blog-post.html (lines ~85-165) to adjust:
- Font sizes: `font-size: 1.1rem`
- Line height: `line-height: 1.8`
- Code block colors: `background-color: #1f2937`
- Link colors: `color: #06b6d4`

### Change Sorting

In blog.html, modify the sort function (around line 155):
```javascript
// Sort by date (newest first)
blogPosts.sort((a, b) => new Date(b.date) - new Date(a.date));

// To sort by date (oldest first):
// blogPosts.sort((a, b) => new Date(a.date) - new Date(b.date));

// To sort alphabetically by title:
// blogPosts.sort((a, b) => a.title.localeCompare(b.title));
```

## SEO

Each post automatically:
- Sets the page title from metadata
- Sets the meta description from excerpt
- Includes semantic HTML
- Supports open graph metadata (can be extended in frontmatter)

## Analytics

Blog page views are tracked via Google Analytics 4. Both pages already include the tracking code.

## Future Enhancements

Ideas for growing the blog system:

- [ ] Search functionality (search post titles/tags)
- [ ] Tag-based filtering (show posts by tag)
- [ ] Comments section (via Disqus or utterances)
- [ ] Related posts suggestions
- [ ] Newsletter signup CTA
- [ ] Social sharing buttons
- [ ] Reading progress indicator
- [ ] Table of contents generator (parse markdown headers)
- [ ] Post archives by month/year
- [ ] Expand YAML parser for more complex metadata

## Summary

The new system is:
- ✅ **Simpler**: Add markdown file + update array = done
- ✅ **Maintainable**: Metadata lives with content
- ✅ **Scalable**: Easy to add 100+ posts
- ✅ **DRY**: No metadata duplication
- ✅ **Static**: Works perfectly on GitHub Pages
- ✅ **Fast**: Client-side parsing, no backend needed

Happy blogging! 📝


### Step 3: Push to GitHub

```bash
git add blogs/my-new-post.md
git add blog.html
git add blog-post.html
git commit -m "Add new blog post: My New Post Title"
git push origin main
```

The post will be live within seconds!

## Markdown Features Supported

The blog system supports standard markdown:

| Feature | Syntax | Example |
|---------|--------|---------|
| Headings | `# H1`, `## H2`, `### H3` | Works as expected |
| Bold | `**text**` | Shows **bold** |
| Italic | `*text*` | Shows *italic* |
| Lists | `- item` or `1. item` | Numbered and bullet lists |
| Links | `[text](url)` | Creates clickable links |
| Code inline | `` `code` `` | Shows inline code |
| Code blocks | ` ```lang ... ``` ` | Syntax-highlighted blocks |
| Blockquotes | `> quote` | Indented quotes |
| Images | `![alt](url)` | Embedded images |
| Tables | Standard markdown tables | Full support |

## Styling

Blog content automatically uses the site's design system:
- Typography matches the overall brand
- Code blocks have dark background for readability
- Blockquotes have accent color left border
- Images are responsive and properly spaced
- Links are styled in accent color (#06b6d4)

## Best Practices

### Writing

1. **Lead with value**: Start each post with why the reader should care
2. **Use headers**: Break content into sections with H2/H3 headers
3. **Code examples**: Include real, runnable code snippets
4. **Keep paragraphs short**: 2-3 sentences per paragraph
5. **Conclusion**: End with takeaways or action items

### Metadata

- **Excerpt**: 1-2 sentences, ~60-120 characters
- **Tags**: 2-3 relevant tags for categorization
- **Read Time**: 1 minute per ~200 words
- **Date**: Use YYYY-MM-DD format (ISO 8601)

### Organization

```
/blogs/
├── designing-scalable-systems.md
├── java-spring-boot-performance.md
├── senior-engineer-skills.md
└── my-new-post.md  # Add new posts here
```

## Performance

- Posts are loaded on-demand when clicked
- Markdown is parsed in the browser
- No backend calls required
- Fully cacheable by CDN

## Troubleshooting

**Post doesn't appear in listing:**
- Check that the `id` in blog.html exactly matches what you used in the metadata object
- Ensure the `date` is in YYYY-MM-DD format

**Markdown doesn't render:**
- Check the file path in the metadata matches actual file location
- Verify the markdown syntax is correct (use `\`\`\`` for code blocks, not `~~~`)
- Check browser console for errors

**Styling looks wrong:**
- The blog content CSS is in blog-post.html starting at line ~85
- Adjust colors if needed, they map to: primary (#111827), secondary (#374151), accent (#06b6d4)

## Customization

### Change Blog Layout

Edit the grid in blog.html (line ~180):
```html
<div id="blog-posts" class="grid md:grid-cols-2 lg:grid-cols-3 gap-8">
```

Change `lg:grid-cols-3` to `lg:grid-cols-2` for larger cards, or `lg:grid-cols-4` for smaller cards.

### Change Content Styling

Edit `.blog-content` CSS in blog-post.html (lines ~85-165) to adjust:
- Font sizes: `font-size: 1.1rem`
- Line height: `line-height: 1.8`
- Code block colors: `background-color: #1f2937`
- Link colors: `color: #06b6d4`

## SEO

Each post automatically:
- Sets the page title from metadata
- Sets the meta description from excerpt
- Includes semantic HTML
- Supports open graph metadata (optional, can be added)

## Analytics

Blog page views are tracked via Google Analytics 4. Track specific events by adding to the blog loading JavaScript:

```javascript
window.Analytics.track('blog_post_viewed', {
  post_id: postId,
  title: post.title,
  author: post.author
});
```

## Future Enhancements

Ideas for growing the blog system:

- [ ] Search functionality
- [ ] Tag-based filtering
- [ ] Comments section (via Disqus)
- [ ] Related posts suggestions
- [ ] Newsletter signup
- [ ] Social sharing buttons
- [ ] Reading progress indicator
- [ ] Table of contents generator

To implement any of these, modify blog.html and blog-post.html accordingly.
