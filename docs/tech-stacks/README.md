# Technology Stacks Reference

This directory contains comprehensive documentation for all supported technology stacks in the microservices template.

## 📚 Navigation

### Core Documentation

- **[Naming Conventions](./NAMING-CONVENTIONS.md)** - Service naming patterns and directory mapping
- **[Decision Tree](./DECISION-TREE.md)** - Technology selection guide
- **[Anti-Patterns](./ANTI-PATTERNS.md)** - Common mistakes and how to avoid them
- **[Adding New Stacks](./ADDING-NEW-STACKS.md)** - Guide for extending the template

### Service Types

| Service Type | Description | Technologies | Documentation |
|--------------|-------------|--------------|---------------|
| **APIs** | RESTful/GraphQL backend services | FastAPI, NestJS, Fiber | [→ apis/](./apis/) |
| **Workers** | Background job processors | BullMQ, Celery, Golang | [→ workers/](./workers/) |
| **Frontends** | User-facing applications | React, Next.js, Flutter | [→ frontends/](./frontends/) |
| **SSG** | Static site generators | Astro, React+Vite+MDX | [→ ssg/](./ssg/) |
| **Gateways** | API proxies & load balancers | Traefik, Nginx | [→ gateways/](./gateways/) |
| **Streams** | Real-time data processing | Go+Kafka/NATS | [→ streams/](./streams/) |

---

## 🚀 Quick Reference

### Service Naming Format

```
{type}-{tech}-{purpose}
```

**Examples:**
- `api-fastapi-auth` - Python FastAPI authentication service
- `worker-bullmq-email` - Node.js BullMQ email worker
- `frontend-react-dashboard` - React admin dashboard
- `ssg-astro-marketing` - Astro static marketing site

### Technology Quick Picks

| Need | Recommended Stack | Alternative |
|------|------------------|-------------|
| **Python API** | `api-fastapi-*` ⭐ | - |
| **Node.js API** | `api-nestjs-*` ⭐ | - |
| **High-Performance API** | `api-fiber-*` ⭐ | - |
| **Node.js Worker** | `worker-bullmq-*` ⭐ | - |
| **Python Worker** | `worker-celery-*` ⭐ | - |
| **Web Dashboard** | `frontend-react-*` ⭐ | `frontend-nextjs-*` |
| **Mobile App** | `frontend-flutter-*` ⭐ | - |
| **Company Website** | `ssg-astro-*` ⭐ | `ssg-react-*` |
| **Documentation Site** | `ssg-react-*` ⭐ | `ssg-astro-*` |
| **API Gateway** | `gateway-traefik-*` ⭐ | `gateway-nginx-*` |
| **Event Processing** | `stream-golang-*` ⭐ | - |

---

## 📖 Detailed Documentation

### [APIs - Backend Services](./apis/)

RESTful and GraphQL API services for business logic and data access.

- **[FastAPI](./apis/fastapi.md)** - Python async API framework
  - Use for: Data-intensive APIs, ML model serving, async I/O operations
  - Examples: `api-fastapi-auth`, `api-fastapi-billing`

- **[NestJS](./apis/nestjs.md)** - Node.js TypeScript framework
  - Use for: Enterprise APIs, microservices, complex business logic
  - Examples: `api-nestjs-payments`, `api-nestjs-notifications`

- **[Fiber](./apis/fiber.md)** - Go high-performance framework
  - Use for: High-throughput APIs, low-latency requirements, system services
  - Examples: `api-fiber-gateway`, `api-fiber-metrics`

### [Workers - Background Job Processors](./workers/)

Asynchronous task execution, scheduled jobs, and queue processing.

- **[BullMQ](./workers/bullmq.md)** - Node.js Redis-backed queue
  - Use for: Email sending, report generation, image processing
  - Examples: `worker-bullmq-email`, `worker-bullmq-reports`

- **[Celery](./workers/celery.md)** - Python distributed task queue
  - Use for: ETL processes, ML model training, scheduled reports
  - Examples: `worker-celery-etl`, `worker-celery-analytics`

