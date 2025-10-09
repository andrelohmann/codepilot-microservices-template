# Scaffold Next.js Service

**Objective:** Create a production-ready Next.js application with App Router, Server Components, Server Actions, and comprehensive features for SEO-optimized web applications.

---

## 📋 Service Specification

Before running this prompt, gather the following information:

1. **Service Name:** `[e.g., frontend-nextjs-landing, frontend-nextjs-docs]`
2. **Service Purpose:** `[Brief description of the application]`
3. **Rendering Strategy:** `[SSR, SSG, ISR, or Hybrid]`
4. **API Endpoints:** `[Backend API URL or built-in API routes]`
5. **Authentication:** `[Yes/No - NextAuth.js]`
6. **Database:** `[Yes/No - for API routes]`
7. **CMS Integration:** `[Yes/No - Headless CMS]`
8. **Port:** `[Default: 3000, or specify custom]`

---

## 🏗️ Architecture Overview

```
┌────────────────────────────────────────────────────────────────────┐
│                    Next.js App Router Architecture                 │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                      App Router Layer                        │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │   Pages    │  │  Layouts   │  │  Loading   │            │ │
│  │  │   (RSC)    │  │   (RSC)    │  │   States   │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
│            ↓                ↓                ↓                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                    Server Components                         │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │   Server   │  │   Server   │  │  Metadata  │            │ │
│  │  │ Components │  │  Actions   │  │    API     │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
│            ↓                ↓                ↓                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                    Client Components                         │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │Interactive │  │   Forms    │  │   State    │            │ │
│  │  │Components  │  │  (Client)  │  │(useState)  │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
└────────────┼────────────────┼────────────────┼────────────────────┘
             │                │                │
             ↓                ↓                ↓
    ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
    │ External    │  │   Database  │  │     CMS     │
    │   APIs      │  │  (Optional) │  │  (Optional) │
    └─────────────┘  └─────────────┘  └─────────────┘
```

---

## 📁 Directory Structure

Create the following complete directory structure:

```
services/[service-name]/
├── src/
│   ├── app/                       ← App Router
│   │   ├── layout.tsx             ← Root layout
│   │   ├── page.tsx               ← Home page
│   │   ├── loading.tsx            ← Loading UI
│   │   ├── error.tsx              ← Error UI
│   │   ├── not-found.tsx          ← 404 page
│   │   │
│   │   ├── (auth)/                ← Route group
│   │   │   ├── login/
│   │   │   │   └── page.tsx
│   │   │   └── register/
│   │   │       └── page.tsx
│   │   │
│   │   ├── dashboard/
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx
│   │   │   └── loading.tsx
│   │   │
│   │   ├── blog/
│   │   │   ├── page.tsx           ← Blog list (SSG)
│   │   │   └── [slug]/
│   │   │       └── page.tsx       ← Blog post (SSG/ISR)
│   │   │
│   │   └── api/                   ← API routes
│   │       ├── auth/
│   │       │   └── [...nextauth]/
│   │       │       └── route.ts
│   │       └── users/
│   │           └── route.ts
│   │
│   ├── components/                ← React components
│   │   ├── ui/                    ← UI components
│   │   │   ├── Button.tsx
│   │   │   ├── Input.tsx
│   │   │   └── Card.tsx
│   │   ├── layout/
│   │   │   ├── Header.tsx
│   │   │   ├── Footer.tsx
│   │   │   └── Sidebar.tsx
│   │   └── features/
│   │       ├── BlogCard.tsx
│   │       └── UserProfile.tsx
│   │
│   ├── lib/                       ← Utilities
│   │   ├── auth.ts                ← NextAuth config
│   │   ├── db.ts                  ← Database client
│   │   ├── api.ts                 ← API utilities
│   │   └── utils.ts               ← Helper functions
│   │
│   ├── types/                     ← TypeScript types
│   │   ├── auth.ts
│   │   ├── user.ts
│   │   └── blog.ts
│   │
│   ├── actions/                   ← Server Actions
│   │   ├── auth.ts
│   │   ├── user.ts
│   │   └── blog.ts
│   │
│   └── middleware.ts              ← Next.js middleware
│
├── public/                        ← Static assets
│   ├── images/
│   ├── favicon.ico
│   └── robots.txt
│
├── tests/                         ← Tests
│   ├── unit/
│   └── e2e/
│
├── .eslintrc.json                 ← ESLint config
├── .prettierrc                    ← Prettier config
├── next.config.mjs                ← Next.js config
├── tsconfig.json                  ← TypeScript config
├── tailwind.config.ts             ← Tailwind config
├── postcss.config.js              ← PostCSS config
├── package.json                   ← Dependencies
├── Dockerfile                     ← Production Docker image
├── .dockerignore                  ← Docker ignore patterns
├── README.md                      ← Service documentation
└── .env.example                   ← Environment variables template
```

