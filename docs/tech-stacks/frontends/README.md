# Frontends - User-Facing Applications

Interactive web applications and mobile apps for end users.

---

## Overview

Frontend services provide user interfaces for your applications - from web dashboards to mobile apps. This includes SPAs (Single Page Applications), SSR (Server-Side Rendered) apps, and cross-platform mobile applications.

---

## Available Technologies

### 1. [React SPA](./react.md) ⭐
**Type:** Single Page Application  
**Language:** TypeScript/JavaScript  
**Best For:** Admin dashboards, internal tools, B2B portals

### 2. [Next.js](./nextjs.md)
**Type:** SSR/SSG Framework  
**Language:** TypeScript/JavaScript  
**Best For:** Marketing sites, SEO-critical pages, blogs, e-commerce

### 3. [Flutter](./flutter.md)
**Type:** Cross-Platform Framework  
**Language:** Dart  
**Best For:** Mobile apps (iOS/Android), kiosk applications, embedded UIs

---

## Technology Comparison

| Feature | React SPA | Next.js | Flutter |
|---------|-----------|---------|---------|
| **Platform** | Web | Web | Mobile/Web/Desktop |
| **Rendering** | Client-side | SSR/SSG | Native |
| **SEO** | ⚠️ Limited | ✅ Excellent | N/A (mobile) |
| **Performance** | ⭐⭐⭐⭐ Fast | ⭐⭐⭐⭐⭐ Fastest | ⭐⭐⭐⭐⭐ Native |
| **Build Time** | ⭐⭐⭐⭐⭐ Fastest | ⭐⭐⭐⭐ Fast | ⭐⭐⭐ Moderate |
| **Ease of Use** | ⭐⭐⭐⭐⭐ Easy | ⭐⭐⭐⭐ Easy | ⭐⭐⭐ Moderate |
| **Mobile Support** | ❌ No | ❌ No | ✅ Native |
| **Hot Reload** | ✅ HMR (instant) | ✅ Fast Refresh | ✅ Hot Reload |
| **State Management** | Zustand/Redux | Server State | Riverpod |
| **Routing** | React Router | Built-in | GoRouter |
| **Deployment** | CDN/Static | Vercel/Node | App Stores |

---

## When to Use Each Technology

### Choose **React SPA** When:
✅ Building **admin dashboards** or **internal tools**  
✅ **SEO is not critical** (authenticated apps)  
✅ Need **fast development** with rich ecosystem  
✅ Want **real-time updates** and interactivity  
✅ Team knows **React** well  
✅ Building **B2B portals** or **SaaS applications**  

**Ideal Use Cases:**
- Admin dashboards
- Internal tools and portals
- B2B applications
- SaaS application interfaces
- Data visualization tools
- Real-time monitoring dashboards

### Choose **Next.js** When:
✅ **SEO is critical** (public-facing pages)  
✅ Need **server-side rendering** for performance  
✅ Building **marketing sites** or **blogs**  
✅ Want **static generation** for fast loads  
✅ Need **API routes** alongside frontend  
✅ Building **e-commerce** sites

**Ideal Use Cases:**
- Marketing websites
- SEO-critical landing pages
- Blogs and content sites
- E-commerce platforms
- Product documentation
- Public-facing applications

### Choose **Flutter** When:
✅ Building **mobile applications** (iOS + Android)  
✅ Need **native performance**  
✅ Want **single codebase** for multiple platforms  
✅ Building **kiosk applications**  
✅ Need **offline-first** capabilities  
✅ Building **embedded UIs**

**Ideal Use Cases:**
- Mobile apps (iOS + Android)
- Tablet applications
- Kiosk interfaces
- Desktop applications
- Embedded UIs
- Cross-platform tools

---

## Quick Decision Guide

```
┌─────────────────────────────────────┐
│ What platform are you targeting?    │
└─────────────────────────────────────┘
           │
           ├─ Mobile (iOS/Android)
           │  → Use Flutter
           │
           └─ Web
              │
              ├─ Is SEO critical?
              │  ├─ YES → Use Next.js (SSR)
              │  └─ NO → Use React SPA
              │
              └─ Is it public-facing?
                 ├─ YES → Use Next.js
                 └─ NO (internal) → Use React SPA

┌─────────────────────────────────────┐
│ What's your use case?               │
└─────────────────────────────────────┘
           │
           ├─ Admin Dashboard
           │  → React SPA (fast development, rich interactivity)
           │
           ├─ Marketing Website
           │  → Next.js (SEO + performance) OR SSG (if purely static)
           │
           ├─ E-commerce
           │  → Next.js (SEO + dynamic content)
           │
           ├─ Mobile App
           │  → Flutter (native performance, cross-platform)
           │
           └─ Internal Tool / Portal
              → React SPA (fast development, no SEO needed)
```

---

## Architecture Patterns

