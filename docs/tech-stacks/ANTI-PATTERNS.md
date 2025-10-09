# Common Anti-Patterns

This document outlines common mistakes when choosing and naming microservices, along with correct alternatives.

## Naming Anti-Patterns

### ❌ Vague or Generic Naming

**Problem:** Service names that don't clearly indicate type, technology, or purpose.

| ❌ Wrong | ✅ Correct | Why |
|---------|-----------|-----|
| `user-service` | `api-nestjs-users` | Missing type and tech |
| `backend-1` | `api-fastapi-auth` | No meaning, no tech |
| `frontend` | `frontend-react-dashboard` | No tech or purpose |
| `my-api` | `api-fastapi-billing` | Not descriptive |
| `service-auth` | `api-nestjs-auth` | Type prefix missing |
| `app` | `frontend-react-admin` | Too generic |
| `main` | `gateway-traefik-main` | Missing type and tech |

**Why it's bad:** You can't tell what the service does or what technology it uses just from the name.

---

### ❌ Generic Technology Identifiers

**Problem:** Using language names instead of specific frameworks.

| ❌ Wrong | ✅ Correct | Why |
|---------|-----------|-----|
| `api-python-auth` | `api-fastapi-auth` | Use specific framework (FastAPI) |
| `worker-node-jobs` | `worker-bullmq-jobs` | Use specific tool (BullMQ) |
| `frontend-js-app` | `frontend-react-app` | Use specific library (React) |
| `api-go-service` | `api-fiber-service` | Use specific framework (Fiber) |
| `worker-python-tasks` | `worker-celery-tasks` | Use specific framework (Celery) |

**Why it's bad:** Doesn't communicate which specific framework or library is used, making it harder to find related documentation.

---

### ❌ Missing Purpose

**Problem:** No indication of what the service does.

| ❌ Wrong | ✅ Correct | Why |
|---------|-----------|-----|
| `api-fastapi` | `api-fastapi-billing` | Add business domain |
| `worker-celery` | `worker-celery-reports` | Add task type |
| `frontend-react` | `frontend-react-dashboard` | Add purpose |
| `ssg-astro` | `ssg-astro-marketing` | Add site purpose |
| `gateway-traefik` | `gateway-traefik-main` | Add gateway role |

**Why it's bad:** You can't understand what the service does without reading documentation.

---

### ❌ Wrong Service Type

**Problem:** Using the wrong service type for the use case.

| ❌ Wrong | ✅ Correct | Why |
|---------|-----------|-----|
| `frontend-react-docs` | `ssg-react-docs` | Docs are static content → use SSG |
| `api-fastapi-email` | `worker-celery-email` | Email sending is async → use worker |
| `worker-bullmq-users` | `api-nestjs-users` | CRUD operations are sync → use API |
| `ssg-react-dashboard` | `frontend-react-dashboard` | Dashboards are interactive → use frontend |
| `frontend-nextjs-blog` | `ssg-astro-blog` or `ssg-react-blog` | Static blogs → use SSG |
| `api-nestjs-scraper` | `worker-golang-scraper` | Scraping is background work → use worker |

**Why it's bad:** Wrong architecture leads to performance issues, scalability problems, and maintainability headaches.

---

### ❌ Inconsistent Structure

**Problem:** Not following the template conventions.

| ❌ Wrong | ✅ Correct | Why |
|---------|-----------|-----|
| `services/auth/` | `services/api-fastapi-auth/` | Follow naming convention |
| `.devcontainer/auth/` | `.devcontainer/api-fastapi-auth-container/` | Match service name + `-container` |
| `service-auth.md` | `service-api-fastapi.instructions.md` | Follow instruction file pattern |
| `AuthService` | `api-nestjs-auth` | Use kebab-case, not PascalCase |
| `api_fastapi_auth` | `api-fastapi-auth` | Use hyphens, not underscores |

**Why it's bad:** Breaks automation, makes it hard to find related files, confuses team members.

---

### ❌ Mixing Concerns

**Problem:** One service trying to do too much.