---

## 📦 Step 1: `package.json`

```json
{
  "name": "[service-name]",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "format": "prettier --write \"src/**/*.{ts,tsx,css}\"",
    "type-check": "tsc --noEmit",
    "test": "jest",
    "test:e2e": "playwright test"
  },
  "dependencies": {
    "next": "^14.0.4",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "next-auth": "^5.0.0-beta.4",
    "@prisma/client": "^5.7.1",
    "zod": "^3.22.4",
    "server-only": "^0.0.1",
    "clsx": "^2.0.0",
    "date-fns": "^3.0.0"
  },
  "devDependencies": {
    "@types/node": "^20.10.5",
    "@types/react": "^18.2.45",
    "@types/react-dom": "^18.2.18",
    "typescript": "^5.3.3",
    "tailwindcss": "^3.4.0",
    "autoprefixer": "^10.4.16",
    "postcss": "^8.4.32",
    "eslint": "^8.56.0",
    "eslint-config-next": "^14.0.4",
    "prettier": "^3.1.1",
    "prisma": "^5.7.1",
    "@playwright/test": "^1.40.1",
    "jest": "^29.7.0",
    "@testing-library/react": "^14.1.2",
    "@testing-library/jest-dom": "^6.1.5"
  }
}
```

---

## 🔧 Step 2: `src/app/layout.tsx` - Root Layout

```typescript
import type { Metadata } from 'next';
import { Inter } from 'next/font/google';
import './globals.css';

const inter = Inter({ subsets: ['latin'] });

export const metadata: Metadata = {
  title: {
    default: '[Service Name]',
    template: '%s | [Service Name]',
  },
  description: '[Service Purpose]',
  keywords: ['keyword1', 'keyword2', 'keyword3'],
  authors: [{ name: 'Your Name' }],
  openGraph: {
    title: '[Service Name]',
    description: '[Service Purpose]',
    url: 'https://your-domain.com',
    siteName: '[Service Name]',
    locale: 'en_US',
    type: 'website',
  },
  twitter: {
    card: 'summary_large_image',
    title: '[Service Name]',
    description: '[Service Purpose]',
  },
  robots: {
    index: true,
    follow: true,
    googleBot: {
      index: true,
      follow: true,
      'max-video-preview': -1,
      'max-image-preview': 'large',
      'max-snippet': -1,
    },
  },
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en" className={inter.className}>
      <body>
        {children}
      </body>
    </html>
  );
}
```

---

## 🔧 Step 3: `src/app/page.tsx` - Home Page (Server Component)

