# System Architecture Overview

**Last Updated:** 2025-10-06  
**Status:** Template (to be customized per project)

---

## Purpose

This document provides a comprehensive overview of the microservices architecture, including all services, backing services, development services, network topology, and data flows.

---

## 🏗️ High-Level Architecture

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                           Development Environment                                      │
│                                                                                        │
│  ┌──────────────────────────────────────────────────────────────────────────────────┐ │
│  │                    Docker Compose Network: app-network                           │ │
│  │                                                                                  │ │
│  │  ╔═══════════════════════════════════════════════════════════════════════════╗  │ │
│  │  ║                          Application Services                             ║  │ │
│  │  ║  (Defined in docker-compose.yml between ##<<SERVICES>> markers)          ║  │ │
│  │  ╚═══════════════════════════════════════════════════════════════════════════╝  │ │
│  │                                                                                  │ │
│  │  ┌─────────────────┐        ┌─────────────────┐        ┌─────────────────┐    │ │
│  │  │    Frontend     │───────→│      API        │───────→│     Worker      │    │ │
│  │  │  (React/Vite)   │  HTTP  │   (FastAPI)     │  AMQP  │   (Node.js)     │    │ │
│  │  │  Port: 3000     │        │  Port: 8000     │        │  (Background)   │    │ │
│  │  └─────────────────┘        └────────┬────────┘        └─────────────────┘    │ │
│  │                                       │                                         │ │
│  │  ═════════════════════════════════════════════════════════════════════════════  │ │
│  │                                                                                  │ │
│  │  ╔═══════════════════════════════════════════════════════════════════════════╗  │ │
│  │  ║                         Backing Services                                  ║  │ │
│  │  ║  (Defined in docker-compose.yml between ##<<BACKING_SERVICES>> markers)  ║  │ │
│  │  ╚═══════════════════════════════════════════════════════════════════════════╝  │ │
│  │                                       │                                         │ │
│  │                    ┌──────────────────┼──────────────────┐                     │ │
│  │                    │                  │                  │                     │ │
│  │                    ↓                  ↓                  ↓                     │ │
│  │           ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐        │ │
│  │           │   PostgreSQL    │ │     Redis       │ │   RabbitMQ      │        │ │
│  │           │   (Database)    │ │    (Cache)      │ │    (Queue)      │        │ │
│  │           │   Port: 5432    │ │   Port: 6379    │ │  Port: 5672     │        │ │
│  │           └─────────────────┘ └─────────────────┘ └─────────────────┘        │ │
│  │                    │                  │                  │                     │ │
│  │                    ↓                  ↓                  ↓                     │ │
│  │           ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐        │ │
│  │           │  postgres-data  │ │   redis-data    │ │  rabbitmq-data  │        │ │
│  │           │ (Named Volume)  │ │ (Named Volume)  │ │ (Named Volume)  │        │ │
│  │           └─────────────────┘ └─────────────────┘ └─────────────────┘        │ │
│  │                                                                                  │ │
│  │  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─   │ │
│  │                                                                                  │ │
│  │  ╔═══════════════════════════════════════════════════════════════════════════╗  │ │
│  │  ║                      Development Services                                 ║  │ │
│  │  ║  (Defined in docker-compose.development.yml                              ║  │ │
│  │  ║   between ##<<DEVELOPMENT_SERVICES>> markers)                            ║  │ │
│  │  ╚═══════════════════════════════════════════════════════════════════════════╝  │ │
│  │                                                                                  │ │
│  │  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐              │ │
│  │  │     Ollama      │  │    pgAdmin      │  │  Redis Commander│              │ │
│  │  │  (LLM Models)   │  │  (DB GUI)       │  │   (Redis GUI)   │              │ │
│  │  │  Port: 11434    │  │  Port: 5050     │  │   Port: 8081    │              │ │
│  │  └─────────────────┘  └─────────────────┘  └─────────────────┘              │ │
│  │         │                                                                        │ │
│  │         ↓                                                                        │ │
│  │  ┌─────────────────┐                                                           │ │
│  │  │  ./tmp/ollama   │  ← Bind mount (models persisted locally)                 │ │
│  │  │  (Models dir)   │                                                           │ │
│  │  └─────────────────┘                                                           │ │
│  │                                                                                  │ │
│  └──────────────────────────────────────────────────────────────────────────────────┘ │
│                                                                                        │
│  ┌──────────────────────────────────────────────────────────────────────────────────┐ │
│  │                            Configuration & Secrets                               │ │
│  │                                                                                  │ │
│  │  ┌─────────────────────┐        ┌─────────────────────────────────┐           │ │
│  │  │  config/.env        │───────→│  All Services (via env_file)    │           │ │
│  │  │  (Gitignored)       │        │  - Database credentials         │           │ │
│  │  └─────────────────────┘        │  - API keys                     │           │ │
│  │                                  │  - Service URLs                 │           │ │
│  │  ┌─────────────────────┐        └─────────────────────────────────┘           │ │
│  │  │config/.env.mcp      │                                                       │ │
│  │  │   .credentials      │───────→ VS Code / MCP Servers                        │ │
│  │  │  (Gitignored)       │         (Brave Search, etc.)                         │ │
│  │  └─────────────────────┘                                                       │ │
│  │                                                                                  │ │
│  └──────────────────────────────────────────────────────────────────────────────────┘ │
│                                                                                        │
└────────────────────────────────────────────────────────────────────────────────────────┘

External Access (Development):
┌─────────────┐
│  Developer  │
│   Machine   │
└──────┬──────┘
       │
       ├──→ http://localhost:3000  (Frontend)
       ├──→ http://localhost:8000  (API)
       ├──→ http://localhost:5050  (pgAdmin)
       ├──→ http://localhost:8081  (Redis Commander)
       └──→ http://localhost:11434 (Ollama API)
```

---

## 🔗 Network Topology

### Docker Network: `app-network`

All services communicate via a single Docker bridge network created by Docker Compose.

```
┌──────────────────────────────────────────────────────────────────┐
│                       app-network (bridge)                       │
│                      Subnet: 172.20.0.0/16                       │
│                                                                   │
│  Service Discovery: DNS-based (service names resolve to IPs)    │
│                                                                   │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐                │
│  │  frontend  │  │    api     │  │   worker   │                │
│  │ 172.20.0.2 │  │ 172.20.0.3 │  │ 172.20.0.4 │                │
│  └────────────┘  └────────────┘  └────────────┘                │
│                                                                   │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐                │
│  │ postgres   │  │   redis    │  │  rabbitmq  │                │
│  │ 172.20.0.5 │  │ 172.20.0.6 │  │ 172.20.0.7 │                │
│  └────────────┘  └────────────┘  └────────────┘                │
│                                                                   │
└──────────────────────────────────────────────────────────────────┘
         ↑
         │ (Bridge to host)
         ↓
┌──────────────────┐
│   Host Machine   │
│  Port Mappings   │
└──────────────────┘
```

### Port Mappings

| Service           | Internal Port | External Port | Protocol | Exposed in Dev? |
|-------------------|---------------|---------------|----------|-----------------|
| Frontend          | 3000          | 3000          | HTTP     | Yes             |
| API               | 8000          | 8000          | HTTP     | Yes             |
| Worker            | N/A           | N/A           | N/A      | No              |
| PostgreSQL        | 5432          | 5432          | TCP      | Yes (dev only)  |
| Redis             | 6379          | 6379          | TCP      | Yes (dev only)  |
| RabbitMQ          | 5672          | 5672          | AMQP     | Yes (dev only)  |
| RabbitMQ Mgmt     | 15672         | 15672         | HTTP     | Yes (dev only)  |
| pgAdmin           | 80            | 5050          | HTTP     | Yes (dev only)  |
| Redis Commander   | 8081          | 8081          | HTTP     | Yes (dev only)  |
| Ollama            | 11434         | 11434         | HTTP     | Yes (dev only)  |

**Production:** Only Frontend and API ports are exposed. All management UIs and direct database access are disabled.

---

## 📊 Data Flow Diagrams

### User Request Flow (Frontend → API → Database)

```
┌──────────┐
│  Browser │
│  (User)  │
└────┬─────┘
     │
     │ 1. HTTP GET /page
     ↓
┌──────────────────┐
│    Frontend      │
│   (React SPA)    │
└────┬─────────────┘
     │
     │ 2. Fetch data
     │    HTTP GET /api/v1/users
     ↓
┌──────────────────┐
│       API        │
│    (FastAPI)     │
└────┬─────────────┘
     │
     │ 3. Check cache
     ↓
┌──────────────────┐
│      Redis       │  ─── Cache hit? Return cached data
│     (Cache)      │
└──────────────────┘
     │ Cache miss
     ↓
┌──────────────────┐
│       API        │
│  (Query DB)      │
└────┬─────────────┘
     │
     │ 4. SQL Query
     │    SELECT * FROM users
     ↓
┌──────────────────┐
│   PostgreSQL     │
│   (Database)     │
└────┬─────────────┘
     │
     │ 5. Return results
     ↓
┌──────────────────┐
│       API        │
│ (Process data)   │
└────┬─────────────┘
     │
     │ 6. Update cache
     ↓
┌──────────────────┐
│      Redis       │
│ (Store with TTL) │
└──────────────────┘
     │
     │ 7. JSON Response
     ↓
┌──────────────────┐
│    Frontend      │
│ (Render UI)      │
└────┬─────────────┘
     │
     │ 8. Display to user
     ↓
┌──────────┐
│  Browser │
└──────────┘
```

### Background Job Processing (API → Queue → Worker)

```
┌──────────────────┐
│       API        │
│  (User Action)   │
└────┬─────────────┘
     │
     │ 1. POST /api/v1/export
     │    Trigger long-running task
     ↓
┌──────────────────┐
│       API        │
│ (Create Job)     │
└────┬─────────────┘
     │
     │ 2. Publish message
     │    {type: "export", userId: 123}
     ↓
┌──────────────────┐
│    RabbitMQ      │
│   (Queue)        │
└────┬─────────────┘
     │
     │ 3. Job stored in queue
     │    Persisted to disk
     │
     │ 4. Consumer listening...
     ↓
┌──────────────────┐
│     Worker       │
│  (Node.js)       │
└────┬─────────────┘
     │
     │ 5. Process job
     │    - Query database
     │    - Generate export file
     │    - Upload to S3
     ↓
┌──────────────────┐
│   PostgreSQL     │
│ (Update status)  │
└──────────────────┘
     │
     │ 6. Job complete
     │    Acknowledge message
     ↓
┌──────────────────┐
│    RabbitMQ      │
│ (Remove from Q)  │
└──────────────────┘
     │
     │ 7. Optional: Send notification
     ↓
┌──────────────────┐
│       API        │
│ (WebSocket/SSE)  │
└────┬─────────────┘
     │
     │ 8. Notify user
     ↓
┌──────────────────┐
│    Frontend      │
│ (Show complete)  │
└──────────────────┘
```

### Authentication Flow (Login → JWT Token)

```
┌──────────┐
│  Browser │
└────┬─────┘
     │
     │ 1. POST /api/v1/auth/login
     │    {email, password}
     ↓
┌──────────────────┐
│       API        │
│ (Auth endpoint)  │
└────┬─────────────┘
     │
     │ 2. Query user
     │    SELECT * FROM users WHERE email=?
     ↓
┌──────────────────┐
│   PostgreSQL     │
└────┬─────────────┘
     │
     │ 3. Return user record
     │    {id, email, hashed_password}
     ↓
┌──────────────────┐
│       API        │
│ (Verify password)│
└────┬─────────────┘
     │
     │ 4. bcrypt.verify(password, hashed_password)
     │    ✓ Match
     ↓
┌──────────────────┐
│       API        │
│ (Generate JWT)   │
└────┬─────────────┘
     │
     │ 5. Create tokens
     │    access_token (30min)
     │    refresh_token (7days)
     ↓
┌──────────────────┐
│      Redis       │
│ (Store refresh)  │
└──────────────────┘
     │
     │ 6. Return tokens
     │    {access_token, refresh_token}
     ↓
┌──────────┐
│  Browser │
│ (Store)  │
└────┬─────┘
     │
     │ 7. Subsequent requests
     │    Authorization: Bearer <access_token>
     ↓
┌──────────────────┐
│       API        │
│ (Verify JWT)     │
└──────────────────┘
```

---

## 🗄️ Data Persistence

### Storage Strategy

```
┌────────────────────────────────────────────────────────────────┐
│                     Data Persistence                           │
│                                                                 │
│  Named Volumes (Production-ready):                            │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │  postgres-data  → PostgreSQL database files              │ │
│  │  redis-data     → Redis persistence (AOF/RDB)            │ │
│  │  rabbitmq-data  → RabbitMQ queue persistence             │ │
│  └──────────────────────────────────────────────────────────┘ │
│                                                                 │
│  Bind Mounts (Development-only):                              │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │  ./tmp/ollama   → Ollama models (local storage)          │ │
│  │  ./config/.env  → Environment variables                  │ │
│  │  ./services/*/  → Service source code (hot reload)       │ │
│  └──────────────────────────────────────────────────────────┘ │
│                                                                 │
└────────────────────────────────────────────────────────────────┘

Backup Strategy:
┌──────────────────┐      ┌──────────────────┐      ┌──────────────────┐
│ PostgreSQL       │─────→│  pg_dump         │─────→│  Backup Storage  │
│ (Daily)          │      │  (Automated)     │      │  (S3/NFS)        │
└──────────────────┘      └──────────────────┘      └──────────────────┘

┌──────────────────┐      ┌──────────────────┐      ┌──────────────────┐
│ Redis            │─────→│  RDB Snapshot    │─────→│  Backup Storage  │
│ (Hourly)         │      │  (Automated)     │      │  (S3/NFS)        │
└──────────────────┘      └──────────────────┘      └──────────────────┘
```

---

## 🔐 Security Boundaries

```
════════════════════════════════════════════════════════════════
  Public Internet
════════════════════════════════════════════════════════════════
                        │
                        ↓
          ┌─────────────────────────┐
          │     Load Balancer       │  ← SSL/TLS Termination
          │      (Production)       │
          └────────────┬────────────┘
                       │
  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
    DMZ / Frontend Tier│
  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
                       │
          ┌────────────┴────────────┐
          │      Frontend           │  ← Static assets only
          │    (React SPA)          │
          └────────────┬────────────┘
                       │
  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
    Application Tier   │
  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
                       │
          ┌────────────┴────────────┐
          │        API              │  ← Authentication required
          │     (FastAPI)           │     Rate limiting
          └────────────┬────────────┘     Input validation
                       │
  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
    Data Tier          │
  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
                       │
       ┌───────────────┼───────────────┐
       │               │               │
       ↓               ↓               ↓
  ┌─────────┐   ┌─────────┐   ┌─────────┐
  │PostgreSQL│   │  Redis  │   │RabbitMQ│
  │ (No ext │   │ (No ext │   │ (No ext│  ← No external access
  │  access)│   │  access)│   │  access)│     Encrypted at rest
  └─────────┘   └─────────┘   └─────────┘
```

### Security Measures

- **Authentication:** JWT-based with refresh tokens
- **Authorization:** Role-based access control (RBAC)
- **Encryption:** TLS for all external communication
- **Secrets:** Stored in `.env` files (development) or secrets manager (production)
- **Network Isolation:** Data tier not accessible from internet
- **Rate Limiting:** Configured on API gateway
- **Input Validation:** Pydantic models validate all inputs
- **SQL Injection Protection:** ORM (SQLAlchemy) with parameterized queries

---

## 📈 Scalability Considerations

### Horizontal Scaling

```
Current (Single Instance):
┌──────┐   ┌──────┐   ┌──────────┐
│ API  │───│Redis │───│PostgreSQL│
└──────┘   └──────┘   └──────────┘

Scaled (Multiple Instances):
┌────────────────────────────────────────────┐
│           Load Balancer                    │
└────────┬───────────┬───────────┬───────────┘
         │           │           │
    ┌────┴───┐  ┌────┴───┐  ┌────┴───┐
    │ API-1  │  │ API-2  │  │ API-3  │
    └────┬───┘  └────┬───┘  └────┬───┘
         │           │           │
         └───────────┴───────────┘
                     │
         ┌───────────┼───────────┐
         │           │           │
    ┌────┴───┐  ┌────┴───┐  ┌────┴────┐
    │ Redis  │  │RabbitMQ│  │PostgreSQL│
    │(Cluster)│  │(Cluster)│ │ (Primary │
    └────────┘  └────────┘  │ +Replicas)│
                            └──────────┘
```

### Resource Allocation (Development)

| Service    | CPU | Memory | Scaling Strategy          |
|------------|-----|--------|---------------------------|
| Frontend   | 0.5 | 512MB  | Horizontal (stateless)    |
| API        | 1.0 | 1GB    | Horizontal (stateless)    |
| Worker     | 1.0 | 1GB    | Horizontal (queue-based)  |
| PostgreSQL | 2.0 | 2GB    | Vertical + Read Replicas  |
| Redis      | 0.5 | 512MB  | Horizontal (cluster mode) |
| RabbitMQ   | 1.0 | 1GB    | Horizontal (cluster mode) |

---

## 🔄 CI/CD Pipeline (Overview)

```
Developer → Git Push → GitHub Actions → Build → Test → Deploy
     │
     ↓
┌──────────────┐
│ Git Push     │
│ (main branch)│
└──────┬───────┘
       │
       ↓
┌──────────────────────────────────────────────────────────┐
│            GitHub Actions Workflow                       │
│                                                           │
│  1. Lint & Format Check                                 │
│     → ruff, prettier, yamllint                          │
│                                                           │
│  2. Unit Tests                                          │
│     → pytest, jest (per service)                        │
│                                                           │
│  3. Integration Tests                                   │
│     → testcontainers (PostgreSQL, Redis)                │
│                                                           │
│  4. Build Docker Images                                 │
│     → Multi-stage builds (dev + prod)                   │
│                                                           │
│  5. Push to Registry                                    │
│     → Docker Hub / GitHub Container Registry            │
│                                                           │
│  6. Deploy to Staging                                   │
│     → docker-compose (staging environment)              │
│                                                           │
│  7. E2E Tests (Staging)                                 │
│     → Playwright / Cypress                              │
│                                                           │
│  8. Deploy to Production (Manual Approval)              │
│     → Kubernetes / Docker Swarm                         │
│                                                           │
└──────────────────────────────────────────────────────────┘
```

---

## 📚 Related Documentation

- **Service Details:**
  - [API Service Architecture](../services/api-service.md)
  - [Frontend Service Architecture](../services/frontend-service.md)
  - [Worker Service Architecture](../services/worker-service.md)

- **Operations:**
  - [Deployment Procedures](../operations/deployment.md)
  - [Monitoring & Alerting](../operations/monitoring.md)
  - [Backup & Restore](../operations/backup-restore.md)
  - [Troubleshooting Guide](../operations/troubleshooting.md)

- **Getting Started:**
  - [Workspace Setup](../getting-started/workspace-setup.md)
  - [Adding a New Service](../getting-started/adding-a-service.md)
  - [Development Workflow](../getting-started/development-workflow.md)

- **Architecture Decisions:**
  - [ADR Directory](../adr/)

---

## 🔄 Maintenance

This document should be updated whenever:
- New services are added
- Network topology changes
- Data flows are modified
- Security boundaries shift
- Deployment procedures change

**Review Schedule:** Quarterly or with major architectural changes

---

## ✅ Validation Checklist

Use this checklist to verify architecture consistency:

- [ ] All services are documented in the diagram
- [ ] Network connections match actual docker-compose configuration
- [ ] Data flows reflect real request paths
- [ ] Security boundaries are clearly marked
- [ ] Port mappings are accurate
- [ ] Storage strategy matches actual volumes/mounts
- [ ] Scaling strategy is feasible with current design
- [ ] Related documentation links are valid

---

**Document Version:** 1.0  
**Last Validated:** 2025-10-06  
**Next Review:** 2026-01-06
