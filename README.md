# Garnett Enterprises Website

A professional business acquisition company website built with modern HTML, CSS, and JavaScript.

## Overview

Garnett Enterprises specializes in acquiring and growing small to medium-sized businesses with a strategic, hands-on approach.

## Tech Stack

- **HTML5** - Semantic markup
- **CSS3** - Custom properties, Grid, Flexbox
- **JavaScript** - Vanilla JS for interactions
- **Fonts** - Google Fonts (Playfair Display, Source Sans 3)
- **Hosting** - Netlify-ready with form handling

## Features

- Responsive design (mobile-first)
- Netlify Forms integration for contact form
- SEO optimized with meta tags
- Smooth scroll navigation
- Sticky header with scroll effect
- Clean, professional design

## Project Structure

```
garnett-enterprises/
├── index.html          # Homepage
├── about.html          # About/Team page
├── css/
│   └── styles.css      # Main stylesheet
├── assets/             # Images and media (add your own)
├── netlify.toml        # Netlify configuration
└── README.md           # This file
```

## Deployment

### Netlify (Recommended)

1. Push to GitHub
2. Connect repo to Netlify
3. Deploy settings:
   - Build command: (none needed)
   - Publish directory: `.`

### Local Development

Simply open `index.html` in a browser, or use a local server:

```bash
# Using Python
python -m http.server 8000

# Using Node.js
npx serve
```

## Customization

### Adding Team Photos

Replace the placeholder images in `about.html` with actual team photos:

1. Add photos to `assets/` folder
2. Update the `src` attributes in `about.html`
3. Recommended size: 600x800px for team photos

### Hero Image

The hero uses an Unsplash image. To use a custom image:

1. Add image to `assets/` folder
2. Update `src` in `index.html` hero section

### Colors

Edit CSS variables in `css/styles.css`:

```css
:root {
  --color-primary: #1a1a1a;
  --color-secondary: #2563eb;
  --color-accent: #0066a1;
  --color-bg-gray: #e8e8e8;
  --color-bg-blue: #8eb4d1;
}
```

## Forms

Contact forms are configured for Netlify Forms. Submissions will appear in your Netlify dashboard under Forms.

## License

© 2025 Garnett Enterprises. All rights reserved.
