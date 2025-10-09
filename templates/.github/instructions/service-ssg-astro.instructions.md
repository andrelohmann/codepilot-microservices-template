---
applyTo: "**/ssg-astro-*/**"
description: Astro SSG with zero JavaScript, Content Collections, and Tailwind
---

# SSG Astro - Zero JavaScript Static Sites

Build ultra-fast static websites with Astro's zero-JS architecture and Content Collections.

**Reference**: [Complete Astro SSG Tech Stack Documentation](../../../docs/tech-stacks/ssg/astro.md)  
**Scaffold**: Use the `scaffold-ssg-astro-service.prompt.md` for new services

## Naming Convention

`ssg-astro-{purpose}` (e.g., `ssg-astro-marketing`, `ssg-astro-docs`, `ssg-astro-corporate`)

## Project Structure

```
services/ssg-astro-{purpose}/
├── src/
│   ├── pages/              # File-based routing
│   │   ├── index.astro     # → /
│   │   ├── about.astro     # → /about
│   │   └── products/
│   │       └── [slug].astro # → /products/:slug
│   ├── content/            # Content Collections
│   │   ├── config.ts       # Schemas
│   │   └── products/
│   ├── components/         # Astro components
│   └── layouts/
└── astro.config.mjs
```

## Quick Reference Patterns

### 1. Content Collections Schema

```typescript
// src/content/config.ts
import { defineCollection, z } from 'astro:content';

const productsCollection = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    description: z.string(),
    price: z.number(),
  }),
});

export const collections = {
  products: productsCollection,
};
```

### 2. Dynamic Page with Content

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
  <h1>{product.data.title}</h1>
  <Content />
</BaseLayout>
```

### 3. Astro Component

```astro
---
// src/components/ProductCard.astro
interface Props {
  title: string;
  price: number;
}

const { title, price } = Astro.props;
---

<div class="card">
  <h3>{title}</h3>
  <p>${price}</p>
</div>

<style>
  .card { padding: 1rem; border: 1px solid #ccc; }
</style>
```

### 4. Form Integration

```astro
---
// Forms POST to API endpoints
---

<form action="https://api.example.com/contact" method="POST">
  <input name="email" type="email" required />
  <button type="submit">Submit</button>
</form>
```

## Code Style

- File-based routing in `/src/pages/`
- Content Collections for type-safe markdown
- Zero JavaScript by default (pure HTML/CSS output)
- Add islands only when interactivity needed
- Tailwind CSS for styling
- MDX for components in markdown
- Content schemas with Zod validation
- Image optimization with `@astrojs/image`
- SEO-friendly with automatic sitemaps
- Perfect Lighthouse scores (95-100)

## Anti-Patterns

- ❌ Using for highly interactive apps (use frontend-react-* or frontend-nextjs-*)
- ❌ Adding unnecessary JavaScript/frameworks
- ❌ Not using Content Collections for content
- ❌ Not optimizing images
