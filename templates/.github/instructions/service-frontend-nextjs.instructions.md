---
applyTo: "**/frontend-nextjs-*/**"
description: Next.js app with SSR/SSG, React Server Components, and App Router
---

# Frontend Next.js - Hybrid Rendering

Build SEO-optimized web apps with Next.js using App Router and React Server Components.

**Reference**: [Complete Next.js Tech Stack Documentation](../../../docs/tech-stacks/frontends/nextjs.md)  
**Scaffold**: Use the `scaffold-frontend-nextjs-service.prompt.md` for new services

## Naming Convention

`frontend-nextjs-{purpose}` (e.g., `frontend-nextjs-landing`, `frontend-nextjs-docs`)

## Project Structure

```
services/frontend-nextjs-{purpose}/
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── (auth)/          # Route group
│   ├── (dashboard)/
│   └── api/
├── components/
├── lib/
└── next.config.js
```

## Quick Reference Patterns

### 1. Server Component (Default)

```typescript
// app/page.tsx
async function HomePage() {
  const data = await fetch('https://api.example.com/data', {
    next: { revalidate: 3600 } // ISR
  });
  
  return <div>{data.title}</div>;
}
```

### 2. Client Component

```typescript
'use client';

import { useState } from 'react';

export function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### 3. Server Actions

```typescript
// app/actions.ts
'use server';

import { revalidatePath } from 'next/cache';

export async function createUser(formData: FormData) {
  const email = formData.get('email');
  await db.user.create({ data: { email } });
  revalidatePath('/users');
  return { success: true };
}

// app/users/page.tsx
import { createUser } from './actions';

function CreateUserForm() {
  return (
    <form action={createUser}>
      <input name="email" type="email" />
      <button type="submit">Create</button>
    </form>
  );
}
```

### 4. Dynamic Metadata

```typescript
export async function generateMetadata({ params }): Promise<Metadata> {
  const post = await getPost(params.id);
  
  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      images: ['/og-image.jpg'],
    },
  };
}
```

### 5. Middleware (Auth Protection)

```typescript
// middleware.ts
import { NextResponse } from 'next/server';
import { getToken } from 'next-auth/jwt';

export async function middleware(req) {
  const token = await getToken({ req });
  
  if (!token && req.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', req.url));
  }
}

export const config = {
  matcher: ['/dashboard/:path*'],
};
```

## Code Style

- Use Server Components by default (no 'use client')
- Add 'use client' only when needed (hooks, interactivity)
- Server Actions for mutations and form handling
- File-based routing with App Router
- Route groups `(group)` for shared layouts without URL segment
- Parallel data fetching with `Promise.all()`
- ISR with `next: { revalidate: 3600 }`
- Dynamic routes with `[slug]` folders
- Always provide metadata for SEO
- NextAuth.js for authentication

## Caching Strategies

```typescript
// Static (default)
fetch('https://api.example.com/data');

// ISR (revalidate every hour)
fetch('https://api.example.com/data', { next: { revalidate: 3600 } });

// Dynamic (no cache)
fetch('https://api.example.com/data', { cache: 'no-store' });

// Tag-based revalidation
fetch('https://api.example.com/data', { next: { tags: ['posts'] } });
revalidateTag('posts'); // In Server Action
```

## Anti-Patterns

- ❌ Using 'use client' unnecessarily
- ❌ Fetching data in client components
- ❌ Not leveraging Server Actions
- ❌ Ignoring caching strategies
- ❌ Missing metadata for SEO
