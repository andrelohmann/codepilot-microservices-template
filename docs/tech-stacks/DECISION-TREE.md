# Technology Selection Decision Tree

This guide helps you choose the right technology stack for your microservice.

## Decision Tree

```
Need a service? Start here:
│
├─ User-facing content/application?
│  ├─ YES → What kind?
│  │  │
│  │  ├─ Static content website (company site, marketing, docs)?
│  │  │  └─ YES → Performance priority?
│  │  │     ├─ Maximum performance (zero JS) → ⭐ ssg-astro-*
│  │  │     │                                    (Astro + Tailwind)
│  │  │     │                                    Perfect Lighthouse scores
│  │  │     │                                    Content Collections API
│  │  │     │
│  │  │     └─ More interactivity needed → ssg-react-*
│  │  │                                     (React + Vite + MDX + Tailwind)
│  │  │                                     Full React ecosystem
│  │  │                                     MDX components
│  │  │
│  │  ├─ Interactive web app (dashboard, portal, tool)?
│  │  │  ├─ SEO critical?
│  │  │  │  ├─ YES → frontend-nextjs-* 
│  │  │  │  │         (Server-side rendering)
│  │  │  │  │         SEO-optimized
│  │  │  │  │         Image optimization
│  │  │  │  │
│  │  │  │  └─ NO → frontend-react-*
│  │  │  │           (Client-side SPA)
│  │  │  │           Fastest development
│  │  │  │           Best for dashboards
│  │  │  │
│  │  │  └─ Internal tool → frontend-react-*
│  │  │                      (Admin dashboards)
│  │  │                      Internal portals
│  │  │
│  │  └─ Mobile or cross-platform app?
│  │     └─ YES → frontend-flutter-*
│  │               (iOS + Android + Web + Desktop)
│  │               Single codebase
│  │               Native performance
│  │
│  └─ NO → Backend service needed ↓
│
├─ REST/GraphQL API?
│  └─ YES → Language preference?
│     ├─ Python → api-fastapi-*
│     │           (Async/await support)
│     │           Auto OpenAPI docs
│     │           Type hints + Pydantic
│     │           Perfect for: ML serving, data APIs
│     │
│     ├─ Node.js/TypeScript → api-nestjs-*
│     │                       (Modular architecture)
│     │                       Dependency injection
│     │                       GraphQL support
│     │                       Perfect for: Enterprise APIs, microservices
│     │
│     └─ Go → api-fiber-*
│               (High performance)
│               Low memory footprint
│               Express-like API
│               Perfect for: High-throughput, low-latency
│
├─ Background/async jobs?
│  └─ YES → Language preference?
│     ├─ Node.js → worker-bullmq-*
│     │            (Redis-backed queue)
│     │            Bull Board monitoring
│     │            Job priorities + delays
│     │            Perfect for: Email, reports, notifications
│     │
│     ├─ Python → worker-celery-*
│     │           (Mature, flexible)
│     │           Celery Beat scheduler
│     │           Multiple brokers
│     │           Perfect for: ETL, ML training, analytics
│     │
│     └─ Go → worker-golang-*
│               (High throughput)
│               No external dependencies
│               Worker pool pattern
│               Perfect for: Real-time pipelines, scraping
│
├─ Real-time streaming/events?
│  └─ YES → stream-golang-*
│            (Kafka/NATS support)
│            High-performance processing
│            Windowing operations
│            Perfect for: Log aggregation, event processing
│
└─ Gateway/proxy/load balancer?
   └─ YES → Use case?
      ├─ Modern microservices (Docker) → gateway-traefik-*
      │                                   (Auto service discovery)
      │                                   Auto SSL (Let's Encrypt)
      │                                   Dynamic configuration
      │
      └─ Traditional setup / static files → gateway-nginx-*
                                            (High performance)
                                            Caching layers
                                            Static file serving
```

---

## Quick Decision Guide

### I need a company website

**Requirements:**
- Multiple pages (About, Products, Contact, etc.)
- Content written by non-developers
- Blog or news section
- Contact forms
- Best possible performance and SEO

