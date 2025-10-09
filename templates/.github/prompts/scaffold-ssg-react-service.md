# Scaffold SSG React Service

Create a static site generator service using React, Vite, MDX, and Tailwind CSS.

## Service Name

`ssg-react-{purpose}` (e.g., `ssg-react-docs`, `ssg-react-blog`)

## Directory Structure

```
services/ssg-react-{purpose}/
├── content/
│   ├── index.md
│   └── about.md
├── src/
│   ├── components/
│   │   ├── Layout.tsx
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   ├── SEO.tsx
│   │   └── mdx/
│   │       ├── CodeBlock.tsx
│   │       ├── InfoBox.tsx
│   │       └── ContactForm.tsx
│   ├── pages/
│   │   └── [...slug].tsx
│   ├── lib/
│   │   ├── markdown.ts
│   │   ├── routes.ts
│   │   └── api.ts
│   ├── styles/
│   │   └── globals.css
│   └── main.tsx
├── public/
│   ├── robots.txt
│   └── sitemap.xml
├── tests/
│   ├── components/
│   └── e2e/
├── .dockerignore
├── Dockerfile
├── package.json
├── tsconfig.json
├── vite.config.ts
├── tailwind.config.ts
├── postcss.config.js
└── README.md
```

## Required Files

### 1. `package.json`

```json
{
  "name": "ssg-react-{purpose}",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "test": "vitest",
    "test:e2e": "playwright test",
    "lint": "eslint src --ext ts,tsx",
    "format": "prettier --write \"src/**/*.{ts,tsx,css,md}\""
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.20.0",
    "@mdx-js/react": "^3.0.0",
    "gray-matter": "^4.0.3",
    "remark": "^15.0.1",
    "remark-gfm": "^4.0.0",
    "remark-frontmatter": "^5.0.0",
    "rehype-prism-plus": "^2.0.0",
    "rehype-slug": "^6.0.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "@vitejs/plugin-react": "^4.2.0",
    "@mdx-js/rollup": "^3.0.0",
    "vite": "^5.0.0",
    "typescript": "^5.3.0",
    "tailwindcss": "^3.4.0",
    "autoprefixer": "^10.4.16",
    "postcss": "^8.4.32",
    "vitest": "^1.0.0",
    "@playwright/test": "^1.40.0",
    "eslint": "^8.55.0",
    "prettier": "^3.1.0"
  }
}
```

### 2. `vite.config.ts`

```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import mdx from '@mdx-js/rollup';
import remarkGfm from 'remark-gfm';
import remarkFrontmatter from 'remark-frontmatter';
import rehypePrism from 'rehype-prism-plus';
import rehypeSlug from 'rehype-slug';

export default defineConfig({
  plugins: [
    react(),
    mdx({
      remarkPlugins: [remarkGfm, remarkFrontmatter],
      rehypePlugins: [rehypePrism, rehypeSlug],
    }),
  ],
  build: {
    outDir: 'dist',
    emptyOutDir: true,
    sourcemap: true,
  },
  server: {
    port: 3000,
    host: '0.0.0.0',
  },
});
```

### 3. `tailwind.config.ts`

```typescript
import type { Config } from 'tailwindcss';

export default {
  content: ['./index.html', './src/**/*.{js,ts,jsx,tsx,mdx}', './content/**/*.{md,mdx}'],
  theme: {
    extend: {
      typography: {
        DEFAULT: {
          css: {
            maxWidth: 'none',
          },
        },
      },
    },
  },
  plugins: [require('@tailwindcss/typography')],
} satisfies Config;
```

### 4. `src/lib/markdown.ts`

