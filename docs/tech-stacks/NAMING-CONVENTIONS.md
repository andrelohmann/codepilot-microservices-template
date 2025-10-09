# Service Naming Conventions

This document defines the standardized naming patterns for all microservices in this template.

## Naming Format

```
{type}-{tech}-{purpose}
```

### Components

1. **`type`** - Service category (api, worker, frontend, ssg, gateway, stream)
2. **`tech`** - Technology identifier (fastapi, nestjs, bullmq, react, etc.)
3. **`purpose`** - Business domain (auth, billing, email, dashboard, etc.)

---

## Service Type Prefixes

| Type | Prefix | Description | Examples |
|------|--------|-------------|----------|
| **API** | `api-` | RESTful/GraphQL backend services | `api-fastapi-auth`, `api-nestjs-billing` |
| **Worker** | `worker-` | Background job processors | `worker-bullmq-email`, `worker-celery-reports` |
| **Frontend** | `frontend-` | User-facing applications | `frontend-react-dashboard`, `frontend-nextjs-landing` |
| **SSG** | `ssg-` | Static site generators | `ssg-astro-marketing`, `ssg-react-docs` |
| **Gateway** | `gateway-` | API gateways/proxies | `gateway-traefik-main`, `gateway-nginx-cdn` |
| **Stream** | `stream-` | Real-time data processors | `stream-golang-analytics`, `stream-golang-logs` |

---

## Technology Identifiers

### APIs
- `fastapi` - Python FastAPI
- `nestjs` - Node.js NestJS
- `fiber` - Go Fiber

### Workers
- `bullmq` - Node.js BullMQ
- `celery` - Python Celery
- `golang` - Go standard worker

### Frontends
- `react` - React SPA
- `nextjs` - Next.js (React with SSR)
- `flutter` - Flutter (mobile/web)

### SSG (Static Site Generators)
- `astro` - Astro + Tailwind
- `react` - React + Vite + MDX

### Gateways
- `traefik` - Traefik reverse proxy
- `nginx` - Nginx

### Stream Processing
- `golang` - Go + Kafka/NATS

---

## Naming Examples

### API Services

```
api-fastapi-auth          # Python FastAPI authentication service
api-fastapi-billing       # Python FastAPI billing service
api-fastapi-users         # Python FastAPI user management

api-nestjs-payments       # Node.js NestJS payment processing
api-nestjs-notifications  # Node.js NestJS notification service
api-nestjs-catalog        # Node.js NestJS product catalog

api-fiber-gateway         # Go Fiber API gateway
api-fiber-metrics         # Go Fiber metrics aggregation
api-fiber-proxy           # Go Fiber reverse proxy
```

### Worker Services

```
worker-bullmq-email       # Node.js BullMQ email sender
worker-bullmq-reports     # Node.js BullMQ report generator
worker-bullmq-notifications # Node.js BullMQ push notifications

worker-celery-etl         # Python Celery ETL pipeline
worker-celery-analytics   # Python Celery analytics processor
worker-celery-ml          # Python Celery ML model training

worker-golang-indexer     # Go native search indexer
worker-golang-sync        # Go native data synchronizer
worker-golang-scraper     # Go native web scraper
```

### Frontend Services

```
frontend-react-dashboard  # React SPA admin dashboard
frontend-react-admin      # React SPA admin panel
frontend-react-portal     # React SPA user portal

frontend-nextjs-landing   # Next.js marketing landing page
frontend-nextjs-docs      # Next.js documentation site
frontend-nextjs-blog      # Next.js blog platform

frontend-flutter-mobile   # Flutter mobile application
frontend-flutter-kiosk    # Flutter kiosk interface
frontend-flutter-tablet   # Flutter tablet application
```

### SSG Services

```
ssg-astro-marketing       # Astro static marketing site (fastest)
ssg-astro-corporate       # Astro corporate website
ssg-astro-docs            # Astro documentation portal

ssg-react-docs            # React static documentation site
ssg-react-marketing       # React static marketing site (more interactive)
ssg-react-blog            # React static blog platform
```

