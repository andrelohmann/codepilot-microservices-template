# API Services - Backend Technologies

RESTful and GraphQL API services for business logic and data access.

## Overview

API services handle synchronous request/response patterns, providing endpoints for client applications to interact with your system's business logic and data.

**Use APIs for:**
- REST/GraphQL endpoints
- CRUD operations
- Authentication/Authorization
- Business logic processing
- Database operations
- Synchronous data transformation

**Don't use APIs for:**
- Background/async jobs → Use [Workers](../workers/)
- Static content → Use [SSG](../ssg/)
- Real-time event processing → Use [Streams](../streams/)

---

## Available Technologies

| Technology | Language | Best For | Recommendation |
|------------|----------|----------|----------------|
| **[FastAPI](./fastapi.md)** | Python | Data-intensive APIs, ML serving, async I/O | ⭐ Python projects |
| **[NestJS](./nestjs.md)** | Node.js/TypeScript | Enterprise APIs, microservices, complex logic | ⭐ Node.js projects |
| **[Fiber](./fiber.md)** | Go | High-throughput, low-latency, system services | ⭐ Performance-critical |

---

## Technology Comparison

### Performance

| Technology | Requests/sec | Latency | Memory | Concurrency |
|------------|--------------|---------|--------|-------------|
| **Fiber (Go)** | 🚀🚀🚀🚀🚀 Very High | Sub-ms | Low | Native (goroutines) |
| **FastAPI** | 🚀🚀🚀🚀 High | 1-5ms | Medium | Async/await |
| **NestJS** | 🚀🚀🚀 Good | 5-15ms | Medium | Async/await |

### Developer Experience

| Technology | Learning Curve | Type Safety | Auto Docs | Ecosystem |
|------------|---------------|-------------|-----------|-----------|
| **NestJS** | Medium | ⭐⭐⭐⭐⭐ (TypeScript) | ✅ Swagger | Rich (npm) |
| **FastAPI** | Low | ⭐⭐⭐⭐ (Pydantic) | ✅ OpenAPI | Rich (PyPI) |
| **Fiber** | Medium-High | ⭐⭐⭐ (Go types) | ⚠️ Manual | Growing |

### Use Case Fit

| Use Case | FastAPI | NestJS | Fiber |
|----------|---------|--------|-------|
| **Data-heavy APIs** | ⭐⭐⭐⭐⭐ Perfect | ⭐⭐⭐ Good | ⭐⭐⭐ Good |
| **ML Model Serving** | ⭐⭐⭐⭐⭐ Perfect | ⭐⭐ Limited | ⭐⭐ Limited |
| **Enterprise Systems** | ⭐⭐⭐⭐ Great | ⭐⭐⭐⭐⭐ Perfect | ⭐⭐⭐ Good |
| **Microservices** | ⭐⭐⭐⭐ Great | ⭐⭐⭐⭐⭐ Perfect | ⭐⭐⭐⭐ Great |
| **High-Throughput** | ⭐⭐⭐ Good | ⭐⭐ Limited | ⭐⭐⭐⭐⭐ Perfect |
| **Low-Latency** | ⭐⭐⭐ Good | ⭐⭐ Limited | ⭐⭐⭐⭐⭐ Perfect |
| **GraphQL** | ⭐⭐⭐ Good | ⭐⭐⭐⭐⭐ Perfect | ⭐⭐⭐ Good |
| **WebSockets** | ⭐⭐⭐⭐ Great | ⭐⭐⭐⭐ Great | ⭐⭐⭐⭐⭐ Perfect |

---

## When to Use Each

### FastAPI (Python) ⭐

**Choose FastAPI when:**
- ✅ You have a Python-based team/ecosystem
- ✅ Working with data science/ML models
- ✅ Need async I/O operations
- ✅ Want automatic API documentation
- ✅ Leveraging Python libraries (numpy, pandas, sklearn, etc.)
- ✅ Building data transformation APIs
- ✅ Integrating with Python services

**Examples:**
```
api-fastapi-auth          # Authentication service
api-fastapi-billing       # Billing/payment processing
api-fastapi-ml-inference  # ML model serving
api-fastapi-data-pipeline # Data transformation API
```

**Strengths:**
- Python ecosystem access
- Excellent async support
- Type hints + Pydantic validation
- Auto-generated OpenAPI docs
- Great for ML/Data Science

**Trade-offs:**
- Slower than Go
- Higher memory usage than Go
- GIL limitations for CPU-bound tasks

[**→ Full FastAPI Documentation**](./fastapi.md)

---

### NestJS (Node.js/TypeScript) ⭐

**Choose NestJS when:**
- ✅ You have a TypeScript/Node.js team
- ✅ Building enterprise-grade applications
- ✅ Need modular, maintainable architecture
- ✅ Using GraphQL
- ✅ Need rich dependency injection
- ✅ Building microservices with message queues
- ✅ Want Angular-like architecture patterns

**Examples:**
```
api-nestjs-payments       # Payment gateway integration
api-nestjs-notifications  # Notification service
api-nestjs-catalog        # Product catalog
api-nestjs-orders         # Order management
```