```typescript
import Link from 'next/link';
import { Suspense } from 'react';

// This is a Server Component by default
async function getData() {
  // Fetch data on the server
  const res = await fetch('https://api.example.com/data', {
    next: { revalidate: 3600 }, // ISR: Revalidate every hour
  });
  
  if (!res.ok) {
    throw new Error('Failed to fetch data');
  }
  
  return res.json();
}

export default async function HomePage() {
  const data = await getData();

  return (
    <main className="min-h-screen p-24">
      <div className="max-w-5xl mx-auto">
        <h1 className="text-4xl font-bold mb-8">
          Welcome to [Service Name]
        </h1>
        
        <p className="text-xl text-gray-600 mb-8">
          [Service Purpose]
        </p>

        <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
          <Link
            href="/dashboard"
            className="p-6 border border-gray-300 rounded-lg hover:border-blue-500"
          >
            <h2 className="text-2xl font-semibold mb-2">Dashboard →</h2>
            <p className="text-gray-600">
              Access your personalized dashboard
            </p>
          </Link>

          <Link
            href="/blog"
            className="p-6 border border-gray-300 rounded-lg hover:border-blue-500"
          >
            <h2 className="text-2xl font-semibold mb-2">Blog →</h2>
            <p className="text-gray-600">
              Read our latest articles
            </p>
          </Link>
        </div>

        <Suspense fallback={<div>Loading...</div>}>
          <DataDisplay data={data} />
        </Suspense>
      </div>
    </main>
  );
}

function DataDisplay({ data }: { data: any }) {
  return (
    <div className="mt-8">
      <h2 className="text-2xl font-semibold mb-4">Latest Data</h2>
      <pre className="bg-gray-100 p-4 rounded-lg overflow-auto">
        {JSON.stringify(data, null, 2)}
      </pre>
    </div>
  );
}
```

---

## 🔧 Step 4: `src/app/loading.tsx` - Loading UI

```typescript
export default function Loading() {
  return (
    <div className="min-h-screen flex items-center justify-center">
      <div className="animate-spin rounded-full h-32 w-32 border-b-2 border-gray-900" />
    </div>
  );
}
```

---

## 🔧 Step 5: `src/app/error.tsx` - Error UI

```typescript
'use client';

import { useEffect } from 'react';

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    console.error(error);
  }, [error]);

  return (
    <div className="min-h-screen flex items-center justify-center">
      <div className="text-center">
        <h2 className="text-2xl font-bold mb-4">Something went wrong!</h2>
        <p className="text-gray-600 mb-4">{error.message}</p>
        <button
          onClick={reset}
          className="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600"
        >
          Try again
        </button>
      </div>
    </div>
  );
}
```

---

## 🔧 Step 6: `src/app/dashboard/layout.tsx` - Dashboard Layout

```typescript
import { redirect } from 'next/navigation';
import { getServerSession } from 'next-auth';
import { authOptions } from '@/lib/auth';
import { Header } from '@/components/layout/Header';
import { Sidebar } from '@/components/layout/Sidebar';

export default async function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  const session = await getServerSession(authOptions);

  if (!session) {
    redirect('/login');
  }

  return (
    <div className="min-h-screen">
      <Header />
      <div className="flex">
        <Sidebar />
        <main className="flex-1 p-6">
          {children}
        </main>
      </div>
    </div>
  );
}
```

---

## 🔧 Step 7: `src/app/api/users/route.ts` - API Route

```typescript
import { NextRequest, NextResponse } from 'next/server';
import { getServerSession } from 'next-auth';
import { authOptions } from '@/lib/auth';
import { prisma } from '@/lib/db';
import { z } from 'zod';

const createUserSchema = z.object({
  name: z.string().min(1),
  email: z.string().email(),
});

// GET /api/users
export async function GET(request: NextRequest) {
  try {
    const session = await getServerSession(authOptions);
    
    if (!session) {
      return NextResponse.json(
        { error: 'Unauthorized' },
        { status: 401 }
      );
    }

    const users = await prisma.user.findMany({
      select: {
        id: true,
        name: true,
        email: true,
        createdAt: true,
      },
    });

    return NextResponse.json(users);
  } catch (error) {
    return NextResponse.json(
      { error: 'Internal Server Error' },
      { status: 500 }
    );
  }
}

// POST /api/users
export async function POST(request: NextRequest) {
  try {
    const session = await getServerSession(authOptions);
    
    if (!session) {
      return NextResponse.json(
        { error: 'Unauthorized' },
        { status: 401 }
      );
    }

    const body = await request.json();
    const validatedData = createUserSchema.parse(body);

    const user = await prisma.user.create({
      data: validatedData,
    });

    return NextResponse.json(user, { status: 201 });
  } catch (error) {
    if (error instanceof z.ZodError) {
      return NextResponse.json(
        { error: 'Validation error', details: error.errors },
        { status: 400 }
      );
    }

    return NextResponse.json(
      { error: 'Internal Server Error' },
      { status: 500 }
    );
  }
}
```