### Gateway Services

```
gateway-traefik-main      # Main Traefik gateway
gateway-traefik-internal  # Internal Traefik gateway
gateway-traefik-public    # Public-facing Traefik gateway

gateway-nginx-cdn         # Nginx CDN/caching layer
gateway-nginx-cache       # Nginx reverse proxy cache
gateway-nginx-lb          # Nginx load balancer
```

### Stream Processing Services

```
stream-golang-analytics   # Go real-time analytics processor
stream-golang-logs        # Go log aggregation pipeline
stream-golang-events      # Go event stream processor
```

---

## Directory Mapping

### Service Directory Structure

```
services/{type}-{tech}-{purpose}/
```

**Examples:**
```
services/
├── api-fastapi-auth/
├── api-nestjs-billing/
├── worker-bullmq-email/
├── worker-celery-reports/
├── frontend-react-dashboard/
├── frontend-nextjs-landing/
├── ssg-astro-marketing/
├── ssg-react-docs/
├── gateway-traefik-main/
└── stream-golang-analytics/
```

### DevContainer Mapping

Pattern: `.devcontainer/{service-name}-container/devcontainer.json`

```
.devcontainer/
├── api-fastapi-auth-container/
│   └── devcontainer.json
├── worker-bullmq-email-container/
│   └── devcontainer.json
├── frontend-react-dashboard-container/
│   └── devcontainer.json
├── ssg-astro-marketing-container/
│   └── devcontainer.json
└── stream-golang-analytics-container/
    └── devcontainer.json
```

### Instruction File Mapping

Pattern: `templates/.github/instructions/service-{type}-{tech}.instructions.md`

```
API Services:
  api-fastapi-*        → service-api-fastapi.instructions.md
  api-nestjs-*         → service-api-nestjs.instructions.md
  api-fiber-*          → service-api-fiber.instructions.md

Worker Services:
  worker-bullmq-*      → service-worker-bullmq.instructions.md
  worker-celery-*      → service-worker-celery.instructions.md
  worker-golang-*      → service-worker-golang.instructions.md

Frontend Services:
  frontend-react-*     → service-frontend-react.instructions.md
  frontend-nextjs-*    → service-frontend-nextjs.instructions.md
  frontend-flutter-*   → service-frontend-flutter.instructions.md

SSG Services:
  ssg-astro-*          → service-ssg-astro.instructions.md
  ssg-react-*          → service-ssg-react.instructions.md

Gateway Services:
  gateway-traefik-*    → service-gateway-traefik.instructions.md
  gateway-nginx-*      → service-gateway-nginx.instructions.md

Stream Services:
  stream-golang-*      → service-stream-golang.instructions.md
```

### Scaffold Prompt Mapping

Pattern: `templates/.github/prompts/scaffold-{type}-{tech}-service.md`

```
Scaffold Prompts:
  scaffold-api-fastapi-service.md
  scaffold-api-nestjs-service.md
  scaffold-api-fiber-service.md
  scaffold-worker-bullmq-service.md
  scaffold-worker-celery-service.md
  scaffold-worker-golang-service.md
  scaffold-frontend-react-service.md
  scaffold-frontend-nextjs-service.md
  scaffold-frontend-flutter-service.md
  scaffold-ssg-astro-service.md
  scaffold-ssg-react-service.md
  scaffold-gateway-traefik-service.md
  scaffold-gateway-nginx-service.md
  scaffold-stream-golang-service.md
```

---

## Naming Rules

### ✅ DO

1. **Use specific framework names**
   - ✅ `api-fastapi-auth` (FastAPI, not just "Python")
   - ✅ `worker-bullmq-email` (BullMQ, not just "Node.js")
   - ✅ `frontend-react-dashboard` (React, not just "JavaScript")

