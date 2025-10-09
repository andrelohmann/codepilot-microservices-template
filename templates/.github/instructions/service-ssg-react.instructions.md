---
applyTo: "**/ssg-react-*/**"
description: Static site generator with React, Vite, MDX, and Tailwind
---

# SSG React - Static Site Generator

Build static websites from markdown content with React components.

**Reference**: [Complete React SSG Tech Stack Documentation](../../../docs/tech-stacks/ssg/react.md)  
**Scaffold**: Use the `scaffold-ssg-react-service.prompt.md` for new services

## Naming Convention

`ssg-react-{purpose}` (e.g., `ssg-react-docs`, `ssg-react-blog`, `ssg-react-marketing`)

## Project Structure

```
services/ssg-react-{purpose}/
├── content/            # Markdown files = pages
│   ├── index.md        # → /
│   ├── about.md        # → /about
│   └── docs/
│       └── guide.md    # → /docs/guide
├── src/
│   ├── components/     # React components
│   ├── pages/
│   ├── lib/
│   └── styles/
└── vite.config.ts
```

## Quick Reference Patterns

### 1. Markdown with Frontmatter

```markdown
---
title: "Page Title"
description: "SEO description"
layout: "default"
date: "2025-01-07"
---

# Content starts here
```

### 2. MDX with React Components

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

### 3. Vite Config

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
  }
});
```

### 4. Form Integration

```tsx
// React state form
const handleSubmit = async (data) => {
  await fetch('https://api.example.com/contact', {
    method: 'POST',
    body: JSON.stringify(data)
  });
};
```

## Code Style

- Content in `/content/` directory
- File structure = URL structure
- Frontmatter for metadata
- MDX for embedding React components
- Forms POST to REST APIs
- Tailwind CSS for styling
- Vite for build tooling
- Output static HTML/CSS/JS to `/dist`
- Serve with Nginx

## Anti-Patterns

- ❌ Using for interactive apps (use frontend-react-*)
- ❌ Server-side rendering needs (use frontend-nextjs-*)
- ❌ Mixing content and code logic
- ❌ Not optimizing images
