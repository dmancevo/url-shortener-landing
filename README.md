# URL Shortener API Landing Page

Official landing page and documentation site for URL Shortener API - a self-hosted URL shortener with A/B testing capabilities built in Rust.

This Astro-based website provides:
- Marketing landing page highlighting the self-hosted nature and key features
- Comprehensive documentation with setup instructions and API examples
- SEO-optimized blog with Content Collections
- Modern, responsive design optimized for developers and teams

## Features

- Built with [Astro](https://astro.build/) for fast, static site generation
- Clean, professional design with responsive layout
- SEO-optimized blog with automatic sitemap and RSS feed
- Content Collections for type-safe blog posts
- Comprehensive documentation with code examples
- Easy to customize and extend

## Development

### Prerequisites

- Node.js (v18 or higher)
- npm

### Installation

Install dependencies:

```bash
npm install
```

### Running in Development Mode

Start the Astro development server with hot-reloading:

```bash
npm run dev
```

The site will be available at `http://localhost:4321`

Changes to files will automatically reload the browser.

## Production

### Building for Production

Create an optimized production build:

```bash
npm run build
```

This will:
1. Run type checking with `astro check`
2. Build the site for production
3. Output static files to the `dist/` directory

### Preview Production Build

To preview the production build locally:

```bash
npm run preview
```

The production preview will be available at `http://localhost:4321`

### Deployment

This project is configured for automatic deployment to GitHub Pages with a custom domain.

#### Automated GitHub Pages Deployment

The repository includes a GitHub Actions workflow that automatically builds and deploys the site whenever changes are merged to the `main` branch.

**Setup Steps:**

1. **Enable GitHub Pages in Your Repository**
   - Go to your repository on GitHub
   - Click **Settings** → **Pages** (left sidebar)
   - Under **Source**, select **GitHub Actions**
   - Click **Save**

2. **Configure DNS for Your Custom Domain**

   Add these DNS records at your domain registrar (e.g., Namecheap, GoDaddy, Cloudflare):

   ```
   Type    Name    Value
   ────────────────────────────────────────────────────
   A       www     185.199.108.153
   A       www     185.199.109.153
   A       www     185.199.110.153
   A       www     185.199.111.153
   ```

   **Optional** - Redirect apex domain (url-shortener-api.com → www.url-shortener-api.com):
   ```
   Type     Name    Value
   ───────────────────────────────────────────
   CNAME    @       www.url-shortener-api.com
   ```
   *Or use A records pointing to the same IPs above*

3. **Add Custom Domain to GitHub Pages**
   - In **Settings** → **Pages**
   - Under **Custom domain**, enter: `www.url-shortener-api.com`
   - Click **Save**
   - Wait for DNS check to complete (~5 minutes)
   - Check **Enforce HTTPS** once available

4. **Deploy**

   Simply push or merge changes to the `main` branch:
   ```bash
   git push origin main
   ```

   The GitHub Actions workflow will automatically:
   - Build the site with `npm run build`
   - Deploy to GitHub Pages
   - Make the site available at `https://www.url-shortener-api.com`

   Monitor deployment progress in the **Actions** tab of your repository.

#### Alternative Deployment Options

You can also deploy to other platforms:

- **Netlify**: Drag and drop the `dist/` folder or connect your Git repository
- **Vercel**: Import your repository or deploy via CLI
- **Any static hosting service**: Upload the contents of `dist/`

Example manual deployment to a static server:

```bash
# Build the site
npm run build

# Upload dist/ contents to your web server
rsync -avz dist/ user@server:/var/www/html/
```

## Project Structure

```
/
├── public/           # Static assets (favicon, images)
├── src/
│   ├── content/      # Content Collections
│   │   ├── blog/     # Blog posts (Markdown files)
│   │   └── config.ts # Content schema definitions
│   ├── layouts/      # Page layouts
│   │   └── Layout.astro
│   └── pages/        # Site pages
│       ├── blog/
│       │   ├── [slug].astro  # Dynamic blog post pages
│       │   └── index.astro   # Blog listing page
│       ├── index.astro       # Landing page
│       ├── _docs.astro       # Documentation (excluded from build)
│       └── rss.xml.js        # RSS feed generator
├── astro.config.mjs  # Astro configuration
├── package.json
└── tsconfig.json
```

## Customization

### Updating Content

- **Landing page**: Edit `src/pages/index.astro`
- **Documentation**: Edit `src/pages/docs.astro`
- **Layout/Navigation**: Edit `src/layouts/Layout.astro`

### Styling

Styles are component-scoped and can be found in the `<style>` sections of each `.astro` file. Global styles are defined in `src/layouts/Layout.astro`.

### Configuration

Update site configuration in `astro.config.mjs`:

```js
export default defineConfig({
  site: 'https://url-shortener-api.com',
});
```

## Adding Blog Posts

The site includes a fully SEO-optimized blog powered by Astro Content Collections.

### Creating a New Blog Post

1. Create a new Markdown file in `src/content/blog/` with a descriptive filename:
   ```bash
   src/content/blog/your-post-slug.md
   ```

2. Add frontmatter at the top of the file:
   ```markdown
   ---
   title: "Your Blog Post Title"
   description: "A concise description for SEO (recommended: 120-160 characters)"
   pubDate: 2025-01-25
   author: "URL Shortener Team"
   tags: ["url-shortening", "analytics", "marketing"]
   draft: false
   ---

   Your markdown content starts here...
   ```

3. Write your content using standard Markdown syntax

4. Save the file - it will automatically appear in the blog listing at `/blog`

### Frontmatter Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | string | ✓ | Post title (used in `<h1>` and meta tags) |
| `description` | string | ✓ | SEO description (appears in search results) |
| `pubDate` | date | ✓ | Publication date (YYYY-MM-DD format) |
| `updatedDate` | date |  | Last updated date (shows "Updated on..." note) |
| `author` | string |  | Author name (defaults to "URL Shortener Team") |
| `tags` | array |  | Post tags/categories for organization |
| `image` | object |  | Featured image: `{ url: "/path.jpg", alt: "Description" }` |
| `draft` | boolean |  | Set to `true` to hide from production (defaults to `false`) |

### Markdown Features

The blog supports all standard Markdown features:

- **Headings**: `## H2`, `### H3`, `#### H4`
- **Lists**: Bulleted and numbered lists
- **Links**: `[text](url)`
- **Images**: `![alt text](/image.jpg)`
- **Code blocks**: Use triple backticks with language
- **Inline code**: Use single backticks
- **Blockquotes**: Start lines with `>`

### Example Post

```markdown
---
title: "5 Ways to Optimize Your Short Links"
description: "Learn proven strategies to increase click-through rates and maximize the impact of your shortened URLs."
pubDate: 2025-01-25
author: "URL Shortener Team"
tags: ["best-practices", "optimization", "analytics"]
draft: false
---

Short links are powerful, but optimizing them can dramatically improve your results...

## 1. Use Custom Slugs

Instead of random codes like `abc123`, create memorable slugs:
- `yourbrand.com/summer-sale`
- `yourbrand.com/free-trial`

## 2. Test Your Destinations

Use A/B testing to compare landing pages and find what converts best.
```

### SEO Features

Every blog post automatically includes:

- **JSON-LD structured data** for rich search results
- **Open Graph tags** for social media sharing
- **Twitter Card tags** for Twitter/X previews
- **Canonical URLs** to avoid duplicate content
- **Reading time calculation**
- **Automatic sitemap generation**
- **RSS feed** at `/rss.xml`

### Testing Locally

After creating a post, run the dev server to preview:

```bash
npm run dev
```

Visit:
- `http://localhost:4321/blog` - Blog listing
- `http://localhost:4321/blog/your-post-slug` - Individual post

### Publishing

1. Ensure `draft: false` in the frontmatter
2. Build the site: `npm run build`
3. Deploy the `dist/` folder to your hosting provider

The blog post will automatically appear in:
- Blog listing page
- RSS feed (`/rss.xml`)
- Sitemap (`/sitemap-index.xml`)

## License

See the main URL Shortener project for license information.
