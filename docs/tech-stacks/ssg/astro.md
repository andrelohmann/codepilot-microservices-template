# Astro SSG - Zero JavaScript Static Site Generator

Build ultra-fast static websites with zero JavaScript by default using Astro's island architecture.

---

## Overview

**Naming:** `ssg-astro-{purpose}`

**Examples:** `ssg-astro-marketing`, `ssg-astro-docs`, `ssg-astro-corporate`

**Astro** is the **fastest** option for static sites, shipping zero JavaScript by default and achieving perfect Lighthouse scores.

---

## Technology Stack

- **Astro 4.0+** - Static site framework
- **Tailwind CSS 3+** - Utility-first styling
- **Content Collections** - Type-safe content management
- **MDX** - Markdown with components
- **@astrojs/sitemap** - Automatic sitemaps
- **@astrojs/image** - Image optimization

---

## Why Astro?

✅ **Zero JS by default** - Only HTML/CSS shipped  
✅ **Perfect Lighthouse scores** (95-100)  
✅ **Content Collections** - TypeScript-validated content  
✅ **Island architecture** - Add React/Vue only where needed  
✅ **Fastest builds** - Vite-powered  
✅ **Best for SEO** - Pure static HTML

---

## Project Structure

```
ssg-astro-{purpose}/
├── src/
│   ├── pages/                 # File-based routing
│   │   ├── index.astro       # Homepage
│   │   ├── about.astro       # /about
│   │   └── products/
│   │       └── [slug].astro  # /products/:slug
│   ├── content/              # Content Collections
│   │   ├── config.ts         # Content schemas
│   │   ├── products/
│   │   │   ├── service-a.md
│   │   │   └── service-b.md
│   │   └── blog/
│   │       └── post-1.md
│   ├── components/           # Astro components
│   │   ├── Header.astro
│   │   ├── Footer.astro
│   │   └── ProductCard.astro
│   ├── layouts/
│   │   └── BaseLayout.astro
│   └── styles/
│       └── global.css
├── public/
│   └── images/
├── astro.config.mjs
├── tailwind.config.mjs
└── Dockerfile
```

---

## Code Examples

### Content Collections Schema

```typescript
// src/content/config.ts
import { defineCollection, z } from 'astro:content';

const productsCollection = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    description: z.string(),
    image: z.string(),
    price: z.number(),
    features: z.array(z.string()),
  }),
});

export const collections = {
  products: productsCollection,
};
```

### Markdown Content

```markdown
---
# src/content/products/service-a.md
title: "Service A - Enterprise Solutions"
description: "Transform your business"
image: "/images/service-a.jpg"
price: 299
features:
  - "Fast Performance"
  - "Enterprise Security"
  - "24/7 Support"
---

# Service A

Our flagship enterprise solution delivers...
```

### Dynamic Page

```astro
---
// src/pages/products/[slug].astro
import { getCollection } from 'astro:content';
import BaseLayout from '../../layouts/BaseLayout.astro';

export async function getStaticPaths() {
  const products = await getCollection('products');
  return products.map(product => ({
    params: { slug: product.slug },
    props: { product },
  }));
}

const { product } = Astro.props;
const { Content } = await product.render();
---

<BaseLayout title={product.data.title}>
  <img src={product.data.image} alt={product.data.title} />
  <h1>{product.data.title}</h1>
  <p>{product.data.description}</p>
  <Content />
  <p class="text-2xl font-bold">${product.data.price}/mo</p>
</BaseLayout>
```

### Form with API Integration

```astro
---
// src/components/ContactForm.astro
---
<form id="contact-form" class="space-y-4">
  <input type="text" name="name" required 
    class="w-full px-4 py-2 border rounded" 
    placeholder="Name" />
  <input type="email" name="email" required 
    class="w-full px-4 py-2 border rounded" 
    placeholder="Email" />
  <textarea name="message" required 
    class="w-full px-4 py-2 border rounded" 
    placeholder="Message"></textarea>
  <button type="submit" 
    class="bg-blue-600 text-white px-6 py-2 rounded hover:bg-blue-700">
    Send Message
  </button>
</form>

<script>
  const form = document.getElementById('contact-form') as HTMLFormElement;
  form?.addEventListener('submit', async (e) => {
    e.preventDefault();
    const formData = new FormData(form);
    
    try {
      const response = await fetch('https://api.example.com/contact', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(Object.fromEntries(formData)),
      });
      
      if (response.ok) {
        alert('Message sent successfully!');
        form.reset();
      }
    } catch (error) {
      alert('Failed to send message');
    }
  });
</script>
```

### React Island (Optional Interactivity)

```astro
---
// src/pages/index.astro
import InteractiveCounter from '../components/InteractiveCounter';
---

<BaseLayout>
  <h1>Welcome</h1>
  <p>This content is static HTML with zero JS</p>
  
  <!-- Only this component loads React -->
  <InteractiveCounter client:load />
</BaseLayout>
```

```tsx
// src/components/InteractiveCounter.tsx
import { useState } from 'react';

export default function InteractiveCounter() {
  const [count, setCount] = useState(0);
  
  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

---

## Configuration

```javascript
// astro.config.mjs
import { defineConfig } from 'astro/config';
import tailwind from '@astrojs/tailwind';
import sitemap from '@astrojs/sitemap';
import react from '@astrojs/react'; // Optional

export default defineConfig({
  site: 'https://example.com',
  output: 'static',
  integrations: [
    tailwind(),
    sitemap(),
    react(), // Only if using React islands
  ],
});
```

```javascript
// tailwind.config.mjs
export default {
  content: ['./src/**/*.{astro,html,js,jsx,md,mdx,ts,tsx}'],
  theme: {
    extend: {
      colors: {
        brand: {
          primary: '#0066CC',
          secondary: '#FF6B35',
        },
      },
    },
  },
};
```

---

## Deployment

### Build

```bash
npm run build
# Output: dist/
```

### Docker

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

## Performance Benefits

- **Lighthouse Score:** 95-100
- **Time to Interactive:** < 0.5s
- **Bundle Size:** 15-20 KB (vs 55 KB React)
- **Build Time:** Fast (Vite-powered)
- **First Contentful Paint:** < 0.3s

---

## Best Practices

✅ Use Content Collections for type safety  
✅ Keep JavaScript minimal (islands only)  
✅ Optimize images with Astro's image component  
✅ Use Tailwind for consistent styling  
✅ Implement proper SEO metadata  
✅ Generate sitemaps automatically  
✅ Use lazy loading for images

---

## Related Documentation

- [SSG Overview](./README.md)
- [React SSG](./react.md) - More interactive alternative
- [Comparison Guide](./COMPARISON.md)

---

**Reference Files:**
- Instruction: `service-ssg-astro.instructions.md`
- Scaffold: `scaffold-ssg-astro-service.md`
