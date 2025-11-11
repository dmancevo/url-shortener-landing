# URL Shortener API Landing Page

Official landing page and documentation site for URL Shortener API - a self-hosted URL shortener with A/B testing capabilities built in Rust.

This Astro-based website provides:
- Marketing landing page highlighting the self-hosted nature and key features
- Comprehensive documentation with setup instructions and API examples
- Modern, responsive design optimized for developers and teams

## Features

- Built with [Astro](https://astro.build/) for fast, static site generation
- Clean, professional design with responsive layout
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

The `dist/` directory contains all static files ready to deploy. You can deploy to:

- **Netlify**: Drag and drop the `dist/` folder or connect your Git repository
- **Vercel**: Import your repository or deploy via CLI
- **GitHub Pages**: Push the `dist/` folder to the `gh-pages` branch
- **Any static hosting service**: Upload the contents of `dist/`

Example deployment to a static server:

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
│   ├── layouts/      # Page layouts
│   │   └── Layout.astro
│   └── pages/        # Site pages
│       ├── index.astro    # Landing page
│       └── docs.astro     # Documentation
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

## License

See the main URL Shortener project for license information.