---

## 🔧 Step 8: `src/actions/user.ts` - Server Actions

```typescript
'use server';

import { revalidatePath } from 'next/cache';
import { redirect } from 'next/navigation';
import { getServerSession } from 'next-auth';
import { authOptions } from '@/lib/auth';
import { prisma } from '@/lib/db';
import { z } from 'zod';

const createUserSchema = z.object({
  name: z.string().min(1, 'Name is required'),
  email: z.string().email('Invalid email'),
});

export async function createUser(formData: FormData) {
  const session = await getServerSession(authOptions);
  
  if (!session) {
    throw new Error('Unauthorized');
  }

  const validatedFields = createUserSchema.safeParse({
    name: formData.get('name'),
    email: formData.get('email'),
  });

  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
    };
  }

  try {
    await prisma.user.create({
      data: validatedFields.data,
    });
  } catch (error) {
    return {
      message: 'Failed to create user',
    };
  }

  revalidatePath('/dashboard/users');
  redirect('/dashboard/users');
}

export async function deleteUser(id: string) {
  const session = await getServerSession(authOptions);
  
  if (!session) {
    throw new Error('Unauthorized');
  }

  try {
    await prisma.user.delete({
      where: { id },
    });
  } catch (error) {
    return {
      message: 'Failed to delete user',
    };
  }

  revalidatePath('/dashboard/users');
}
```

---

## 🔧 Step 9: `src/lib/auth.ts` - NextAuth Configuration

```typescript
import { NextAuthOptions } from 'next-auth';
import CredentialsProvider from 'next-auth/providers/credentials';
import { PrismaAdapter } from '@next-auth/prisma-adapter';
import { prisma } from './db';
import { compare } from 'bcryptjs';

export const authOptions: NextAuthOptions = {
  adapter: PrismaAdapter(prisma),
  session: {
    strategy: 'jwt',
  },
  pages: {
    signIn: '/login',
  },
  providers: [
    CredentialsProvider({
      name: 'credentials',
      credentials: {
        email: { label: 'Email', type: 'email' },
        password: { label: 'Password', type: 'password' },
      },
      async authorize(credentials) {
        if (!credentials?.email || !credentials?.password) {
          return null;
        }

        const user = await prisma.user.findUnique({
          where: {
            email: credentials.email,
          },
        });

        if (!user || !user.password) {
          return null;
        }

        const isPasswordValid = await compare(
          credentials.password,
          user.password
        );

        if (!isPasswordValid) {
          return null;
        }

        return {
          id: user.id,
          email: user.email,
          name: user.name,
        };
      },
    }),
  ],
  callbacks: {
    async jwt({ token, user }) {
      if (user) {
        token.id = user.id;
      }
      return token;
    },
    async session({ session, token }) {
      if (session.user) {
        session.user.id = token.id as string;
      }
      return session;
    },
  },
};
```

---

## 🔧 Step 10: `src/middleware.ts` - Middleware

```typescript
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { getToken } from 'next-auth/jwt';

export async function middleware(request: NextRequest) {
  const token = await getToken({
    req: request,
    secret: process.env.NEXTAUTH_SECRET,
  });

  const isAuthPage = request.nextUrl.pathname.startsWith('/login') ||
                     request.nextUrl.pathname.startsWith('/register');
  const isProtectedPage = request.nextUrl.pathname.startsWith('/dashboard');

  // Redirect authenticated users away from auth pages
  if (isAuthPage && token) {
    return NextResponse.redirect(new URL('/dashboard', request.url));
  }

  // Redirect unauthenticated users to login
  if (isProtectedPage && !token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/((?!api|_next/static|_next/image|favicon.ico).*)'],
};
```