- **[Golang Worker](./workers/golang.md)** - Go native worker pools
  - Use for: High-throughput processing, real-time pipelines
  - Examples: `worker-golang-indexer`, `worker-golang-sync`

### [Frontends - User-Facing Applications](./frontends/)

Interactive web and mobile applications.

- **[React SPA](./frontends/react.md)** - React single-page application
  - Use for: Admin dashboards, internal tools, B2B portals
  - Examples: `frontend-react-dashboard`, `frontend-react-admin`

- **[Next.js](./frontends/nextjs.md)** - React with SSR/SSG
  - Use for: Marketing sites, SEO-critical pages, blogs, e-commerce
  - Examples: `frontend-nextjs-landing`, `frontend-nextjs-blog`

- **[Flutter](./frontends/flutter.md)** - Cross-platform mobile/web
  - Use for: Mobile apps, kiosk applications, embedded UIs
  - Examples: `frontend-flutter-mobile`, `frontend-flutter-kiosk`

### [SSG - Static Site Generators](./ssg/)

Static websites built from markdown content with component-based architecture.

- **[Astro](./ssg/astro.md)** - Zero-JS static site generator ⭐
  - Use for: Corporate websites, pure static sites, best performance
  - Examples: `ssg-astro-marketing`, `ssg-astro-corporate`

- **[React SSG](./ssg/react.md)** - React + Vite + MDX static generator
  - Use for: Company websites with interactivity, documentation
  - Examples: `ssg-react-marketing`, `ssg-react-docs`

- **[SSG Comparison](./ssg/COMPARISON.md)** - Detailed comparison guide

### [Gateways - API Proxies & Traffic Management](./gateways/)

Entry points, reverse proxies, load balancing, and SSL/TLS termination.

- **[Traefik](./gateways/traefik.md)** - Modern cloud-native proxy
  - Use for: Modern microservices, Docker environments, automatic SSL
  - Examples: `gateway-traefik-main`, `gateway-traefik-internal`

- **[Nginx](./gateways/nginx.md)** - Traditional high-performance proxy
  - Use for: Traditional deployments, static file serving, caching layers
  - Examples: `gateway-nginx-cdn`, `gateway-nginx-cache`

### [Streams - Real-Time Data Processing](./streams/)

Event streaming, real-time analytics, and data pipelines.

- **[Golang Stream](./streams/golang.md)** - Go + Kafka/NATS processor
  - Use for: Log aggregation, metrics collection, event processing, CDC
  - Examples: `stream-golang-analytics`, `stream-golang-logs`

---

## 🎯 Decision Making

### I need a...

**Backend API?**
- Python project → [FastAPI](./apis/fastapi.md)
- Node.js project → [NestJS](./apis/nestjs.md)
- High performance needed → [Fiber](./apis/fiber.md)

**Background Worker?**
- Node.js project → [BullMQ](./workers/bullmq.md)
- Python project → [Celery](./workers/celery.md)
- High throughput needed → [Golang Worker](./workers/golang.md)

**User Interface?**
- Interactive dashboard → [React SPA](./frontends/react.md)
- SEO-critical website → [Next.js](./frontends/nextjs.md)
- Mobile app → [Flutter](./frontends/flutter.md)

**Static Website?**
- Maximum performance → [Astro](./ssg/astro.md) ⭐
- More interactivity → [React SSG](./ssg/react.md)
- See [comparison](./ssg/COMPARISON.md) for details

**Gateway/Proxy?**
- Modern Docker setup → [Traefik](./gateways/traefik.md)
- Traditional setup → [Nginx](./gateways/nginx.md)

**Real-time Processing?**
- Event streams → [Golang Stream](./streams/golang.md)

---

## 📝 Additional Resources

- **[Architecture Components](../ARCHITECTURE-COMPONENTS.md)** - System architecture overview
- **[Microservices Catalog](../MICROSERVICES-COMPONENT-CATALOG.md)** - Component catalog

---

**Last Updated:** 2025-10-09  
**Template Version:** 1.0.0