```typescript
import matter from 'gray-matter';
import { join } from 'path';
import { readFileSync, readdirSync } from 'fs';

export interface PageData {
  slug: string;
  frontmatter: {
    title: string;
    description?: string;
    date?: string;
    layout?: string;
  };
  content: string;
}

export function getAllPages(dir: string = 'content'): PageData[] {
  const files = readdirSync(dir, { recursive: true });
  
  return files
    .filter(file => file.endsWith('.md') || file.endsWith('.mdx'))
    .map(file => {
      const path = join(dir, file);
      const fileContents = readFileSync(path, 'utf8');
      const { data, content } = matter(fileContents);
      
      return {
        slug: file.replace(/\.(md|mdx)$/, '').replace(/\/index$/, ''),
        frontmatter: data as any,
        content,
      };
    });
}
```

### 5. `src/components/Layout.tsx`

```typescript
import React from 'react';
import Header from './Header';
import Footer from './Footer';

interface LayoutProps {
  children: React.ReactNode;
}

export default function Layout({ children }: LayoutProps) {
  return (
    <div className="min-h-screen flex flex-col">
      <Header />
      <main className="flex-grow container mx-auto px-4 py-8">
        {children}
      </main>
      <Footer />
    </div>
  );
}
```

### 6. `src/components/mdx/CodeBlock.tsx`

```typescript
import React from 'react';

interface CodeBlockProps {
  language: string;
  children: string;
}

export default function CodeBlock({ language, children }: CodeBlockProps) {
  return (
    <div className="relative">
      <div className="absolute top-0 right-0 px-2 py-1 text-xs text-gray-400 bg-gray-800">
        {language}
      </div>
      <pre className={`language-${language}`}>
        <code>{children}</code>
      </pre>
    </div>
  );
}
```

### 7. `src/components/mdx/ContactForm.tsx`

```typescript
import React, { useState } from 'react';

interface ContactFormProps {
  apiEndpoint: string;
  fields: string[];
}

export default function ContactForm({ apiEndpoint, fields }: ContactFormProps) {
  const [formData, setFormData] = useState<Record<string, string>>({});
  const [status, setStatus] = useState<'idle' | 'loading' | 'success' | 'error'>('idle');

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    setStatus('loading');
    
    try {
      const response = await fetch(apiEndpoint, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(formData),
      });
      
      if (response.ok) {
        setStatus('success');
        setFormData({});
      } else {
        setStatus('error');
      }
    } catch (error) {
      setStatus('error');
    }
  };

  return (
    <form onSubmit={handleSubmit} className="max-w-md space-y-4">
      {fields.map(field => (
        <div key={field}>
          <label className="block text-sm font-medium mb-1 capitalize">
            {field}
          </label>
          <input
            type={field === 'email' ? 'email' : 'text'}
            value={formData[field] || ''}
            onChange={e => setFormData({ ...formData, [field]: e.target.value })}
            className="w-full px-3 py-2 border rounded-md"
            required
          />
        </div>
      ))}
      <button
        type="submit"
        disabled={status === 'loading'}
        className="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 disabled:opacity-50"
      >
        {status === 'loading' ? 'Sending...' : 'Submit'}
      </button>
      {status === 'success' && <p className="text-green-600">Message sent!</p>}
      {status === 'error' && <p className="text-red-600">Error sending message.</p>}
    </form>
  );
}
```

### 8. `content/index.md`

```markdown
---
title: "Home"
description: "Welcome to our site"
layout: "default"
---

# Welcome

This is a static site built with React, Vite, and MDX.

<InfoBox type="info">
  You can use React components directly in markdown!
</InfoBox>

## Features

- Fast static site generation
- React components in markdown
- Tailwind CSS styling
- Full TypeScript support

## Contact Us

<ContactForm 
  apiEndpoint="https://api.example.com/contact"
  fields={["name", "email", "message"]}
/>
```

### 9. `Dockerfile`

```dockerfile
FROM node:20-alpine AS build

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

FROM nginx:alpine

COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost/ || exit 1

CMD ["nginx", "-g", "daemon off;"]
```

### 10. `nginx.conf`