### SPA Pattern (React)
```
Browser → CDN → Static Files (HTML/JS/CSS)
         ↓
      REST API ← React App (Client-Side Rendering)
```
- All rendering happens in browser
- Fetch data from APIs
- Best for authenticated apps

### SSR Pattern (Next.js)
```
Browser → Next.js Server → Render HTML → Return to Browser
                  ↓
               REST API (fetch data)
```
- HTML rendered on server
- Fast initial load
- SEO-friendly

### Mobile Pattern (Flutter)
```
Mobile Device → Flutter App (Native Rendering)
                     ↓
                  REST API (fetch data)
                     ↓
                Local Storage (Offline Mode)
```
- Native performance
- Offline capabilities
- Platform-specific features

---

## Naming Convention

```
frontend-{tech}-{purpose}
```

### Examples
```
frontend-react-dashboard     # React admin dashboard
frontend-react-admin         # React admin panel
frontend-react-portal        # React B2B portal
frontend-nextjs-landing      # Next.js landing page
frontend-nextjs-docs         # Next.js documentation site
frontend-nextjs-blog         # Next.js blog
frontend-flutter-mobile      # Flutter mobile app
frontend-flutter-kiosk       # Flutter kiosk app
```

---

## Common Features

### State Management
- **React SPA:** Zustand (lightweight) or Redux (complex apps)
- **Next.js:** React Query + Server State
- **Flutter:** Riverpod (recommended)

### Routing
- **React SPA:** React Router 6
- **Next.js:** Built-in file-based routing
- **Flutter:** GoRouter

### Data Fetching
- **React SPA:** TanStack Query (React Query)
- **Next.js:** Server Components + React Query
- **Flutter:** Dio + Riverpod

### Styling
- **React SPA:** Tailwind CSS
- **Next.js:** Tailwind CSS + CSS Modules
- **Flutter:** Material Design 3

### Forms
- **React SPA:** React Hook Form + Zod
- **Next.js:** React Hook Form + Server Actions
- **Flutter:** Flutter Form + Validators

---

## Testing Strategy

### Unit Tests
Test individual components and functions:
```typescript
// React/Next.js example
test('renders button with correct text', () => {
  render(<Button>Click me</Button>);
  expect(screen.getByText('Click me')).toBeInTheDocument();
});
```

### Integration Tests
Test component interactions:
```typescript
// React/Next.js example
test('submits form with valid data', async () => {
  render(<LoginForm />);
  await userEvent.type(screen.getByLabelText('Email'), 'test@example.com');
  await userEvent.type(screen.getByLabelText('Password'), 'password123');
  await userEvent.click(screen.getByRole('button', { name: 'Login' }));
  
  expect(mockLogin).toHaveBeenCalledWith({
    email: 'test@example.com',
    password: 'password123',
  });
});
```

### E2E Tests
Test full user flows:
```typescript
// Playwright example
test('user can login and view dashboard', async ({ page }) => {
  await page.goto('/login');
  await page.fill('[name="email"]', 'test@example.com');
  await page.fill('[name="password"]', 'password123');
  await page.click('button[type="submit"]');
  
  await expect(page).toHaveURL('/dashboard');
  await expect(page.locator('h1')).toHaveText('Dashboard');
});
```

---

## Performance Optimization

### React SPA
- Code splitting (React.lazy)
- Virtualization (react-window)
- Memoization (useMemo, React.memo)
- Bundle analysis (webpack-bundle-analyzer)

### Next.js
- Image optimization (next/image)
- Font optimization (next/font)
- Static generation when possible
- Incremental static regeneration (ISR)

### Flutter
- Lazy loading
- Image caching
- Widget optimization
- Platform-specific optimizations

---

## Deployment

### React SPA
- **Build:** `npm run build` → `dist/`
- **Deploy:** Vercel, Netlify, S3+CloudFront, nginx
- **Docker:** Multi-stage (build → nginx serve)

### Next.js
- **Build:** `npm run build` → `.next/`
- **Deploy:** Vercel (recommended), Docker + Node.js
- **SSR Requirement:** Needs Node.js server

### Flutter
- **Build:** `flutter build {platform}`
- **Deploy:** 
  - iOS: App Store (TestFlight for testing)
  - Android: Google Play Store
  - Web: Same as React SPA

---

## Related Documentation

- [SSG Technologies](../ssg/README.md) - For static content websites
- [API Technologies](../apis/README.md) - Backend APIs
- [Naming Conventions](../NAMING-CONVENTIONS.md)
- [Decision Tree](../DECISION-TREE.md)

---

## Next Steps

1. **Choose a technology** based on platform and use case
2. **Review detailed docs**:
   - [React SPA Documentation](./react.md)
   - [Next.js Documentation](./nextjs.md)
   - [Flutter Documentation](./flutter.md)
3. **Follow naming conventions** for your frontend service
4. **Set up development environment** (Dev Container)
5. **Implement authentication** (if needed)
6. **Connect to backend APIs**
7. **Write tests** (unit, integration, E2E)
8. **Deploy** to appropriate platform