**Strengths:**
- Excellent architecture (modules, DI, decorators)
- TypeScript native
- Rich ecosystem (npm)
- Built-in GraphQL support
- Microservices patterns
- Easy to test and maintain

**Trade-offs:**
- More boilerplate than FastAPI
- Slower than Go
- Higher learning curve

[**→ Full NestJS Documentation**](./nestjs.md)

---

### Fiber (Go) ⭐

**Choose Fiber when:**
- ✅ Performance is critical (high throughput, low latency)
- ✅ Building system-level services
- ✅ Need low memory footprint
- ✅ Want native concurrency (goroutines)
- ✅ Building API gateways/proxies
- ✅ Handling thousands of concurrent connections
- ✅ Resource-constrained environments

**Examples:**
```
api-fiber-gateway         # API gateway/proxy
api-fiber-metrics         # Metrics aggregation
api-fiber-proxy           # Reverse proxy
api-fiber-websocket       # WebSocket server
```

**Strengths:**
- Fastest performance
- Lowest memory usage
- Native concurrency
- Great for high-load scenarios
- No GIL (unlike Python)
- Compiled binary (easy deployment)

**Trade-offs:**
- Smaller ecosystem than Python/Node.js
- Higher learning curve
- Manual API documentation
- Fewer libraries for specialized tasks

[**→ Full Fiber Documentation**](./fiber.md)

---

## Naming Convention

All API services follow the pattern:

```
api-{tech}-{purpose}
```

### Examples

**FastAPI:**
- `api-fastapi-auth` - Authentication service
- `api-fastapi-billing` - Billing service
- `api-fastapi-users` - User management
- `api-fastapi-ml-model` - ML model serving

**NestJS:**
- `api-nestjs-payments` - Payment processing
- `api-nestjs-notifications` - Notification service
- `api-nestjs-catalog` - Product catalog
- `api-nestjs-orders` - Order management

**Fiber:**
- `api-fiber-gateway` - API gateway
- `api-fiber-metrics` - Metrics collection
- `api-fiber-proxy` - Reverse proxy
- `api-fiber-auth` - Authentication

---

## Common Patterns

### Pattern: REST API with Database

```
Technology: FastAPI, NestJS, or Fiber
Structure:
  - RESTful endpoints (GET, POST, PUT, DELETE)
  - Database connection (PostgreSQL, MySQL, MongoDB)
  - Data validation (Pydantic, class-validator, struct tags)
  - Error handling
  - Authentication/Authorization
  - Logging
  - Health checks
```

### Pattern: GraphQL API

```
Best Choice: NestJS (built-in GraphQL support)
Alternative: FastAPI (with Strawberry/Ariadne)
Structure:
  - GraphQL schema
  - Resolvers
  - DataLoader (N+1 query prevention)
  - Authentication
  - Subscriptions (optional)
```

### Pattern: API Gateway

```
Best Choice: Fiber (performance)
Alternative: NestJS (features)
Structure:
  - Route forwarding
  - Load balancing
  - Authentication middleware
  - Rate limiting
  - Request/Response transformation
  - Metrics collection
```

### Pattern: ML Model Serving

```
Best Choice: FastAPI (Python ML ecosystem)
Structure:
  - Model loading/caching
  - Prediction endpoint
  - Batch prediction support
  - Input validation (Pydantic)
  - Async processing
  - Model versioning
```

---

## Directory Structure

### Standard API Structure

```
services/api-{tech}-{purpose}/
├── src/
│   ├── main.{ext}              # Application entry point
│   ├── routes/                 # Route definitions
│   ├── controllers/            # Request handlers
│   ├── services/               # Business logic
│   ├── models/                 # Data models
│   ├── middleware/             # Custom middleware
│   ├── db/                     # Database connection
│   └── utils/                  # Utilities
├── tests/
│   ├── unit/                   # Unit tests
│   ├── integration/            # Integration tests
│   └── e2e/                    # End-to-end tests
├── Dockerfile                  # Multi-stage production build
├── docker-compose.yml          # Local development
├── .env.example                # Environment variables template
└── README.md                   # Service documentation
```

---

## Getting Started

### 1. Choose Your Technology

Use the [Decision Tree](../DECISION-TREE.md) or comparison table above.

### 2. Read Detailed Documentation

- [FastAPI Documentation](./fastapi.md)
- [NestJS Documentation](./nestjs.md)
- [Fiber Documentation](./fiber.md)

### 3. Scaffold Your Service

Use GitHub Copilot with the appropriate scaffold prompt:
- `templates/.github/prompts/scaffold-api-fastapi-service.md`
- `templates/.github/prompts/scaffold-api-nestjs-service.md`
- `templates/.github/prompts/scaffold-api-fiber-service.md`

---

## See Also

- **[Workers](../workers/)** - Background job processing
- **[Naming Conventions](../NAMING-CONVENTIONS.md)** - Service naming rules
- **[Decision Tree](../DECISION-TREE.md)** - Technology selection guide
- **[Anti-Patterns](../ANTI-PATTERNS.md)** - Common mistakes

---

**Last Updated:** 2025-10-09  
**Template Version:** 1.0.0
