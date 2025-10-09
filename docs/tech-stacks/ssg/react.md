# React SSG - Static Site with React + Vite + MDX

Build interactive static websites with React components embedded in markdown content.

---

## Overview

**Naming:** `ssg-react-{purpose}`

**Examples:** `ssg-react-marketing`, `ssg-react-docs`, `ssg-react-blog`

**React SSG** combines the power of React with static generation, perfect for content sites that need more interactivity.

---

## Technology Stack

- **React 18+** - UI library
- **Vite 5+** - Build tool
- **MDX** - Markdown + JSX
- **Tailwind CSS 3+** - Styling
- **Gray Matter** - Frontmatter parsing
- **vite-plugin-pages** - File-based routing
- **React Hook Form** - Forms

---

## Why React SSG?

✅ **Full React ecosystem** - Use any React library  
✅ **MDX support** - Components in markdown  
✅ **Content hierarchy** - File structure = site structure  
✅ **Fast builds** - Vite HMR  
✅ **Interactive** - Full client-side capabilities  
✅ **Familiar** - Standard React development

---

## Project Structure

```
ssg-react-{purpose}/
├── content/                   # Markdown content
│   ├── index.md              # Homepage
│   ├── about/
│   │   ├── index.md         # /about
│   │   └── team.md          # /about/team
│   ├── products/
│   │   ├── index.md
│   │   └── service-a.md
│   └── blog/
│       └── 2024-01-15-post.md
├── src/
│   ├── components/
│   │   ├── layout/
│   │   │   ├── Header.tsx
│   │   │   └── Footer.tsx
│   │   ├── marketing/
│   │   │   ├── Hero.tsx
│   │   │   ├── FeatureGrid.tsx
│   │   │   └── PricingTable.tsx
│   │   └── forms/
│   │       └── ContactForm.tsx
│   ├── pages/
│   │   └── [...slug].tsx    # Dynamic route
│   ├── lib/
│   │   ├── markdown.ts      # Parse markdown
│   │   └── api.ts           # API client
│   └── styles/
│       └── globals.css
├── public/
├── vite.config.ts
└── tailwind.config.ts
```

---

## Code Examples

### Markdown with Frontmatter

```markdown
---
# content/products/service-a.md
title: "Service A - Enterprise Solutions"
description: "Transform your business"
layout: "product"
image: "/images/service-a.jpg"
cta_text: "Get Started"
cta_link: "/contact"
---

# Service A

<Hero 
  title="Transform Your Business" 
  subtitle="Industry-leading solutions"
  backgroundImage="/images/hero.jpg"
/>

## Key Features

<FeatureGrid>
  <Feature icon="zap" title="Fast Performance">
    Lightning-fast processing times
  </Feature>
  <Feature icon="shield" title="Secure">
    Enterprise-grade security
  </Feature>
</FeatureGrid>

## Pricing

<PricingTable plans={[
  { name: "Starter", price: "$99/mo", features: ["Feature 1", "Feature 2"] },
  { name: "Pro", price: "$299/mo", features: ["All Starter", "Feature 3"] }
]} />

## Contact Us

<ContactForm apiEndpoint="https://api.example.com/contact" />
```

### Markdown Parser

```typescript
// src/lib/markdown.ts
import fs from 'fs';
import path from 'path';
import matter from 'gray-matter';
import { remark } from 'remark';
import html from 'remark-html';

export interface PageData {
  slug: string;
  frontmatter: Record<string, any>;
  content: string;
}

export function getContentFiles(dir: string): string[] {
  const files: string[] = [];
  const entries = fs.readdirSync(dir, { withFileTypes: true });

  for (const entry of entries) {
    const fullPath = path.join(dir, entry.name);
    if (entry.isDirectory()) {
      files.push(...getContentFiles(fullPath));
    } else if (entry.name.endsWith('.md')) {
      files.push(fullPath);
    }
  }

  return files;
}

export async function parseMarkdown(filePath: string): Promise<PageData> {
  const fileContents = fs.readFileSync(filePath, 'utf8');
  const { data, content } = matter(fileContents);

  const processedContent = await remark()
    .use(html)
    .process(content);

  const slug = filePath
    .replace('content/', '')
    .replace('/index.md', '')
    .replace('.md', '');

  return {
    slug,
    frontmatter: data,
    content: processedContent.toString(),
  };
}
```

### Dynamic Page Component