**Recommendation:**
- 🥇 **`ssg-astro-marketing`** (Astro + Tailwind)
  - Zero JS by default
  - Perfect Lighthouse scores (95-100)
  - Content Collections API
  - Fastest page loads

- 🥈 **`ssg-react-marketing`** (React + Vite + MDX + Tailwind)
  - More client-side interactivity
  - Full React ecosystem
  - MDX components in content

**See:** [SSG Comparison](./ssg/COMPARISON.md) for detailed comparison

---

### I need a SaaS web application

**Requirements:**
- User authentication
- Interactive dashboard
- Real-time updates
- Admin panel

**Recommendation:**
- **Frontend:** `frontend-react-dashboard` (React SPA)
  - Fast development
  - Rich component ecosystem
  - TanStack Query for data fetching
  
- **Backend API:** `api-nestjs-core` or `api-fastapi-core`
  - Choose based on team expertise
  - NestJS for TypeScript teams
  - FastAPI for Python teams

---

### I need a product landing page

**Requirements:**
- SEO optimization
- Fast initial load
- Marketing content
- Lead capture forms

**Recommendation:**
- 🥇 **`frontend-nextjs-landing`** (Next.js SSR)
  - Server-side rendering for SEO
  - Image optimization
  - API routes for forms
  
- 🥈 **`ssg-react-product`** (if content is fully static)
  - Pre-rendered HTML
  - Fastest page loads
  - Forms POST to API

---

### I need a mobile application

**Requirements:**
- iOS + Android support
- Native performance
- Offline mode
- Push notifications

**Recommendation:**
- **`frontend-flutter-mobile`** (Flutter)
  - Single codebase for iOS/Android
  - Native performance
  - Material Design 3
  - Riverpod for state management

---

### I need a backend API

**Requirements:**
- User authentication
- Database operations (CRUD)
- Business logic
- RESTful endpoints

**Choose based on:**

**Python project?**
- **`api-fastapi-auth`**
  - Async/await support
  - Auto-generated docs
  - Perfect for data-intensive APIs

**Node.js project?**
- **`api-nestjs-auth`**
  - TypeScript + decorators
  - Modular architecture
  - Perfect for enterprise APIs

**High performance needed?**
- **`api-fiber-auth`**
  - Go's concurrency
  - Low memory footprint
  - Perfect for high-throughput

---

### I need background workers

**Requirements:**
- Sending emails
- Generating reports
- Processing uploads
- Scheduled tasks

**Choose based on:**

**Node.js project?**
- **`worker-bullmq-email`**
  - Redis-backed queues
  - Bull Board monitoring UI
  - Perfect for: Email, reports, notifications

**Python project?**
- **`worker-celery-reports`**
  - Mature and flexible
  - Celery Beat for scheduling
  - Perfect for: ETL, ML training, analytics

**High throughput needed?**
- **`worker-golang-processor`**
  - Native Go concurrency
  - No external dependencies
  - Perfect for: Real-time pipelines

---

## Decision Criteria

### Performance Requirements

| Need | Recommended Stack |
|------|------------------|
| **Ultra-high performance** | Go Fiber (API) or Go Worker |
| **High throughput** | Go Fiber (API) or Go Stream |
| **Low latency** | Go Fiber (API) |
| **Fast page loads** | Astro (SSG) |
| **Zero JavaScript** | Astro (SSG) |

### Team Expertise

| Team Skills | Recommended Stack |
|-------------|------------------|
| **Python** | FastAPI (API), Celery (Worker) |
| **Node.js/TypeScript** | NestJS (API), BullMQ (Worker) |
| **Go** | Fiber (API), Golang (Worker/Stream) |
| **React** | React SPA (Frontend), React SSG (Static) |
| **Mobile** | Flutter |

### Project Type

