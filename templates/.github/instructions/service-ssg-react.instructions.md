---
applyTo: "**/ssg-react-*/**"
description: Static site generator with React, Vite, MDX, and Tailwind
---

# SSG React - Static Site Generator

Build static websites from markdown content with React components.

## Naming Convention

`ssg-react-{purpose}` (e.g., `ssg-react-docs`, `ssg-react-blog`, `ssg-react-marketing`)

## Technology Stack

- React 18+ (components)
- TypeScript (type safety)
- Vite + vite-plugin-static-routes (SSG)
- Tailwind CSS (styling)
- MDX (markdown + React)
- React Router (navigation)
- Gray Matter (frontmatter)
- Remark/Rehype (markdown processing)

## Directory Structure

```
services/ssg-react-{purpose}/
├── content/              # Markdown files = pages
│   ├── index.md
│   ├── about.md
│   └── docs/
│       └── guide.md
├── src/
│   ├── components/      # React components
│   │   ├── Layout.tsx
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   └── ContactForm.tsx
│   ├── pages/
│   │   └── [...slug].tsx
│   ├── lib/
│   │   ├── markdown.ts
│   │   └── api.ts
│   └── styles/
│       └── globals.css
├── public/
├── Dockerfile
├── package.json
├── vite.config.ts
└── tailwind.config.ts
```

## Content Structure

Markdown files define hierarchy:
```
content/
├── index.md              → /
├── about.md              → /about
└── products/
    ├── index.md          → /products
    └── service-a.md      → /products/service-a
```

Frontmatter metadata:
```markdown
---
title: "Page Title"
description: "SEO description"
layout: "default"
date: "2025-01-07"
---

# Content
```

## React Components in Markdown

Use MDX to embed components:
```markdown
<InfoBox type="warning">
  Important information
</InfoBox>

<CodeBlock language="bash">
npm install
</CodeBlock>

<ContactForm 
  apiEndpoint="https://api.example.com/contact"
  fields={["name", "email", "message"]}
/>
```

## Vite Configuration

```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import mdx from '@mdx-js/rollup';

export default defineConfig({
  plugins: [
    react(),
    mdx({
      remarkPlugins: [remarkGfm, remarkFrontmatter],
      rehypePlugins: [rehypePrism, rehypeSlug]
    })
  ],
  build: {
    outDir: 'dist',
    emptyOutDir: true
  }
});
```

## Form Integration

Forms POST to REST APIs:
```tsx
<form action="https://api.example.com/contact" method="POST">
  <input name="email" type="email" required />
  <button type="submit">Submit</button>
</form>
```

Or use React state:
```tsx
const handleSubmit = async (data) => {
  await fetch('https://api.example.com/contact', {
    method: 'POST',
    body: JSON.stringify(data)
  });
};
```

## Build Process

1. Parse markdown files → extract frontmatter
2. Generate routes from file structure
3. Render React + markdown content
4. Apply Tailwind classes
5. Output static HTML/CSS/JS

## Dockerfile

```dockerfile
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
```

## Testing

- Vitest (component tests)
- Playwright (E2E, forms)
- Markdownlint (content validation)

## Anti-Patterns

- ❌ Using for interactive apps (use frontend-react-*)
- ❌ Server-side rendering needs (use frontend-nextjs-*)
- ❌ Mixing content and code logic
- ❌ Not optimizing images
