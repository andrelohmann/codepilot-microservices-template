---
description: "Scaffold an Astro SSG service"
mode: agent
tools: [file, workspace, terminal]
---

# Scaffold Astro SSG Service

Create a production-ready `${input:serviceName}` static site using Astro with zero JavaScript, Content Collections, and Tailwind CSS.

## Reference Documentation

- **[Astro SSG Tech-Stack Guide](../../../../docs/tech-stacks/ssg/astro.md)** - Complete implementation details
- **[Astro Instructions](../instructions/service-ssg-astro.instructions.md)** - Copilot guidance

## Service Configuration

Ask the user for:
- **Service Name**: `${input:serviceName}` (e.g., `ssg-astro-marketing`, `ssg-astro-docs`)
- **Purpose**: `${input:servicePurpose}`
- **Port**: `${input:port:3000}`
- **Content Types**: `${input:contentTypes:products,blog}` (comma-separated)

## Project Structure

```
services/${input:serviceName}/
├── src/
│   ├── pages/              # File-based routing
│   ├── content/            # Content Collections
│   │   ├── config.ts
│   │   └── ${contentType}/
│   ├── components/
│   ├── layouts/
│   └── styles/
├── public/
├── astro.config.mjs
├── tailwind.config.mjs
└── README.md
```

## Implementation Steps

1. **Initialize Astro Project**
   - Create directory in `services/`
   - Initialize with `npm create astro@latest`
   - Install Tailwind CSS integration

2. **Configure Astro**
   - `astro.config.mjs` - Configure integrations (@astrojs/tailwind, @astrojs/sitemap, @astrojs/mdx)
   - Enable Content Collections
   - Configure image optimization

3. **Content Collections Setup**
   - `src/content/config.ts` - Define schemas for each content type
   - Create collection directories in `src/content/`
   - Add sample markdown files with frontmatter

4. **Pages (File-based Routing)**
   - `src/pages/index.astro` - Homepage
   - `src/pages/${contentType}/[slug].astro` - Dynamic pages
   - Use `getCollection()` and `getStaticPaths()`

5. **Layouts**
   - `src/layouts/BaseLayout.astro` - Base HTML structure
   - Include SEO meta tags, Tailwind CSS
   - Header and footer slots

6. **Components**
   - `src/components/Header.astro`
   - `src/components/Footer.astro`
   - `src/components/Card.astro` (for content items)
   - Use Astro's scoped styles

7. **Styling**
   - Configure Tailwind CSS
   - `src/styles/global.css` - Global styles
   - Use utility classes in components

8. **Forms Integration**
   - Contact form that POSTs to REST API
   - Client-side validation (optional island)

9. **Image Optimization**
   - Use `<Image />` component from @astrojs/image
   - Configure formats and sizes

10. **Docker Configuration**
    - `Dockerfile` - Multi-stage Node build + Nginx
    - Build static output to `/dist`
    - Serve with Nginx

11. **Development Configuration**
    - `.devcontainer/` - VS Code devcontainer
    - `.env.example` - Environment template

12. **Docker Compose Integration**
    - Update `docker-compose.yml` in project root
    - Add service with ports

13. **Verify Service**
    - Start service: `docker compose up ${input:serviceName}`
    - Check site at `http://localhost:${input:port}`
    - Verify Lighthouse score (should be 95-100)

## Code Templates

All code templates are available in the [Astro SSG Tech-Stack Documentation](../../../../docs/tech-stacks/ssg/astro.md):
- Content Collections schemas
- Dynamic page patterns
- Astro components
- Layout structure
- Form integration

## Completion Checklist

- [ ] Astro project initialized
- [ ] Content Collections configured
- [ ] Pages created with file-based routing
- [ ] Layouts and components implemented
- [ ] Tailwind CSS configured
- [ ] Forms integrated
- [ ] Images optimized
- [ ] Docker builds successfully
- [ ] Static output generated
- [ ] Site accessible at port
- [ ] Lighthouse score 95-100

## Post-Scaffolding

After scaffolding completes:
1. Build site: `docker compose exec ${input:serviceName} npm run build`
2. Check output in `/dist`
3. Test all pages and forms
4. Refer to [instructions file](../instructions/service-ssg-astro.instructions.md) for Copilot coding guidance

---

**Note**: This prompt creates the scaffolding only. Refer to the [complete tech-stack documentation](../../../../docs/tech-stacks/ssg/astro.md) for detailed implementation patterns, best practices, and code examples.
