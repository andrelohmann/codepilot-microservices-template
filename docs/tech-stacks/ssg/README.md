# Static Site Generators (SSG) - Content-Driven Websites

Build blazing-fast static websites from markdown content with modern component architectures.

---

## Overview

SSG (Static Site Generators) are perfect for content-heavy websites where performance and SEO are critical. They pre-render all pages at build time, resulting in the fastest possible page loads.

---

## Available Technologies

### 1. [Astro + Tailwind](./astro.md) ⭐ **RECOMMENDED**
**Performance:** Zero JS by default  
**Best For:** Corporate websites, marketing sites, documentation (maximum performance)

### 2. [React + Vite + MDX](./react.md)
**Performance:** Fast with React runtime  
**Best For:** Content sites needing more interactivity, React-focused teams

---

## Technology Comparison

| Feature | Astro | React+Vite |
|---------|-------|------------|
| **JavaScript** | Zero by default | React runtime included |
| **Performance** | ⭐⭐⭐⭐⭐ Fastest | ⭐⭐⭐⭐ Fast |
| **Lighthouse** | 95-100 | 90-95 |
| **Interactivity** | Islands only | Full React |
| **Learning Curve** | Easy | Medium |
| **Bundle Size** | Smallest | Larger |
| **Content Management** | Content Collections | MDX |

---

## When to Use SSG

### ✅ Perfect For:
- **Corporate websites** - Company information, about pages
- **Marketing sites** - Product pages, landing pages
- **Documentation** - Technical docs, guides
- **Blogs** - News, articles, posts
- **Portfolios** - Personal/company portfolios
- **Product catalogs** - Static product listings

### ❌ Not For:
- **Admin dashboards** → Use React SPA
- **SaaS applications** → Use React SPA or Next.js
- **Real-time apps** → Use React SPA
- **Mobile apps** → Use Flutter

---

## Quick Decision Guide

```
Need a website? Start here:
│
├─ Is it mostly static content?
│  └─ YES → SSG ✅
│     │
│     ├─ Need maximum performance?
│     │  └─ YES → Use Astro (zero JS by default)
│     │
│     └─ Need heavy client-side interactivity?
│        └─ YES → Use React + Vite
│
└─ Is it a dynamic web app?
   └─ NO → Use React SPA or Next.js
```

---

## Common Features

### Content Management
Both options support:
- ✅ **Markdown/MDX** - Write content in markdown
- ✅ **Frontmatter** - YAML metadata for pages
- ✅ **File-based routing** - Directory structure = URLs
- ✅ **Component embedding** - Use components in markdown
- ✅ **Tailwind CSS** - Utility-first styling

### Build Process
1. Read markdown files
2. Parse frontmatter metadata
3. Generate routes from structure
4. Process components
5. Apply styling
6. Optimize assets
7. Output static HTML/CSS/JS

### Deployment
- Static hosting (Netlify, Vercel, Cloudflare Pages)
- CDN distribution
- nginx/Apache
- S3 + CloudFront
- Docker containers

---

## Content Structure Example

```
content/
├── index.md                    # Homepage → /
├── about/
│   ├── index.md               # About page → /about
│   ├── team.md                # Team page → /about/team
│   └── careers.md             # Careers → /about/careers
├── products/
│   ├── index.md               # Products → /products
│   ├── service-a.md           # Service A → /products/service-a
│   └── service-b.md           # Service B → /products/service-b
├── blog/
│   ├── index.md               # Blog index → /blog
│   ├── 2024-01-15-post-1.md  # Blog post → /blog/2024-01-15-post-1
│   └── 2024-02-20-post-2.md  # Blog post → /blog/2024-02-20-post-2
└── contact.md                  # Contact → /contact
```

---

## Markdown with Frontmatter Example

```markdown
---
title: "About Us - Company Name"
description: "Learn about our mission and team"
layout: "default"
image: "/images/about-hero.jpg"
---

# About Us

We are a team of passionate individuals...

## Our Mission

To deliver exceptional value...
```

---

## Naming Convention

```
ssg-{tech}-{purpose}
```

### Examples
```
ssg-astro-marketing      # Astro marketing site
ssg-astro-docs           # Astro documentation
ssg-astro-corporate      # Astro corporate website
ssg-react-marketing      # React SSG marketing site
ssg-react-docs           # React SSG documentation
ssg-react-blog           # React SSG blog
```

---

## Performance Comparison

### Page Load Times (First Contentful Paint)

| Site Type | Astro | React+Vite | Next.js SSG |
|-----------|-------|------------|-------------|
| **Simple Page** | 0.3s | 0.5s | 0.6s |
| **Complex Page** | 0.5s | 0.8s | 1.0s |
| **With Images** | 0.7s | 1.0s | 1.2s |

### Bundle Sizes

| Technology | HTML | CSS | JS | Total |
|------------|------|-----|----|----- |
| **Astro** | 5 KB | 10 KB | 0-5 KB | **15-20 KB** |
| **React+Vite** | 5 KB | 10 KB | 40 KB | **55 KB** |
| **Next.js** | 5 KB | 10 KB | 80 KB | **95 KB** |

---

## SEO Features

Both technologies provide:
- ✅ **Pre-rendered HTML** - Perfect for crawlers
- ✅ **Meta tags** - From frontmatter
- ✅ **Sitemap generation** - Automatic XML sitemaps
- ✅ **RSS feeds** - For blogs
- ✅ **Semantic HTML** - Proper heading hierarchy
- ✅ **Fast page loads** - Core Web Vitals optimized
- ✅ **Mobile responsive** - Mobile-first design

---

## Testing Strategy

### Content Validation
- Markdown linting (markdownlint)
- Broken link checking
- Frontmatter schema validation

### Component Testing
- Unit tests for components
- Visual regression testing

### E2E Testing
- Navigation flows (Playwright)
- Form submissions
- Responsive design testing

---

## Related Documentation

- [Astro Documentation](./astro.md) - Detailed Astro guide
- [React SSG Documentation](./react.md) - Detailed React guide
- [Comparison Guide](./COMPARISON.md) - Detailed comparison
- [Frontends](../frontends/README.md) - For dynamic apps
- [Naming Conventions](../NAMING-CONVENTIONS.md)

---

## Next Steps

1. **Choose technology**: Astro (performance) or React (interactivity)
2. **Review detailed docs**: Check technology-specific documentation
3. **Plan content structure**: Map out pages and hierarchy
4. **Follow naming conventions**: Use `ssg-{tech}-{purpose}` pattern
5. **Set up development environment**: DevContainer
6. **Create content**: Write markdown files
7. **Build components**: Create reusable UI components
8. **Deploy**: Static hosting platform
