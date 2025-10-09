# Next.js - Server-Side Rendered React Framework

Modern React framework with server-side rendering, static generation, and excellent SEO capabilities.

---

## Overview

**Naming Convention:** `frontend-nextjs-{purpose}`

**Examples:**
- `frontend-nextjs-landing` - Marketing landing page
- `frontend-nextjs-docs` - Documentation site
- `frontend-nextjs-blog` - Blog platform

**Next.js** is perfect for public-facing websites where SEO is critical, combining server-side rendering with static generation.

---

## Technology Stack

- **Next.js 14+** - App Router
- **React 18+** - Server Components
- **TypeScript 5+** - Type safety
- **Server Actions** - Form handling
- **Tailwind CSS 3+** - Styling
- **next/image** - Image optimization
- **next/font** - Font optimization

---

## Features

- ✅ **Server-side rendering** - Fast initial loads
- ✅ **Static generation** - Pre-render at build time
- ✅ **Server Components** - Zero JS for non-interactive parts
- ✅ **API routes** - Backend endpoints alongside frontend
- ✅ **Image optimization** - Automatic WebP/AVIF conversion
- ✅ **SEO-friendly** - Perfect for search engines

---

## Ideal Use Cases

✅ **Marketing Sites** - Company websites, landing pages  
✅ **SEO-Critical Pages** - Public-facing content  
✅ **Blogs** - Content platforms  
✅ **E-commerce** - Product catalogs  
✅ **Documentation** - Technical docs

❌ **Not Ideal For:**
- Pure static sites → Use SSG (Astro/React+Vite)
- Internal dashboards → Use React SPA
- Mobile apps → Use Flutter

---

## Project Structure

```
frontend-nextjs-{purpose}/
├── app/
│   ├── layout.tsx           # Root layout
│   ├── page.tsx             # Homepage
│   ├── about/
│   │   └── page.tsx         # /about
│   ├── blog/
│   │   ├── page.tsx         # /blog
│   │   └── [slug]/
│   │       └── page.tsx     # /blog/[slug]
│   └── api/
│       └── contact/
│           └── route.ts     # API route
├── components/
├── lib/
├── public/
├── .env.example
├── next.config.js
└── tailwind.config.ts
```

---

## Code Examples

### Server Component (Default)

```typescript
// app/blog/page.tsx
export default async function BlogPage() {
  // Fetch data on server
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());

  return (
    <div className="container mx-auto">
      <h1 className="text-4xl font-bold">Blog</h1>
      <div className="grid grid-cols-3 gap-4">
        {posts.map((post) => (
          <article key={post.id}>
            <h2>{post.title}</h2>
            <p>{post.excerpt}</p>
          </article>
        ))}
      </div>
    </div>
  );
}
```

### Client Component (Interactive)

```typescript
// components/SearchForm.tsx
'use client';

import { useState } from 'react';

export function SearchForm() {
  const [query, setQuery] = useState('');

  return (
    <form className="flex gap-2">
      <input
        type="search"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        className="px-4 py-2 border rounded"
      />
      <button type="submit">Search</button>
    </form>
  );
}
```

### Server Actions (Forms)

```typescript
// app/contact/page.tsx
import { revalidatePath } from 'next/cache';

async function submitContact(formData: FormData) {
  'use server';
  
  const data = {
    name: formData.get('name'),
    email: formData.get('email'),
    message: formData.get('message'),
  };

  await fetch('https://api.example.com/contact', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(data),
  });

  revalidatePath('/contact');
}

export default function ContactPage() {
  return (
    <form action={submitContact}>
      <input name="name" required />
      <input name="email" type="email" required />
      <textarea name="message" required />
      <button type="submit">Send</button>
    </form>
  );
}
```

### Dynamic Routes

```typescript
// app/blog/[slug]/page.tsx
export async function generateStaticParams() {
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());
  
  return posts.map((post) => ({
    slug: post.slug,
  }));
}

export default async function BlogPost({ params }: { params: { slug: string } }) {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(r => r.json());

  return (
    <article>
      <h1>{post.title}</h1>
      <div dangerouslySetInnerHTML={{ __html: post.content }} />
    </article>
  );
}
```

### Metadata (SEO)

```typescript
// app/blog/[slug]/page.tsx
import { Metadata } from 'next';

export async function generateMetadata({ params }): Promise<Metadata> {
  const post = await fetch(`https://api.example.com/posts/${params.slug}`)
    .then(r => r.json());

  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      description: post.excerpt,
      images: [post.image],
    },
  };
}
```

---

## Deployment

### Vercel (Recommended)

```bash
npm run build
# Deploy to Vercel (automatic from Git)
```

### Docker (Self-Hosted)

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/package*.json ./
RUN npm ci --only=production

EXPOSE 3000
CMD ["npm", "start"]
```

---

## Best Practices

✅ Use Server Components by default  
✅ Use 'use client' only when needed  
✅ Implement proper loading.tsx files  
✅ Use next/image for all images  
✅ Implement metadata for SEO  
✅ Use Server Actions for forms  
✅ Implement error.tsx boundaries  
✅ Cache strategically with revalidate

---

## Related Documentation

- [Frontends Overview](./README.md)
- [React SPA](./react.md) - For internal apps
- [SSG Technologies](../ssg/README.md) - For pure static sites

---

**Reference Files:**
- Instruction: `service-frontend-nextjs.instructions.md`
- Scaffold: `scaffold-frontend-nextjs-service.md`