```nginx
events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    server {
        listen 80;
        server_name _;
        root /usr/share/nginx/html;
        index index.html;

        # Gzip compression
        gzip on;
        gzip_types text/plain text/css application/json application/javascript text/xml application/xml+rss text/javascript;

        # SPA routing
        location / {
            try_files $uri $uri/ /index.html;
        }

        # Cache static assets
        location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
            expires 1y;
            add_header Cache-Control "public, immutable";
        }
    }
}
```

## Docker Compose Integration

Add to `docker-compose.yml`:
```yaml
##<<SERVICES>>
  ssg-react-{purpose}:
    <<: *default-env
    build:
      context: ./services/ssg-react-{purpose}
      dockerfile: Dockerfile
    container_name: ssg-react-{purpose}
    restart: unless-stopped
    ports:
      - "3000:80"
    networks:
      - app-network
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost/"]
      interval: 30s
      timeout: 10s
      retries: 3
##</SERVICES>>
```

Add to `docker-compose.development.yml`:
```yaml
##<<SERVICES>>
  ssg-react-{purpose}:
    volumes:
      - ./services/ssg-react-{purpose}:/app
      - /app/node_modules
    command: npm run dev
    ports:
      - "3000:3000"
##</SERVICES>>
```

## DevContainer Configuration

Create `.devcontainer/ssg-react-{purpose}-container/devcontainer.json`:
```json
{
  "name": "SSG React - {Purpose}",
  "dockerComposeFile": "../../docker-compose.development.yml",
  "service": "ssg-react-{purpose}",
  "workspaceFolder": "/app",
  "postCreateCommand": "npm install",
  "customizations": {
    "vscode": {
      "extensions": [
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode",
        "bradlc.vscode-tailwindcss",
        "unifiedjs.vscode-mdx"
      ],
      "settings": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.formatOnSave": true
      }
    }
  }
}
```

## Testing

### Vitest (Component Tests)
```typescript
// src/components/__tests__/Layout.test.tsx
import { describe, it, expect } from 'vitest';
import { render, screen } from '@testing-library/react';
import Layout from '../Layout';

describe('Layout', () => {
  it('renders children', () => {
    render(<Layout><div>Test</div></Layout>);
    expect(screen.getByText('Test')).toBeInTheDocument();
  });
});
```

### Playwright (E2E Tests)
```typescript
// tests/e2e/navigation.spec.ts
import { test, expect } from '@playwright/test';

test('homepage loads', async ({ page }) => {
  await page.goto('/');
  await expect(page.locator('h1')).toContainText('Welcome');
});

test('contact form submits', async ({ page }) => {
  await page.goto('/');
  await page.fill('[name="email"]', 'test@example.com');
  await page.fill('[name="message"]', 'Test message');
  await page.click('button[type="submit"]');
  await expect(page.locator('.text-green-600')).toBeVisible();
});
```

## Checklist

### Structure
- [ ] Directory structure created
- [ ] Content folder with sample markdown files
- [ ] Component library (Layout, Header, Footer, SEO)
- [ ] MDX components (CodeBlock, InfoBox, ContactForm)
- [ ] Markdown processing utilities

### Configuration
- [ ] package.json with all dependencies
- [ ] vite.config.ts with MDX plugins
- [ ] tailwind.config.ts with typography plugin
- [ ] tsconfig.json with strict mode
- [ ] .env.example with API endpoints

### Docker
- [ ] Production-ready Dockerfile
- [ ] nginx.conf for SPA routing
- [ ] .dockerignore file
- [ ] Service in docker-compose.yml
- [ ] Dev overrides in docker-compose.development.yml
- [ ] DevContainer configuration

### Testing
- [ ] Vitest setup for components
- [ ] Playwright for E2E
- [ ] Test coverage >80%

### Documentation
- [ ] README.md with setup instructions
- [ ] Sample content files
- [ ] Component usage examples
- [ ] Form integration guide

## Notes

- Static build outputs to `dist/`
- Forms require external API endpoints
- Images in `public/` folder
- MDX allows React components in markdown
- Tailwind typography plugin for markdown styling