---

## 🔧 Step 11: `src/components/ui/Button.tsx` - Client Component

```typescript
'use client';

import { ButtonHTMLAttributes, forwardRef } from 'react';
import clsx from 'clsx';

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'outline';
  size?: 'sm' | 'md' | 'lg';
  isLoading?: boolean;
}

export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  ({ variant = 'primary', size = 'md', isLoading, children, className, ...props }, ref) => {
    return (
      <button
        ref={ref}
        className={clsx(
          'inline-flex items-center justify-center rounded-md font-medium transition-colors',
          'focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-offset-2',
          'disabled:pointer-events-none disabled:opacity-50',
          {
            'bg-blue-600 text-white hover:bg-blue-700': variant === 'primary',
            'bg-gray-200 text-gray-900 hover:bg-gray-300': variant === 'secondary',
            'border border-gray-300 bg-transparent hover:bg-gray-50': variant === 'outline',
            'h-8 px-3 text-sm': size === 'sm',
            'h-10 px-4': size === 'md',
            'h-12 px-6 text-lg': size === 'lg',
          },
          className
        )}
        disabled={isLoading}
        {...props}
      >
        {isLoading ? (
          <>
            <svg
              className="animate-spin -ml-1 mr-2 h-4 w-4"
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
            >
              <circle
                className="opacity-25"
                cx="12"
                cy="12"
                r="10"
                stroke="currentColor"
                strokeWidth="4"
              />
              <path
                className="opacity-75"
                fill="currentColor"
                d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
              />
            </svg>
            Loading...
          </>
        ) : (
          children
        )}
      </button>
    );
  }
);

Button.displayName = 'Button';
```

---

## 🔧 Step 12: `next.config.mjs` - Next.js Configuration

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  images: {
    domains: ['your-domain.com'],
    formats: ['image/avif', 'image/webp'],
  },
  experimental: {
    serverActions: {
      bodySizeLimit: '2mb',
    },
  },
  // Production optimizations
  swcMinify: true,
  compress: true,
  poweredByHeader: false,
};