| Project Type | Recommended Stack |
|--------------|------------------|
| **Corporate website** | Astro (SSG) |
| **Documentation** | React SSG or Astro (SSG) |
| **Marketing site** | Astro (SSG) or Next.js (Frontend) |
| **Admin dashboard** | React SPA (Frontend) |
| **Mobile app** | Flutter (Frontend) |
| **REST API** | FastAPI, NestJS, or Fiber (API) |
| **Background jobs** | BullMQ, Celery, or Golang (Worker) |
| **Real-time processing** | Golang (Stream) |
| **API Gateway** | Traefik or Nginx (Gateway) |

### Scalability Needs

| Requirement | Recommended Stack |
|-------------|------------------|
| **Horizontal scaling** | Any containerized service |
| **High concurrency** | Go Fiber (API), Go Worker |
| **Async processing** | FastAPI (API), Celery (Worker) |
| **Event-driven** | Golang Stream |
| **Queue-based** | BullMQ (Worker), Celery (Worker) |

---

## Common Patterns

### Pattern: Full-Stack SaaS Application

```
Frontend: frontend-react-dashboard
Backend API: api-nestjs-core
Background Jobs: worker-bullmq-email
Gateway: gateway-traefik-main
```

### Pattern: Content-Heavy Website

```
Static Site: ssg-astro-marketing
Contact Form API: api-fastapi-contact (optional)
Gateway: gateway-nginx-cdn
```

### Pattern: E-Commerce Platform

```
Frontend: frontend-nextjs-shop (SSR for SEO)
Product API: api-nestjs-products
Order Processing: worker-celery-orders
Payment API: api-fastapi-payments
Gateway: gateway-traefik-main
```

### Pattern: Real-Time Analytics

```
Data Ingestion: stream-golang-analytics
Storage API: api-fiber-timeseries
Dashboard: frontend-react-analytics
Gateway: gateway-traefik-main
```

### Pattern: Mobile App + Backend

```
Mobile App: frontend-flutter-mobile
Backend API: api-nestjs-mobile
Push Notifications: worker-bullmq-notifications
Gateway: gateway-traefik-main
```

---

## Anti-Patterns (What NOT to Do)

### ❌ Using Frontend Stack for Static Content

**Wrong:**
```
frontend-react-docs  # Documentation is static content
```

**Correct:**
```
ssg-react-docs      # Use SSG for static content
```

### ❌ Using API Stack for Background Jobs

**Wrong:**
```
api-fastapi-email   # Email sending is async work
```

**Correct:**
```
worker-celery-email # Use worker for background tasks
```

### ❌ Using Worker Stack for CRUD Operations

**Wrong:**
```
worker-bullmq-users # CRUD operations are synchronous
```

**Correct:**
```
api-nestjs-users    # Use API for CRUD operations
```

### ❌ Wrong Service Type for Use Case

**Wrong:**
```
ssg-react-dashboard # Dashboards are interactive, not static
```

**Correct:**
```
frontend-react-dashboard # Use frontend for interactive apps
```

---

## Still Unsure?

### Decision Helper Questions

1. **Is it user-facing?**
   - YES → Frontend or SSG
   - NO → API, Worker, Stream, or Gateway

2. **Does it serve static content?**
   - YES → SSG (Astro or React)
   - NO → Continue...

3. **Does it need SEO?**
   - YES → Next.js (Frontend) or Astro (SSG)
   - NO → React SPA (Frontend)

4. **Is it a REST/GraphQL API?**
   - YES → API (FastAPI, NestJS, or Fiber)
   - NO → Continue...

5. **Does it run background tasks?**
   - YES → Worker (BullMQ, Celery, or Golang)
   - NO → Continue...

6. **Does it process real-time events?**
   - YES → Stream (Golang)
   - NO → Continue...

7. **Is it a gateway/proxy?**
   - YES → Gateway (Traefik or Nginx)
   - NO → Review the decision tree again

---

## See Also

- **[Naming Conventions](./NAMING-CONVENTIONS.md)** - How to name your service
- **[Tech Stacks Overview](./README.md)** - All available stacks
- **[SSG Comparison](./ssg/COMPARISON.md)** - Astro vs React vs Next.js

---

**Last Updated:** 2025-10-09  
**Template Version:** 1.0.0