2. **Use descriptive business domains**
   - ✅ `api-nestjs-payments` (clear purpose)
   - ✅ `worker-celery-reports` (clear task type)
   - ✅ `frontend-react-analytics` (clear function)

3. **Keep names concise but meaningful**
   - ✅ `api-fastapi-auth`
   - ✅ `worker-bullmq-email`
   - ✅ `ssg-astro-marketing`

4. **Use kebab-case (lowercase with hyphens)**
   - ✅ `api-fastapi-user-management`
   - ✅ `worker-celery-data-pipeline`

### ❌ DON'T

1. **Use generic or vague names**
   - ❌ `service-1`, `backend`, `frontend`
   - ❌ `api-python-service`, `worker-node-jobs`

2. **Mix naming styles**
   - ❌ `apiService`, `API_Service`, `Api-Service`
   - ✅ `api-fastapi-service`

3. **Omit service type or technology**
   - ❌ `auth-service` (missing type and tech)
   - ❌ `fastapi-auth` (missing type)
   - ✅ `api-fastapi-auth`

4. **Use wrong service type**
   - ❌ `frontend-react-docs` (docs are static → use `ssg-*`)
   - ❌ `api-fastapi-email` (email is async → use `worker-*`)
   - ❌ `worker-bullmq-users` (CRUD is sync → use `api-*`)

---

## Purpose Naming Guidelines

### Good Purpose Names

**Be Specific:**
- ✅ `auth` - authentication
- ✅ `billing` - billing/payments
- ✅ `email` - email sending
- ✅ `reports` - report generation
- ✅ `analytics` - analytics processing
- ✅ `notifications` - push notifications

**Domain-Driven:**
- ✅ `orders` - order management
- ✅ `inventory` - inventory tracking
- ✅ `customers` - customer management
- ✅ `products` - product catalog

**Functional:**
- ✅ `dashboard` - admin dashboard
- ✅ `landing` - landing page
- ✅ `marketing` - marketing site
- ✅ `docs` - documentation

### Avoid Generic Names

❌ `service`, `app`, `main`, `core`, `backend`, `frontend`

**Why?** These don't describe what the service actually does.

---

## When to Deviate

### Rare Exceptions

You may deviate from the naming convention when:

1. **Multiple instances of same tech/purpose**
   ```
   api-fastapi-auth-v1
   api-fastapi-auth-v2  # During migration
   ```

2. **Regional or tenant-specific services**
   ```
   api-fastapi-auth-eu
   api-fastapi-auth-us
   ```

3. **Environment-specific overrides** (use labels, not names)
   ```
   # ❌ Don't do this:
   api-fastapi-auth-prod
   api-fastapi-auth-dev
   
   # ✅ Do this instead:
   api-fastapi-auth  # Use docker-compose profiles/labels for env
   ```

---

## Consistency Benefits

### Why This Matters

1. **Instant Recognition** - Know service type and tech at a glance
2. **Auto-Discovery** - Tools can automatically find related files
3. **Team Communication** - Everyone speaks the same language
4. **Documentation** - Clear mapping between services and docs
5. **Automation** - Easier to script and automate tasks
6. **Onboarding** - New developers understand architecture quickly

### Example

When you see `api-fastapi-billing`:
- ✅ It's an API service
- ✅ It uses Python FastAPI
- ✅ It handles billing
- ✅ Instruction file: `service-api-fastapi.instructions.md`
- ✅ DevContainer: `.devcontainer/api-fastapi-billing-container/`
- ✅ Scaffold: `scaffold-api-fastapi-service.md`

---

## See Also

- **[Decision Tree](./DECISION-TREE.md)** - Choose the right technology
- **[Anti-Patterns](./ANTI-PATTERNS.md)** - Common naming mistakes
- **[Tech Stacks Overview](./README.md)** - All available stacks

---

**Last Updated:** 2025-10-09  
**Template Version:** 1.0.0