| ❌ Wrong | ✅ Correct | Why |
|---------|-----------|-----|
| `api-nestjs-everything` | `api-nestjs-auth` + `api-nestjs-billing` | Separate concerns |
| `frontend-react-app` (with backend API routes) | `frontend-react-app` + `api-nestjs-backend` | Separate frontend from backend |
| `worker-celery-all` | `worker-celery-email` + `worker-celery-reports` | One worker type per service |
| `api-fastapi-monolith` | Split into multiple focused APIs | Follow microservices principles |

**Why it's bad:** Violates single responsibility principle, makes services hard to scale and maintain.

---

## Architecture Anti-Patterns

### ❌ Using Frontend for Static Content

**Wrong:**
```yaml
Service: frontend-react-docs
Purpose: Documentation website
Content: Static markdown files
```

**Why it's wrong:**
- React SPA adds unnecessary JavaScript
- Slower page loads
- Poor SEO
- Higher hosting costs

**Correct:**
```yaml
Service: ssg-react-docs or ssg-astro-docs
Purpose: Documentation website
Content: Static markdown files
Benefits:
  - Pre-rendered HTML
  - Zero or minimal JavaScript
  - Perfect SEO
  - Fastest page loads
  - Cheaper hosting
```

---

### ❌ Using API for Background Tasks

**Wrong:**
```yaml
Service: api-fastapi-email
Endpoint: POST /send-email
Behavior: Blocks while sending email (slow)
```

**Why it's wrong:**
- Blocks HTTP request while sending email
- Timeouts on slow operations
- No retry mechanism
- No task monitoring

**Correct:**
```yaml
Service: worker-celery-email or worker-bullmq-email
Behavior: 
  - API enqueues job → returns immediately
  - Worker processes job in background
  - Automatic retries on failure
  - Task monitoring dashboard
```

---

### ❌ Using Worker for CRUD Operations

**Wrong:**
```yaml
Service: worker-bullmq-users
Purpose: User management (Create, Read, Update, Delete)
```

**Why it's wrong:**
- CRUD operations are synchronous
- Users expect immediate responses
- No need for background processing
- Adds unnecessary complexity

**Correct:**
```yaml
Service: api-nestjs-users or api-fastapi-users
Purpose: User management RESTful API
Endpoints: GET/POST/PUT/DELETE /users
```

---

### ❌ Wrong SSG Choice

**Wrong:**
```yaml
Service: frontend-nextjs-company-site
Purpose: Company marketing website
Content: Fully static (no dynamic data)
```

**Why it's wrong:**
- Next.js overhead for no benefit
- Slower builds
- More complex deployment
- Node.js runtime required

**Correct:**
```yaml
Service: ssg-astro-marketing
Purpose: Company marketing website
Benefits:
  - Pure static HTML/CSS
  - Zero JavaScript by default
  - Fastest builds
  - Deploy anywhere (no runtime needed)
  - Perfect Lighthouse scores
```

---

## Technology Selection Anti-Patterns

### ❌ Choosing Tech by Popularity

**Wrong Decision Process:**
```
"Next.js is popular, let's use it for everything!"
```

**Problems:**
- Company website → Next.js (overkill, use Astro)
- Documentation → Next.js (overkill, use SSG)
- Admin dashboard → Next.js (could use React SPA)

**Correct Decision Process:**
```
1. What's the use case?
2. What are the requirements?
3. Which tech fits best?

Company website → Astro (zero JS, perfect performance)
Documentation → React SSG or Astro (static content)
Admin dashboard → React SPA (interactive, no SEO needed)
```

---

### ❌ One-Size-Fits-All Approach

**Wrong:**
```yaml
"We're a Python shop, so everything is Python"

API: api-fastapi-* ✅ Good choice
Worker: worker-celery-* ✅ Good choice
Stream Processing: worker-celery-streams ❌ Wrong!
```

**Why it's wrong:**
- Python isn't ideal for high-throughput streaming
- Go excels at concurrent stream processing
- Using wrong tool for the job

**Correct:**
```yaml
API: api-fastapi-* (Python, good for data APIs)
Worker: worker-celery-* (Python, good for ML/ETL)
Stream Processing: stream-golang-analytics (Go, perfect for streams)
```

**Key Principle:** Choose the right tool for each job, not the same tool for everything.

---

### ❌ Over-Engineering

**Wrong:**
```yaml
Service: api-nestjs-hello-world
Lines of Code: 5,000+
Dependencies: 50+
Purpose: Returns "Hello World"
```

