---
applyTo: "**/frontend-nextjs-*/**"
description: Next.js app with SSR/SSG, React Server Components, and App Router
---

# Frontend Next.js - Hybrid Rendering

Build SEO-optimized web apps with Next.js using App Router and React Server Components.

## Naming Convention

`frontend-nextjs-{purpose}` (e.g., `frontend-nextjs-landing`, `frontend-nextjs-docs`)

## Technology Stack

- Next.js 14+ (framework)
- React 18+ (UI library)
- TypeScript 5+ (language)
- App Router (routing)
- React Server Components
- Server Actions (mutations)
- Tailwind CSS (styling)
- Prisma or Drizzle (ORM)
- NextAuth.js (authentication)
- Vercel Analytics

## Directory Structure

```
services/frontend-nextjs-{purpose}/
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── (auth)/
│   │   ├── login/
│   │   └── register/
│   ├── (dashboard)/
│   │   ├── layout.tsx
│   │   └── dashboard/
│   ├── api/
│   │   └── auth/[...nextauth]/
│   └── globals.css
├── components/
│   ├── ui/
│   └── forms/
├── lib/
│   ├── db.ts
│   ├── auth.ts
│   └── utils.ts
├── public/
├── Dockerfile
├── package.json
├── next.config.js
└── tailwind.config.ts
```

## App Router

File-based routing:
```
app/
├── page.tsx                  # /
├── about/page.tsx            # /about
├── blog/
│   ├── page.tsx              # /blog
│   └── [slug]/page.tsx       # /blog/:slug
└── (dashboard)/              # Route group (no URL segment)
    └── settings/page.tsx     # /settings
```

## Server Components

Default in App Router:
```typescript
// app/page.tsx
async function HomePage() {
  const data = await fetch('https://api.example.com/data', {
    next: { revalidate: 3600 } // ISR
  });
  
  return <div>{data.title}</div>;
}
```

## Client Components

Use 'use client' directive:
```typescript
'use client';

import { useState } from 'react';

export function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

## Server Actions

Form mutations:
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

## Data Fetching

Async Server Components:
```typescript
async function Posts() {
  const posts = await db.post.findMany();
  
  return posts.map(post => <PostCard key={post.id} post={post} />);
}
```

Parallel data fetching:
```typescript
async function Dashboard() {
  const [user, posts] = await Promise.all([
    getUser(),
    getPosts(),
  ]);
  
  return <>{/* ... */}</>;
}
```

## Layouts

Shared UI across routes:
```typescript
// app/layout.tsx
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <Header />
        {children}
        <Footer />
      </body>
    </html>
  );
}
```

## Metadata

SEO optimization:
```typescript
import { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'Page Title',
  description: 'Page description',
  openGraph: {
    title: 'Page Title',
    description: 'Page description',
    images: ['/og-image.jpg'],
  },
};
```

Dynamic metadata:
```typescript
export async function generateMetadata({ params }): Promise<Metadata> {
  const post = await getPost(params.id);
  
  return {
    title: post.title,
    description: post.excerpt,
  };
}
```

## Authentication

NextAuth.js:
```typescript
// app/api/auth/[...nextauth]/route.ts
import NextAuth from 'next-auth';
import Credentials from 'next-auth/providers/credentials';

export const authOptions = {
  providers: [
    Credentials({
      credentials: {
        email: {},
        password: {},
      },
      authorize: async (credentials) => {
        const user = await verifyCredentials(credentials);
        return user || null;
      },
    }),
  ],
};

export const GET = NextAuth(authOptions);
export const POST = NextAuth(authOptions);
```

## Middleware

Route protection:
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

## Environment Variables

```typescript
// Access server-side
process.env.DATABASE_URL

// Access client-side (must prefix with NEXT_PUBLIC_)
process.env.NEXT_PUBLIC_API_URL
```

## Caching Strategies

```typescript
// Static (default)
fetch('https://api.example.com/data');

// ISR (revalidate every hour)
fetch('https://api.example.com/data', {
  next: { revalidate: 3600 }
});

// Dynamic (no cache)
fetch('https://api.example.com/data', {
  cache: 'no-store'
});

// Tag-based revalidation
fetch('https://api.example.com/data', {
  next: { tags: ['posts'] }
});

// In Server Action
revalidateTag('posts');
```

## Image Optimization

```typescript
import Image from 'next/image';

<Image
  src="/image.jpg"
  alt="Description"
  width={800}
  height={600}
  priority // for LCP images
/>
```

## Testing

```typescript
// __tests__/page.test.tsx
import { render, screen } from '@testing-library/react';
import Home from '@/app/page';

test('renders homepage', () => {
  render(<Home />);
  expect(screen.getByText('Welcome')).toBeInTheDocument();
});
```

## Anti-Patterns

- ❌ Using 'use client' unnecessarily
- ❌ Fetching data in client components
- ❌ Not leveraging Server Actions
- ❌ Ignoring caching strategies
- ❌ Missing metadata for SEO