```tsx
// src/pages/[...slug].tsx
import { useEffect, useState } from 'react';
import { useParams } from 'react-router-dom';
import { MDXProvider } from '@mdx-js/react';
import Layout from '@/components/layout/Layout';
import Hero from '@/components/marketing/Hero';
import FeatureGrid from '@/components/marketing/FeatureGrid';
import ContactForm from '@/components/forms/ContactForm';

const components = {
  Hero,
  FeatureGrid,
  ContactForm,
};

export default function DynamicPage() {
  const { slug } = useParams();
  const [page, setPage] = useState(null);

  useEffect(() => {
    // Load page data
    fetch(`/content/${slug}.json`)
      .then(r => r.json())
      .then(setPage);
  }, [slug]);

  if (!page) return <div>Loading...</div>;

  return (
    <Layout>
      <MDXProvider components={components}>
        <div dangerouslySetInnerHTML={{ __html: page.content }} />
      </MDXProvider>
    </Layout>
  );
}
```

### Reusable Component

```tsx
// src/components/marketing/FeatureGrid.tsx
import { ReactNode } from 'react';

interface FeatureProps {
  icon: string;
  title: string;
  children: ReactNode;
}

export function Feature({ icon, title, children }: FeatureProps) {
  return (
    <div className="p-6 border rounded-lg">
      <div className="text-4xl mb-4">{icon}</div>
      <h3 className="text-xl font-bold mb-2">{title}</h3>
      <p className="text-gray-600">{children}</p>
    </div>
  );
}

interface FeatureGridProps {
  children: ReactNode;
}

export default function FeatureGrid({ children }: FeatureGridProps) {
  return (
    <div className="grid grid-cols-1 md:grid-cols-3 gap-6 my-8">
      {children}
    </div>
  );
}
```

### Contact Form

```tsx
// src/components/forms/ContactForm.tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const contactSchema = z.object({
  name: z.string().min(1),
  email: z.string().email(),
  company: z.string().optional(),
  message: z.string().min(10),
});

type ContactForm = z.infer<typeof contactSchema>;

interface ContactFormProps {
  apiEndpoint: string;
}

export default function ContactForm({ apiEndpoint }: ContactFormProps) {
  const { register, handleSubmit, formState: { errors }, reset } = useForm<ContactForm>({
    resolver: zodResolver(contactSchema),
  });

  const onSubmit = async (data: ContactForm) => {
    try {
      await fetch(apiEndpoint, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data),
      });
      alert('Message sent successfully!');
      reset();
    } catch (error) {
      alert('Failed to send message');
    }
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-4 max-w-lg">
      <div>
        <label className="block mb-2">Name</label>
        <input {...register('name')} className="w-full px-4 py-2 border rounded" />
        {errors.name && <p className="text-red-500 text-sm">{errors.name.message}</p>}
      </div>

      <div>
        <label className="block mb-2">Email</label>
        <input {...register('email')} type="email" className="w-full px-4 py-2 border rounded" />
        {errors.email && <p className="text-red-500 text-sm">{errors.email.message}</p>}
      </div>

      <div>
        <label className="block mb-2">Company (Optional)</label>
        <input {...register('company')} className="w-full px-4 py-2 border rounded" />
      </div>

      <div>
        <label className="block mb-2">Message</label>
        <textarea {...register('message')} rows={5} className="w-full px-4 py-2 border rounded" />
        {errors.message && <p className="text-red-500 text-sm">{errors.message.message}</p>}
      </div>

      <button type="submit" className="bg-blue-600 text-white px-6 py-2 rounded hover:bg-blue-700">
        Send Message
      </button>
    </form>
  );
}
```

---

## Build Configuration

```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import pages from 'vite-plugin-pages';
import mdx from '@mdx-js/rollup';

export default defineConfig({
  plugins: [
    react(),
    mdx(),
    pages({
      dirs: 'src/pages',
    }),
  ],
  build: {
    outDir: 'dist',
  },
});
```

---

## Deployment

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

---

## Best Practices

✅ Keep content in `content/` directory  
✅ Use frontmatter for metadata  
✅ Create reusable components  
✅ Implement proper TypeScript types  
✅ Use Tailwind for consistent styling  
✅ Optimize images  
✅ Test forms with validation  
✅ Implement SEO metadata

---

## Related Documentation

- [SSG Overview](./README.md)
- [Astro SSG](./astro.md) - Faster alternative
- [Comparison Guide](./COMPARISON.md)

---

**Reference Files:**
- Instruction: `service-ssg-react.instructions.md`
- Scaffold: `scaffold-ssg-react-service.md`