**Why it's wrong:**
- Unnecessary complexity
- Harder to maintain
- Slower to develop
- More attack surface

**Correct:**
```yaml
Service: api-fastapi-hello (or api-fiber-hello)
Lines of Code: ~100
Dependencies: Minimal
Purpose: Returns "Hello World"
Keep it simple!
```

---

### ❌ Under-Engineering

**Wrong:**
```yaml
Service: ssg-astro-webapp
Purpose: Interactive dashboard with real-time updates
Technology: Static site generator
```

**Why it's wrong:**
- SSG can't handle real-time updates
- No WebSocket support
- No state management
- Wrong tool for interactive apps

**Correct:**
```yaml
Service: frontend-react-dashboard
Purpose: Interactive dashboard with real-time updates
Technology: React SPA with TanStack Query
```

---

## Deployment Anti-Patterns

### ❌ Environment in Service Name

**Wrong:**
```yaml
services/
├── api-fastapi-auth-prod/
├── api-fastapi-auth-dev/
├── api-fastapi-auth-staging/
```

**Why it's wrong:**
- Triplicates codebase
- Harder to maintain
- Violates DRY principle

**Correct:**
```yaml
services/
└── api-fastapi-auth/  # Single codebase

# Use docker-compose profiles or environment variables:
docker-compose --profile production up
docker-compose --profile development up
```

---

### ❌ Hardcoded Configuration

**Wrong:**
```python
# api-fastapi-auth/main.py
DATABASE_URL = "postgresql://localhost/prod"
SECRET_KEY = "my-secret-123"
```

**Why it's wrong:**
- Exposes secrets
- Can't change without code deploy
- Not portable

**Correct:**
```python
# api-fastapi-auth/main.py
from os import getenv

DATABASE_URL = getenv("DATABASE_URL")
SECRET_KEY = getenv("SECRET_KEY")

# .env file (git-ignored)
DATABASE_URL=postgresql://localhost/prod
SECRET_KEY=my-secret-123
```

---

## Testing Anti-Patterns

### ❌ No Tests

**Wrong:**
```
services/api-fastapi-auth/
├── src/
└── Dockerfile
# No tests/ directory!
```

**Why it's wrong:**
- Can't verify functionality
- Afraid to refactor
- Bugs in production

**Correct:**
```
services/api-fastapi-auth/
├── src/
├── tests/
│   ├── test_auth.py
│   ├── test_users.py
│   └── test_integration.py
├── Dockerfile
└── pytest.ini
```

---

### ❌ Only Unit Tests

**Wrong:**
```yaml
tests/
└── unit/
    └── test_functions.py  # Only unit tests
```

**Why it's wrong:**
- Doesn't test integration
- Doesn't test API contracts
- Doesn't test E2E flows

**Correct:**
```yaml
tests/
├── unit/           # Function-level tests
├── integration/    # Service integration tests
└── e2e/           # End-to-end tests
```

---

## Documentation Anti-Patterns

### ❌ No README

**Wrong:**
```
services/api-fastapi-auth/
├── src/
└── Dockerfile
# No README.md!
```

**Why it's wrong:**
- New developers can't onboard
- No setup instructions
- Architecture not documented

**Correct:**
```
services/api-fastapi-auth/
├── src/
├── README.md  # ← Contains setup, architecture, API docs
├── Dockerfile
└── .env.example
```

---

### ❌ Outdated Documentation

**Wrong:**
```markdown
# README.md (Last updated: 2020)
## Setup
Run `npm install` (but we switched to pnpm in 2023)
```

**Why it's wrong:**
- Confuses developers
- Wastes time
- Erodes trust

**Correct:**
```markdown
# README.md (Last updated: 2025-10-09)
## Setup
Run `pnpm install` (we use pnpm for package management)
```

**Best Practice:** Update docs when you change code, not as an afterthought.

---

## See Also

- **[Naming Conventions](./NAMING-CONVENTIONS.md)** - Correct naming patterns
- **[Decision Tree](./DECISION-TREE.md)** - Choose the right technology
- **[Tech Stacks Overview](./README.md)** - All available stacks

---

**Last Updated:** 2025-10-09  
**Template Version:** 1.0.0