export default nextConfig;
```

---

## 🔧 Step 13: `tsconfig.json` - TypeScript Configuration

```json
{
  "compilerOptions": {
    "target": "ES2017",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

---

## 🔧 Step 14: `tailwind.config.ts` - Tailwind Configuration

```typescript
import type { Config } from 'tailwindcss';

const config: Config = {
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
    './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
        },
      },
    },
  },
  plugins: [],
};

export default config;
```

---

## 🔧 Step 15: `.env.example` - Environment Variables Template

```bash
# Database
DATABASE_URL="postgresql://user:password@localhost:5432/dbname"

# NextAuth
NEXTAUTH_URL="http://localhost:3000"
NEXTAUTH_SECRET="your-secret-key-change-in-production"

# External API (if needed)
API_URL="http://localhost:8000/api/v1"
```

---

## 🐳 Step 16: `Dockerfile` - Production Docker Image

```dockerfile
# Dependencies stage
FROM node:20-alpine AS deps
WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

# Builder stage
FROM node:20-alpine AS builder
WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# Production stage
FROM node:20-alpine AS runner
WORKDIR /app

ENV NODE_ENV production
ENV NEXT_TELEMETRY_DISABLED 1

RUN addgroup --system --gid 1001 nodejs && \
    adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

ENV PORT 3000
ENV HOSTNAME "0.0.0.0"

CMD ["node", "server.js"]
```

---

## 🐳 Step 17: Docker Compose Integration

Add to `docker-compose.yml`:

```yaml
  [service-name]:
    build:
      context: ./services/[service-name]
      dockerfile: Dockerfile
    container_name: [service-name]
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=${DATABASE_URL}
      - NEXTAUTH_URL=${NEXTAUTH_URL}
      - NEXTAUTH_SECRET=${NEXTAUTH_SECRET}
    depends_on:
      postgres:
        condition: service_healthy
    networks:
      - microservices
    healthcheck:
      test: ["CMD", "node", "-e", "require('http').get('http://localhost:3000/api/health', (res) => process.exit(res.statusCode === 200 ? 0 : 1))"]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 40s
```

---

## 🔧 Step 18: `.devcontainer/[service-name]-container/devcontainer.json`

Location: Project root `.devcontainer/[service-name]-container/devcontainer.json`

```json
{
  "name": "[service-name]",
  "dockerComposeFile": ["../../docker-compose.development.yml"],
  "service": "[service-name]",
  "workspaceFolder": "/workspace/services/[service-name]",
  "customizations": {
    "vscode": {
      "extensions": [
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode",
        "bradlc.vscode-tailwindcss",
        "Prisma.prisma"
      ],
      "settings": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
          "source.fixAll.eslint": "explicit"
        }
      }
    }
  },
  "forwardPorts": [3000],
  "postCreateCommand": "npm install && npx prisma generate",
  "remoteUser": "node"
}
```

---

## 📝 Step 19: `README.md` - Service Documentation

```markdown
# [Service Name]

[Service Purpose]

## Technology Stack

- **Framework:** Next.js 14 (App Router)
- **Language:** TypeScript 5
- **Authentication:** NextAuth.js 5
- **Database:** PostgreSQL (Prisma ORM)
- **Styling:** Tailwind CSS
- **Validation:** Zod
- **Testing:** Jest, Playwright

## Features

- ✅ Next.js 14 App Router
- ✅ Server Components by default
- ✅ Server Actions for mutations
- ✅ NextAuth.js authentication
- ✅ Prisma ORM with PostgreSQL
- ✅ Type-safe API routes
- ✅ Middleware for route protection
- ✅ SEO optimized with Metadata API
- ✅ ISR/SSG support
- ✅ Tailwind CSS styling
- ✅ Zod validation
- ✅ Production-ready Docker setup

## Getting Started

### Development

```bash
# Install dependencies
npm install

# Setup database
npx prisma migrate dev

# Run development server
npm run dev

# Run tests
npm run test
```

## Environment Variables

See `.env.example` for required environment variables.

## Project Structure

- `src/app/` - App Router pages and layouts
- `src/components/` - React components
- `src/actions/` - Server Actions
- `src/lib/` - Utilities and configurations
- `src/types/` - TypeScript types

## Database

```bash
# Generate Prisma client
npx prisma generate

# Run migrations
npx prisma migrate dev

# Open Prisma Studio
npx prisma studio
```

## Deployment

The application is optimized for production with:
- Standalone output mode
- Image optimization
- Automatic static optimization
- Server-side rendering where needed

## SEO

- Metadata API for SEO tags
- Automatic sitemap generation
- robots.txt configuration
- OpenGraph and Twitter cards
```

---

## ✅ Completion Checklist

- [ ] All directories created
- [ ] `package.json` configured
- [ ] Root layout with metadata
- [ ] Home page (Server Component)
- [ ] Loading and error UIs
- [ ] Dashboard layout with auth check
- [ ] API routes created
- [ ] Server Actions implemented
- [ ] NextAuth configuration
- [ ] Middleware for route protection
- [ ] Client components created
- [ ] Prisma schema defined
- [ ] Next.js configuration complete
- [ ] TypeScript configuration complete
- [ ] Tailwind CSS configured
- [ ] `Dockerfile` with standalone output
- [ ] `.env.example` documented
- [ ] Docker Compose integration added
- [ ] `.devcontainer/[service-name]-container/devcontainer.json` created at project root
- [ ] `README.md` completed
- [ ] Service builds successfully
- [ ] Authentication flow working
- [ ] Server Actions working
- [ ] API routes accessible

---

**Note:** Replace all `[service-name]` placeholders with your actual service name. Configure database, authentication providers, and API integrations based on your requirements.
