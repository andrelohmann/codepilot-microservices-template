# TODO — Template Enhancements

This document tracks planned improvements and prompt files to add to the template.

## 📋 Documentation Standards

**All documentation must follow these conventions:**

1. **Location:** All documentation files go in the `docs/` folder at workspace root
2. **Architecture Diagrams:** Every doc must include ASCII art diagrams showing:
   - Component relationships
   - Data flow
   - Network boundaries
   - Service dependencies
3. **Format:** Markdown with clear headers, code blocks, and examples
4. **Diagrams:** Use box-drawing characters (─│┌┐└┘├┤┬┴┼) for readability
5. **Update Frequency:** Update diagrams when architecture changes

**Example ASCII Art Conventions:**
```
┌─────────────┐
│   Service   │  ← Component/Service
└─────────────┘

┌─────────────┐
│  Database   │  ← Data Store
└─────────────┘

      ↓
     HTTP         ← Protocol/Connection
      ↓

═══════════════  ← Network Boundary

- - - - - - - -  ← Optional/Conditional
```

---

## 🔧 Development Services

### Ollama LLM Service
**Priority:** High  
**Status:** Not Started

Create `.github/prompts/scaffold-ollama.md` to automate:

- [ ] Add Ollama service definition to `docker-compose.development.yml` between `##<<DEVELOPMENT_SERVICES>>` markers
- [ ] Configure persistent model storage in `./tmp/ollama-models/` (bind mount, gitignored)
- [ ] Add environment variables to `config/.env.example`:
  - `OLLAMA_MODELS` — Comma-separated list of models to download on first start (e.g., `llama3.2,codellama,mistral`)
  - `OLLAMA_HOST` — Service hostname for inter-container communication
  - `OLLAMA_PORT` — Exposed port (default: 11434)
- [ ] Include health check to verify Ollama API readiness
- [ ] Add entrypoint script to pull models listed in `OLLAMA_MODELS` on container startup
- [ ] Document model management commands (pull, list, remove) in prompt file
- [ ] Ensure volume permissions allow non-root user access
- [ ] Add restart policy: `unless-stopped`

**Best Practices:**
- Store models outside container for persistence across rebuilds
- Use `.env` for model list to avoid hardcoding in compose files
- Implement health checks for service orchestration dependencies
- Document resource requirements (CPU/RAM per model)

---

## 🗄️ Backing Services

All backing service prompts should be created in `.github/prompts/` and follow these conventions:
- Define services in `docker-compose.yml` between `##<<BACKING_SERVICES>>` markers
- Use official Docker images with pinned versions (avoid `latest`)
- Store data in named volumes (not bind mounts) for production readiness
- Add development-specific overrides in `docker-compose.development.yml` (e.g., exposed ports, debug modes)
- Include health checks for all services
- Document connection strings and credentials in `config/.env.example`
- Never commit actual credentials

### PostgreSQL Database
**Priority:** High  
**Status:** Not Started

Create `.github/prompts/scaffold-postgres.md`:

- [ ] Add PostgreSQL service with:
  - Official `postgres:16-alpine` image (or latest stable)
  - Named volume for `/var/lib/postgresql/data`
  - Environment variables: `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD` (from `.env`)
  - Health check using `pg_isready`
- [ ] Install common extensions in custom Dockerfile or init script:
  - PostGIS (geospatial)
  - pgvector (vector similarity)
  - uuid-ossp (UUID generation)
  - pg_trgm (fuzzy text search)
- [ ] Add pgAdmin service in `docker-compose.development.yml` for GUI management
- [ ] Include database initialization script support (`./config/postgres-init/` mounted to `/docker-entrypoint-initdb.d/`)
- [ ] Document backup/restore procedures
- [ ] Add connection pooling recommendations (PgBouncer)

**Environment Variables (`.env.example`):**
```
POSTGRES_HOST=postgres
POSTGRES_PORT=5432
POSTGRES_DB=app_db
POSTGRES_USER=app_user
POSTGRES_PASSWORD=changeme
```

**Best Practices:**
- Use `POSTGRES_PASSWORD_FILE` for Docker secrets in production
- Pin major version to avoid breaking changes
- Configure `shared_buffers` and `max_connections` for workload
- Enable query logging in development, disable in production

### Redis Cache & Session Store
**Priority:** High  
**Status:** Not Started

Create `.github/prompts/scaffold-redis.md`:

- [ ] Add Redis service with:
  - Official `redis:7-alpine` image
  - Named volume for `/data` (persistence via AOF or RDB)
  - Health check using `redis-cli ping`
  - Optional password protection (`REDIS_PASSWORD`)
- [ ] Configure persistence strategy:
  - AOF (append-only file) for durability
  - RDB snapshots for backups
- [ ] Add Redis Commander or RedisInsight in development overlay for GUI
- [ ] Include sentinel configuration template for HA (optional)
- [ ] Document TTL strategies and memory eviction policies

**Environment Variables (`.env.example`):**
```
REDIS_HOST=redis
REDIS_PORT=6379
REDIS_PASSWORD=changeme
REDIS_DB=0
```

**Best Practices:**
- Enable AOF persistence for critical session data
- Set `maxmemory-policy` (e.g., `allkeys-lru`) to prevent OOM
- Use separate Redis instances for cache vs. queues
- Monitor slow queries with `SLOWLOG`

### RabbitMQ Message Broker
**Priority:** Medium  
**Status:** Not Started

Create `.github/prompts/scaffold-rabbitmq.md`:

- [ ] Add RabbitMQ service with:
  - Official `rabbitmq:3-management-alpine` image
  - Named volume for `/var/lib/rabbitmq`
  - Management UI exposed in development (port 15672)
  - Environment variables: `RABBITMQ_DEFAULT_USER`, `RABBITMQ_DEFAULT_PASS`
  - Health check using management API
- [ ] Configure default vhost and permissions
- [ ] Add plugin support (e.g., `rabbitmq_delayed_message_exchange`)
- [ ] Document exchange types (direct, topic, fanout, headers)
- [ ] Include dead-letter exchange setup for failed messages
- [ ] Add monitoring via Prometheus plugin (optional)

**Environment Variables (`.env.example`):**
```
RABBITMQ_HOST=rabbitmq
RABBITMQ_PORT=5672
RABBITMQ_MANAGEMENT_PORT=15672
RABBITMQ_DEFAULT_USER=admin
RABBITMQ_DEFAULT_PASS=changeme
RABBITMQ_DEFAULT_VHOST=/
```

**Best Practices:**
- Use separate queues per service/responsibility
- Implement message acknowledgments and retries
- Set TTL on messages to prevent unbounded growth
- Monitor queue depth and consumer lag

### OpenSearch (ElasticSearch Alternative)
**Priority:** Medium  
**Status:** Not Started

Create `.github/prompts/scaffold-opensearch.md`:

- [ ] Add OpenSearch service with:
  - Official `opensearchproject/opensearch:2` image
  - Named volume for `/usr/share/opensearch/data`
  - Disable security plugin in development (`DISABLE_SECURITY_PLUGIN=true`)
  - Health check using REST API (`/_cluster/health`)
- [ ] Add OpenSearch Dashboards in development overlay
- [ ] Configure memory settings (`ES_JAVA_OPTS: "-Xms512m -Xmx512m"`)
- [ ] Document index templates and mappings
- [ ] Include index lifecycle management (ILM) recommendations
- [ ] Add ingest pipeline examples

**Environment Variables (`.env.example`):**
```
OPENSEARCH_HOST=opensearch
OPENSEARCH_PORT=9200
OPENSEARCH_USER=admin
OPENSEARCH_PASSWORD=changeme
OPENSEARCH_INITIAL_ADMIN_PASSWORD=ComplexPassword123!
```

**Best Practices:**
- Use single-node discovery mode in development
- Enable security plugin with TLS in production
- Configure shard allocation and replica settings
- Monitor cluster health and disk usage

### MinIO (S3-Compatible Object Storage)
**Priority:** Medium  
**Status:** Not Started

Create `.github/prompts/scaffold-minio.md`:

- [ ] Add MinIO service with:
  - Official `minio/minio:latest` image
  - Named volumes for data (`/data`) and config
  - Console exposed in development (port 9001)
  - Environment variables: `MINIO_ROOT_USER`, `MINIO_ROOT_PASSWORD`
  - Health check using `/minio/health/live`
- [ ] Configure default buckets via init script or mc client
- [ ] Add bucket lifecycle policies (retention, versioning)
- [ ] Document S3 SDK integration examples (boto3, aws-sdk)
- [ ] Include presigned URL generation patterns
- [ ] Add MinIO Client (mc) commands for administration

**Environment Variables (`.env.example`):**
```
MINIO_HOST=minio
MINIO_PORT=9000
MINIO_CONSOLE_PORT=9001
MINIO_ROOT_USER=minioadmin
MINIO_ROOT_PASSWORD=changeme
MINIO_BUCKET_NAME=app-storage
MINIO_REGION=us-east-1
```

**Best Practices:**
- Use separate buckets per environment/tenant
- Enable versioning for critical data
- Configure CORS for browser uploads
- Implement bucket policies for access control
- Use presigned URLs for secure temporary access

---

## 🚀 Services (Application Microservices)

All service prompts should be created in `.github/prompts/` and include:
- Complete project scaffolding with proper directory structure
- Dockerfile with multi-stage builds (dev + prod)
- `devcontainer.json` configuration for VS Code Dev Containers
- Docker Compose service definition with health checks
- Environment variable schema
- Test suite setup with coverage reporting
- CI/CD pipeline template (GitHub Actions)
- README with quick start instructions

**Testing Guidelines:**
- Tests live in service folder (e.g., `services/api/tests/`)
- Use technology-specific frameworks (pytest, Jest, Go testing, etc.)
- Include unit, integration, and E2E tests
- Target >80% code coverage
- Mock external dependencies in unit tests
- Use test containers for integration tests

### FastAPI REST API (Python)
**Priority:** High  
**Status:** Not Started

Create `.github/prompts/scaffold-fastapi-service.md`:

- [ ] Generate service structure:
  ```
  services/api/
  ├── Dockerfile (multi-stage: dev + prod)
  ├── requirements.txt (pinned versions)
  ├── requirements-dev.txt (testing, linting)
  ├── pyproject.toml (ruff, mypy, pytest config)
  ├── .env.example
  ├── src/
  │   ├── __init__.py
  │   ├── main.py (FastAPI app, lifespan, CORS)
  │   ├── config.py (pydantic-settings)
  │   ├── models/ (Pydantic models)
  │   │   ├── __init__.py
  │   │   └── domain.py
  │   ├── schemas/ (API request/response schemas)
  │   │   ├── __init__.py
  │   │   └── api.py
  │   ├── persistence/ (database layer)
  │   │   ├── __init__.py
  │   │   ├── database.py (SQLAlchemy engine, session)
  │   │   ├── models.py (ORM models)
  │   │   └── repositories.py
  │   ├── routers/ (API endpoints)
  │   │   ├── __init__.py
  │   │   └── v1/
  │   ├── dependencies.py (FastAPI dependencies)
  │   └── middleware.py (logging, error handling)
  └── tests/
      ├── __init__.py
      ├── conftest.py (pytest fixtures)
      ├── unit/
      │   └── test_models.py
      ├── integration/
      │   └── test_repositories.py
      └── e2e/
          └── test_api.py
  ```
- [ ] Add dependencies:
  - `fastapi` — Web framework
  - `uvicorn[standard]` — ASGI server
  - `sqlalchemy` — ORM
  - `alembic` — Database migrations
  - `pydantic-settings` — Configuration management
  - `asyncpg` or `psycopg[binary]` — PostgreSQL driver
  - `redis[hiredis]` — Redis client
  - `httpx` — Async HTTP client
- [ ] Add dev dependencies:
  - `pytest` — Test framework
  - `pytest-asyncio` — Async test support
  - `pytest-cov` — Coverage reporting
  - `httpx` — API testing
  - `faker` — Test data generation
  - `ruff` — Linting and formatting
  - `mypy` — Type checking
- [ ] Configure Docker Compose service:
  - Health check via `/health` endpoint
  - Hot reload in development (mount `./services/api/src:/app/src`)
  - Environment variables from `.env`
  - Depends on `postgres`, `redis`
- [ ] Add `.devcontainer/api-container/devcontainer.json`:
  - Service: `api`
  - Workspace folder: `/workspace/services/api`
  - Extensions: Python, Pylance, Ruff, autoDocstring
  - Post-create command: `pip install -r requirements-dev.txt`
- [ ] Include Alembic migration setup
- [ ] Add OpenAPI documentation customization
- [ ] Document authentication/authorization patterns (JWT)

**Best Practices:**
- Use Pydantic V2 for all models and config
- Implement dependency injection for testability
- Use async/await throughout for I/O operations
- Separate domain models from ORM models
- Use repository pattern for data access
- Configure CORS properly (restrictive in prod)
- Implement request ID tracing via middleware
- Add structured logging (JSON format)

### Static Site Generator (React + Markdown)
**Priority:** Medium  
**Status:** Not Started

Create `.github/prompts/scaffold-static-site-service.md`:

- [ ] Generate service structure:
  ```
  services/frontend/
  ├── Dockerfile (multi-stage: build + nginx)
  ├── package.json
  ├── package-lock.json
  ├── vite.config.ts
  ├── tsconfig.json
  ├── .env.example
  ├── nginx.conf (production)
  ├── public/
  ├── src/
  │   ├── main.tsx
  │   ├── App.tsx
  │   ├── content/ (Markdown files)
  │   │   └── pages/
  │   ├── components/
  │   │   ├── Layout.tsx
  │   │   ├── MarkdownRenderer.tsx
  │   │   └── Form.tsx
  │   ├── hooks/
  │   │   └── useApi.ts
  │   ├── lib/
  │   │   ├── markdown.ts
  │   │   └── api-client.ts
  │   └── styles/
  └── tests/
      ├── unit/
      │   └── components/
      └── e2e/
          └── playwright/
  ```
- [ ] Add dependencies:
  - `react` + `react-dom` — UI framework
  - `vite` — Build tool
  - `react-router-dom` — Routing
  - `react-markdown` — Markdown rendering
  - `remark-gfm` — GitHub Flavored Markdown
  - `react-hook-form` — Form management
  - `zod` — Schema validation
  - `axios` or `ky` — HTTP client
  - `tailwindcss` (optional) — Styling
- [ ] Add dev dependencies:
  - `@testing-library/react` — Component testing
  - `@testing-library/jest-dom` — DOM assertions
  - `vitest` — Test runner
  - `@playwright/test` — E2E testing
  - `eslint` + `@typescript-eslint` — Linting
  - `prettier` — Code formatting
- [ ] Configure Vite for:
  - Markdown import as modules
  - Static asset optimization
  - Environment variable injection
  - Build output to `dist/`
- [ ] Add nginx configuration for SPA routing
- [ ] Implement form components with validation
- [ ] Add API client with error handling and retries
- [ ] Document content authoring workflow

**Best Practices:**
- Use TypeScript for type safety
- Implement code splitting per route
- Add loading states and error boundaries
- Use React.memo for expensive renders
- Implement form validation with Zod schemas
- Add accessibility (ARIA labels, keyboard nav)
- Optimize images (lazy loading, WebP)
- Use service worker for offline support (optional)

### Node.js Worker Service (Queue Consumer)
**Priority:** Medium  
**Status:** Not Started

Create `.github/prompts/scaffold-nodejs-worker-service.md`:

- [ ] Generate service structure:
  ```
  services/worker/
  ├── Dockerfile (multi-stage)
  ├── package.json
  ├── package-lock.json
  ├── tsconfig.json
  ├── .env.example
  ├── src/
  │   ├── index.ts (entry point)
  │   ├── config.ts (env validation)
  │   ├── queue/
  │   │   ├── consumer.ts
  │   │   └── handlers/
  │   │       ├── email-job.ts
  │   │       ├── export-job.ts
  │   │       └── index.ts
  │   ├── services/
  │   │   └── external-api.ts
  │   └── utils/
  │       ├── logger.ts
  │       └── metrics.ts
  └── tests/
      ├── unit/
      │   └── handlers/
      └── integration/
          └── queue/
  ```
- [ ] Add dependencies:
  - `amqplib` or `bullmq` — Queue client
  - `zod` — Job payload validation
  - `pino` — Structured logging
  - `ioredis` (for BullMQ) — Redis client
  - `node-cron` (optional) — Scheduled jobs
- [ ] Add dev dependencies:
  - `jest` — Test framework
  - `ts-jest` — TypeScript support
  - `@types/node` — Node.js types
  - `nodemon` — Hot reload
  - `eslint` + `@typescript-eslint`
  - `prettier`
- [ ] Implement job handlers:
  - Idempotent processing (handle retries)
  - Error handling with dead-letter queue
  - Job status updates
  - Graceful shutdown on SIGTERM
- [ ] Add health check endpoint (HTTP server on separate port)
- [ ] Configure concurrency and prefetch limits
- [ ] Document job schemas and retry policies

**Best Practices:**
- Use TypeScript for type-safe job payloads
- Implement exponential backoff for retries
- Add distributed tracing (OpenTelemetry)
- Monitor queue depth and processing latency
- Use separate queues per job type
- Implement circuit breakers for external APIs
- Add metrics export (Prometheus)
- Handle poison messages (invalid payloads)

---

## 🔌 Model Context Protocol (MCP) Servers

**Priority:** High  
**Status:** In Progress (mcp.json configured)

### MCP Servers Configured in `templates/.vscode/mcp.json`

The following MCP servers are configured and require Node.js/npm runtime in all service devcontainers:

1. **Context7** (`@upstash/context7-mcp@latest`)
   - **Purpose:** Library documentation lookup
   - **Runtime:** Node.js + npx
   - **Credentials:** None required

2. **Brave Search** (`@brave/brave-search-mcp-server`)
   - **Purpose:** Web search capabilities
   - **Runtime:** Node.js + npx
   - **Credentials:** Required via `.env.mcp.credentials` (BRAVE_API_KEY)

3. **Playwright** (`@playwright/mcp@latest`)
   - **Purpose:** Browser automation and web scraping
   - **Runtime:** Node.js + npx + Playwright browsers
   - **Credentials:** None required

4. **Filesystem** (`@modelcontextprotocol/server-filesystem`)
   - **Purpose:** File system operations within workspace
   - **Runtime:** Node.js + npx
   - **Credentials:** None required
   - **Scope:** Limited to `${workspaceFolder}`

5. **Git** (`@modelcontextprotocol/server-git`)
   - **Purpose:** Git repository operations
   - **Runtime:** Node.js + npx + git CLI
   - **Credentials:** None required (uses workspace git config)
   - **Scope:** Limited to `${workspaceFolder}`

### Runtime Requirements Per Service Devcontainer

All service devcontainers must include these runtime dependencies to support MCP servers:

#### Base Requirements (All Services)
- [ ] **Node.js 20+** (LTS) installed
- [ ] **npm** package manager
- [ ] **npx** available in PATH
- [ ] **git** CLI installed and configured

#### Service-Specific MCP Runtime Setup

##### FastAPI Service (`services/api/`)
**Dockerfile additions:**
```dockerfile
# Add Node.js to Python-based image
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - && \
    apt-get install -y nodejs git
```

**devcontainer.json additions:**
- [ ] Add `postCreateCommand` to verify Node.js installation
- [ ] Include `git` in features or install via Dockerfile
- [ ] Mount `.env.mcp.credentials` if using Brave Search

##### Static Site Service (`services/frontend/`)
**Status:** Node.js already present (React/Vite stack)
- [ ] Verify Node.js version >=20
- [ ] Ensure git is installed
- [ ] Configure `.env.mcp.credentials` mount

##### Node.js Worker Service (`services/worker/`)
**Status:** Node.js already present
- [ ] Verify Node.js version >=20
- [ ] Ensure git is installed
- [ ] Configure `.env.mcp.credentials` mount

#### Playwright-Specific Requirements
For services using Playwright MCP server heavily:
- [ ] Install Playwright browsers: `npx playwright install --with-deps`
- [ ] Add system dependencies (chromium, ffmpeg, etc.)
- [ ] Allocate sufficient container memory (2GB+)
- [ ] Configure `--no-sandbox` for containerized Chrome

**Example devcontainer.json postCreateCommand:**
```json
{
  "postCreateCommand": "node --version && npm --version && git --version && echo 'MCP runtimes verified'"
}
```

### Credentials Management

#### `.env.mcp.credentials` Configuration
Create `config/.env.mcp.credentials.example`:

```bash
# Brave Search API Key (required for @brave/brave-search-mcp-server)
BRAVE_API_KEY=your_api_key_here

# Future MCP server credentials can be added here
```

**Tasks:**
- [ ] Create `config/.env.mcp.credentials.example` template
- [ ] Add `.env.mcp.credentials` to `.gitignore`
- [ ] Document Brave Search API key acquisition in README
- [ ] Update each `devcontainer.json` to reference:
  ```json
  {
    "remoteEnv": {
      "MCP_ENV_FILE": "${localWorkspaceFolder}/config/.env.mcp.credentials"
    }
  }
  ```

### Documentation Tasks

### Documentation
Create `docs/mcp-servers.md`:

- [ ] Document each MCP server's purpose and capabilities
- [ ] Provide usage examples for each server
- [ ] Include ASCII art architecture diagram showing:
  - MCP server invocation flow (VS Code → npx → MCP server)
  - Credential file flow (`.env.mcp.credentials` → MCP server)
  - Service devcontainer runtime stack (Node.js, npm, npx, git)
  - Data flow between MCP servers and workspace
- [ ] Example diagram structure:
  ```
  ┌──────────────────────────────────────────────────────────┐
  │                    VS Code IDE                            │
  │  ┌─────────────────────────────────────────────────┐    │
  │  │              Copilot Chat                       │    │
  │  └─────────────────┬───────────────────────────────┘    │
  └────────────────────┼──────────────────────────────────────┘
                       │ MCP Protocol
                       ↓
  ┌──────────────────────────────────────────────────────────┐
  │           Service Devcontainer (e.g., API)               │
  │                                                           │
  │  Runtime Stack:                                          │
  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
  │  │   Node.js    │  │      npm     │  │      git     │  │
  │  └──────────────┘  └──────────────┘  └──────────────┘  │
  │                                                           │
  │  ┌────────────────────────────────────────────────┐     │
  │  │          npx (MCP Server Launcher)             │     │
  │  └───┬────────────────────────────────────────────┘     │
  │      │                                                    │
  │      ├─→ @upstash/context7-mcp                          │
  │      ├─→ @brave/brave-search-mcp ← .env.mcp.credentials│
  │      ├─→ @playwright/mcp                                │
  │      ├─→ @modelcontextprotocol/server-filesystem        │
  │      └─→ @modelcontextprotocol/server-git               │
  │                                                           │
  │  ┌────────────────────────────────────────────────┐     │
  │  │           Workspace Files                      │     │
  │  │  /workspace/services/api/src/...               │     │
  │  │  /workspace/.git/...                           │     │
  │  │  /workspace/config/.env.mcp.credentials        │     │
  │  └────────────────────────────────────────────────┘     │
  └──────────────────────────────────────────────────────────┘
  ```
- [ ] Include troubleshooting guide:
  - Node.js not found in PATH
  - npx permission errors
  - Playwright browser installation failures
  - Git authentication issues
- [ ] Add instructions for testing MCP server availability:
  ```bash
  npx -y @upstash/context7-mcp@latest --help
  ```
- [ ] Document how to add new MCP servers to `mcp.json`
- [ ] Include security best practices (API key rotation, scope limitations)

### Devcontainer Integration Checklist

For each new service scaffolded:

- [ ] Install Node.js 20+ in Dockerfile (if not JavaScript/TypeScript service)
- [ ] Install git CLI in Dockerfile
- [ ] Copy `templates/.vscode/mcp.json` to service `.vscode/` folder
- [ ] Mount workspace root at `/workspace` (required for `${workspaceFolder}` substitution)
- [ ] Add environment variable for MCP credentials file path
- [ ] Test MCP server execution in postCreateCommand
- [ ] Verify all 5 MCP servers can be invoked via npx
- [ ] Document any service-specific MCP configuration

### Prompt File Updates

Update service scaffolding prompts to include MCP runtime setup:

- [ ] `scaffold-fastapi-service.md` — Add Node.js installation to Dockerfile
- [ ] `scaffold-static-site-service.md` — Verify Node.js version compatibility
- [ ] `scaffold-nodejs-worker-service.md` — Document git installation requirement
- [ ] Create `scaffold-devcontainer-mcp.md` — Reusable MCP setup instructions

### Testing & Validation

- [ ] Create test script to validate MCP server availability in each devcontainer
- [ ] Add CI check to ensure all MCP servers can be invoked
- [ ] Document manual testing procedure for new services
- [ ] Create troubleshooting runbook for common MCP issues

**Best Practices:**
- Always use `npx -y` to auto-accept package installations
- Pin MCP server versions in production environments (remove `@latest`)
- Cache npx packages in devcontainer to avoid repeated downloads
- Implement timeout handling for MCP server invocations
- Use `.env.mcp.credentials` for all sensitive MCP credentials
- Never commit `.env.mcp.credentials` to version control
- Test MCP servers in isolated environment before devcontainer integration
- Monitor MCP server resource usage (memory, network)

---

## 📚 Instructions & Guardrails

**Priority:** High  
**Status:** Partially Complete

### New Instruction Files
Create these in `.github/instructions/`:

#### `production-vs-development.instructions.md`
**Status:** Not Started

- [ ] Document separation of concerns:
  - Production config: `docker-compose.yml`, service Dockerfiles (prod stage)
  - Development config: `docker-compose.development.yml`, devcontainer.json, dev dependencies
- [ ] Define what belongs where:
  - Production: Pinned versions, minimal images, no exposed ports, secrets via environment
  - Development: Debug tools, exposed ports, hot reload, verbose logging
- [ ] Add checklist for new service additions
- [ ] Include examples of correct separation
- [ ] Document `.env.example` vs `.env` usage
- [ ] Add validation criteria (no dev tools in prod images)

**Applies to:** All docker-compose files, Dockerfiles, package manifests

#### `context7-integration.instructions.md`
**Status:** Not Started

- [ ] Require Context7 for all library documentation needs
- [ ] Document workflow:
  1. Identify library/framework in user request
  2. Use `resolve-library-id` to find Context7 ID
  3. Fetch docs with `get-library-docs`
  4. Reference official patterns from Context7 response
- [ ] Add examples of proper Context7 usage
- [ ] Include fallback strategy if library not in Context7
- [ ] Emphasize using official docs over hallucinating APIs

**Applies to:** All code generation prompts

#### `environment-configuration.instructions.md`
**Status:** Not Started

- [ ] Mandate `.env` for all environment-specific config
- [ ] Document `.env.example` as template (committed)
- [ ] Specify `.env` must be gitignored
- [ ] Define naming conventions: `SERVICE_SETTING_NAME`
- [ ] Include validation patterns (Pydantic, Zod)
- [ ] Add secrets management guidance (Docker secrets, Vault)
- [ ] Document config precedence (env vars > .env > defaults)

**Applies to:** All services, docker-compose files

#### `testing-standards.instructions.md`
**Status:** Not Started

- [ ] Define test structure per service type:
  - `tests/unit/` — Pure logic, mocked dependencies
  - `tests/integration/` — Database, external services (test containers)
  - `tests/e2e/` — Full user workflows
- [ ] Specify technology-specific frameworks:
  - Python: pytest + pytest-asyncio
  - TypeScript/JavaScript: Vitest or Jest
  - Go: testing package + testify
- [ ] Mandate coverage thresholds (>80%)
- [ ] Document fixture/mock patterns
- [ ] Add CI/CD test pipeline examples
- [ ] Include performance testing guidelines

**Applies to:** All service scaffolding prompts

#### `health-checks.instructions.md`
**Status:** Not Started

- [ ] Require health check endpoints for all services
- [ ] Document standard paths:
  - `/health` — Liveness (is app running?)
  - `/ready` — Readiness (can accept traffic?)
- [ ] Include dependency checks in readiness (DB, Redis, etc.)
- [ ] Specify timeout and interval recommendations
- [ ] Add Docker Compose health check syntax
- [ ] Document Kubernetes liveness/readiness probes

**Applies to:** All services, backing services

#### `prompt-refinement.instructions.md`
**Status:** Not Started
**Priority:** High

- [ ] Mandate prompt refinement workflow for all user requests
- [ ] Define refinement process:
  1. Accept user's original prompt
  2. Analyze intent and context
  3. Identify ambiguities, missing requirements, or unclear scope
  4. Refactor prompt for clarity, completeness, and specificity
  5. Add relevant technical constraints and best practices
  6. Present refined prompt to user for approval before execution
- [ ] Document refinement criteria:
  - [ ] Remove ambiguous language
  - [ ] Add explicit file paths and locations
  - [ ] Specify technology stack and versions
  - [ ] Include error handling requirements
  - [ ] Add testing expectations
  - [ ] Reference relevant documentation (Context7, official docs)
  - [ ] Clarify edge cases and constraints
  - [ ] Break complex requests into discrete steps
- [ ] Include prompt improvement patterns:
  ```
  Original: "Add a database"
  Refined:  "Add PostgreSQL 16 backing service to docker-compose.yml 
             between ##<<BACKING_SERVICES>> markers with:
             - Named volume for data persistence
             - Health check using pg_isready
             - Environment variables from config/.env
             - Connection pooling recommendations documented"
  
  Original: "Create an API"
  Refined:  "Scaffold FastAPI service in services/api/ with:
             - Python 3.11+ with Pydantic V2 models
             - SQLAlchemy 2.0 async ORM
             - Alembic migrations
             - Pytest test suite (unit/integration/e2e)
             - Dockerfile with multi-stage build
             - devcontainer.json for VS Code
             - OpenAPI documentation at /docs
             - Health check endpoint at /health"
  ```
- [ ] Add ASCII art diagram showing refinement flow:
  ```
  User Input → Copilot Analysis → Refinement → User Approval → Execution
      │              │                │              │             │
      │              │                │              │             │
      ▼              ▼                ▼              ▼             ▼
  "Add DB"    Identify gaps    Add details    Confirm scope   Create files
              (which DB?       (PostgreSQL    (yes/modify)    Generate code
               where?           16, compose                    Update docs
               config?)         markers, etc.)
  ```
- [ ] Document prompt history logging (see Meta Tasks below)
- [ ] Include examples of good vs. poor prompts
- [ ] Add troubleshooting for prompt refinement failures

**Applies to:** All Copilot interactions, all prompt files

---

## 📝 Meta Tasks

### Prompt History & Tracking
**Priority:** High
**Status:** Not Started

- [ ] Create `docs/prompts/history.md` for tracking all refined prompts
- [ ] Define history entry format:
  ```markdown
  ## [YYYY-MM-DD HH:MM:SS] — Model: GPT-5
  
  ### Original Prompt
  ```
  [User's original request verbatim]
  ```
  
  ### Refined Prompt
  ```
  [Copilot's refined version with added context and specifics]
  ```
  
  ### Refinement Rationale
  - Added: [what was added and why]
  - Clarified: [ambiguities resolved]
  - Scoped: [boundaries defined]
  
  ### Execution Result
  - Files created: [list]
  - Files modified: [list]
  - Status: ✓ Success / ✗ Failed / ⚠ Partial
  - Notes: [any issues or observations]
  
  ---
  ```
- [ ] Implement automatic history logging:
  - [ ] Copilot appends entry to `docs/prompts/history.md` after each refined prompt execution
  - [ ] Include timestamp (ISO 8601 format)
  - [ ] Record model used (GPT-5, GPT-4, etc.)
  - [ ] Capture both original and refined prompts
  - [ ] Log execution outcome (success/failure/partial)
  - [ ] Track files created/modified
- [ ] Add history search/filter capabilities:
  - [ ] By date range
  - [ ] By model used
  - [ ] By success/failure status
  - [ ] By files affected
  - [ ] By prompt topic (database, service, config, etc.)
- [ ] Create analysis tools:
  - [ ] Script to generate prompt effectiveness metrics
  - [ ] Identify common refinement patterns
  - [ ] Track which refinements lead to successful outcomes
  - [ ] Generate improvement recommendations
- [ ] Add history file maintenance:
  - [ ] Archive old entries (monthly or quarterly)
  - [ ] Maintain index for quick lookup
  - [ ] Compress archived history
- [ ] Document history file usage:
  - [ ] How to review past prompts
  - [ ] Learning from successful refinements
  - [ ] Avoiding repeated mistakes
  - [ ] Sharing effective prompts with team

**Example History Entry:**
```markdown
## [2025-10-06 14:32:17] — Model: GPT-5

### Original Prompt
```
Add Redis to the project
```

### Refined Prompt
```
Scaffold Redis backing service with:
- Official redis:7-alpine image
- Named volume for /data persistence (AOF enabled)
- Health check using redis-cli ping
- Environment variables in config/.env.example:
  - REDIS_HOST=redis
  - REDIS_PORT=6379
  - REDIS_PASSWORD=changeme
- Add Redis Commander in docker-compose.development.yml for GUI
- Document connection string and TTL strategies in docs/services/
- Place service definition in docker-compose.yml between ##<<BACKING_SERVICES>> markers
```

### Refinement Rationale
- Added: Specific image version (7-alpine) for reproducibility
- Clarified: Persistence strategy (AOF vs RDB)
- Scoped: Development vs production configuration separation
- Added: Health check for orchestration dependencies
- Added: Environment variable schema with examples
- Added: Development tooling (Redis Commander)
- Added: Documentation requirements
- Added: Exact placement using marker system

### Execution Result
- Files created:
  - None (prompt file to be created)
- Files modified:
  - TODO.md (added Redis scaffolding task)
- Status: ✓ Success
- Notes: User approved refined prompt; ready for implementation

---
```

### Prompt File Standards
- [ ] Create `.github/prompts/README.md` documenting prompt file conventions
- [ ] Add template for new prompt files
- [ ] Include examples of good vs. bad prompts
- [ ] Document how to test prompts before committing
- [ ] Each prompt file should reference relevant docs in `docs/` folder
- [ ] Prompt files should generate ASCII art diagrams in any documentation they create
- [ ] Reference `docs/prompts/history.md` for proven prompt patterns

### Documentation Improvements

**All documentation goes in `docs/` folder with mandatory ASCII art diagrams.**

#### `docs/architecture/` — System Architecture Documentation
**Status:** Not Started

- [ ] Create `docs/architecture/overview.md`:
  - [ ] ASCII art diagram of entire microservices architecture
  - [ ] Show all services, backing services, development services
  - [ ] Indicate network boundaries (Docker networks)
  - [ ] Mark data persistence points (volumes)
  - [ ] Label communication protocols (HTTP, gRPC, message queues)
  - [ ] Example structure:
    ```
    ┌────────────────────────────────────────────────────────────────┐
    │                    Development Environment                      │
    │  ┌──────────────────────────────────────────────────────────┐  │
    │  │            Docker Compose Network: app-network           │  │
    │  │                                                           │  │
    │  │  ┌─────────────┐     ┌─────────────┐     ┌───────────┐  │  │
    │  │  │   Frontend  │────→│     API     │────→│  Postgres │  │  │
    │  │  │   (React)   │HTTP │  (FastAPI)  │SQL  │  (DB)     │  │  │
    │  │  └─────────────┘     └──────┬──────┘     └───────────┘  │  │
    │  │                             │                            │  │
    │  │                             │ Redis Protocol            │  │
    │  │                             ↓                            │  │
    │  │                      ┌─────────────┐                    │  │
    │  │                      │    Redis    │                    │  │
    │  │                      │   (Cache)   │                    │  │
    │  │                      └─────────────┘                    │  │
    │  │                                                           │  │
    │  │  ┌─────────────┐     ┌─────────────┐                    │  │
    │  │  │   Worker    │←───→│  RabbitMQ   │                    │  │
    │  │  │  (Node.js)  │AMQP │   (Queue)   │                    │  │
    │  │  └─────────────┘     └─────────────┘                    │  │
    │  └──────────────────────────────────────────────────────────┘  │
    │                                                                 │
    │  Development Services (docker-compose.development.yml):        │
    │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐           │
    │  │   Ollama    │  │  pgAdmin    │  │   Mailhog   │           │
    │  │   (LLM)     │  │    (UI)     │  │  (Email)    │           │
    │  └─────────────┘  └─────────────┘  └─────────────┘           │
    └────────────────────────────────────────────────────────────────┘
    
    Data Persistence:
    ┌─────────────┐
    │  Volumes:   │
    │  - postgres │  ← Named volume for PostgreSQL data
    │  - redis    │  ← Named volume for Redis data
    │  - rabbitmq │  ← Named volume for RabbitMQ data
    └─────────────┘
    
    ┌─────────────┐
    │  Bind Mounts│
    │  - ./tmp    │  ← Local directory for Ollama models, logs
    │  - ./config │  ← Configuration files (.env)
    └─────────────┘
    ```

- [ ] Create `docs/architecture/data-flow.md`:
  - [ ] ASCII art diagrams for each major workflow
  - [ ] Request/response flow through services
  - [ ] Background job processing flow
  - [ ] Authentication/authorization flow
  - [ ] Example:
    ```
    User Request Flow:
    
    Browser              Frontend            API              Database
       │                    │                 │                   │
       │─────GET /page─────→│                 │                   │
       │                    │                 │                   │
       │                    │──GET /api/data─→│                   │
       │                    │                 │                   │
       │                    │                 │─SELECT * FROM─→│
       │                    │                 │                   │
       │                    │                 │←─────results────│
       │                    │                 │                   │
       │                    │←─JSON response──│                   │
       │                    │                 │                   │
       │←──HTML + data──────│                 │                   │
       │                    │                 │                   │
    ```

- [ ] Create `docs/architecture/network-topology.md`:
  - [ ] Docker network configuration diagram
  - [ ] Port mappings and exposure
  - [ ] Internal vs. external communication
  - [ ] Load balancing points (if applicable)

#### `docs/adr/` — Architecture Decision Records
**Status:** Not Started

- [ ] Set up ADR folder structure:
  - `docs/adr/0001-use-fastapi-for-backend.md`
  - `docs/adr/0002-postgres-over-mysql.md`
  - `docs/adr/0003-docker-compose-for-local-dev.md`
- [ ] Each ADR must include:
  - [ ] Context diagram (ASCII art showing problem space)
  - [ ] Decision diagram (ASCII art showing chosen solution)
  - [ ] Consequences diagram (ASCII art showing impact)
- [ ] Template example:
  ```markdown
  # ADR-0001: Use FastAPI for Backend Services
  
  ## Status
  Accepted
  
  ## Context
  
  Problem Space:
  ┌─────────────────────────────────────────────────────┐
  │  Need: High-performance async API framework         │
  │                                                      │
  │  Requirements:                                       │
  │  ✓ OpenAPI/Swagger auto-generation                 │
  │  ✓ Type hints and validation                       │
  │  ✓ Async/await support                             │
  │  ✓ Easy testing                                     │
  └─────────────────────────────────────────────────────┘
  
  ## Decision
  
  Chosen Solution:
  ┌─────────────────────────────────────────────────────┐
  │              FastAPI Framework                      │
  │                                                      │
  │  ┌──────────┐  ┌──────────┐  ┌──────────┐         │
  │  │ Pydantic │  │  Starlette │  │ OpenAPI │         │
  │  │  Models  │  │   (ASGI)   │  │  Docs   │         │
  │  └──────────┘  └──────────┘  └──────────┘         │
  └─────────────────────────────────────────────────────┘
  
  ## Consequences
  
  Impact:
  ┌─────────────────────────────────────────────────────┐
  │  Positive:                 │  Negative:             │
  │  ✓ Auto API docs          │  ✗ Learning curve      │
  │  ✓ Type safety            │  ✗ Less mature than    │
  │  ✓ High performance       │    Flask/Django        │
  │  ✓ Modern async patterns  │                         │
  └─────────────────────────────────────────────────────┘
  ```

#### `docs/services/` — Service-Specific Documentation
**Status:** Not Started

- [ ] Create docs for each service type:
  - `docs/services/api-service.md` — FastAPI service architecture
  - `docs/services/frontend-service.md` — React frontend architecture
  - `docs/services/worker-service.md` — Background worker architecture
- [ ] Each service doc must include:
  - [ ] Internal architecture diagram (layers, modules)
  - [ ] External dependencies diagram (databases, APIs, queues)
  - [ ] Request flow diagram (how requests are processed)
  - [ ] Testing architecture diagram (unit → integration → e2e)

#### `docs/operations/` — Operational Procedures
**Status:** Not Started

- [ ] Create `docs/operations/deployment.md`:
  - [ ] Deployment pipeline diagram
  - [ ] Environment promotion flow (dev → staging → prod)
  - [ ] Rollback procedure diagram
- [ ] Create `docs/operations/monitoring.md`:
  - [ ] Monitoring stack diagram (metrics, logs, traces)
  - [ ] Alert flow diagram
- [ ] Create `docs/operations/backup-restore.md`:
  - [ ] Backup architecture diagram
  - [ ] Disaster recovery flow
- [ ] Create `docs/operations/troubleshooting.md`:
  - [ ] Common issue decision trees (ASCII flowcharts)
  - [ ] Debug flow diagrams

#### `docs/getting-started/` — Onboarding Documentation
**Status:** Not Started

- [ ] Create `docs/getting-started/workspace-setup.md`:
  - [ ] Setup flow diagram (clone → scaffold → configure → launch)
  - [ ] File structure diagram (where everything goes)
- [ ] Create `docs/getting-started/adding-a-service.md`:
  - [ ] Service creation workflow diagram
  - [ ] Integration points diagram
- [ ] Create `docs/getting-started/development-workflow.md`:
  - [ ] Daily development cycle diagram
  - [ ] Git workflow diagram (branching, PRs, merges)

### Automation
- [ ] Create GitHub Action to validate prompt files
- [ ] Add pre-commit hook to check `.env.example` completeness
- [ ] Implement automated dependency updates (Renovate/Dependabot)
- [ ] Add license compliance checker

---

## 🎯 Priority Summary

**High Priority (Start Here):**
1. Prompt refinement instruction file (`prompt-refinement.instructions.md`)
2. Prompt history tracking system (`docs/prompts/history.md`)
3. Documentation standards enforcement (ASCII art conventions)
4. `docs/architecture/overview.md` with system diagram
5. FastAPI service prompt (with architecture diagram)
6. Ollama development service
7. PostgreSQL backing service
8. Production vs. Development instructions
9. Environment configuration instructions

**Medium Priority:**
6. Redis backing service
7. Static site generator service
8. Node.js worker service
9. Testing standards instructions
10. MCP server integration

**Low Priority:**
11. RabbitMQ, OpenSearch, MinIO backing services
12. Context7 instructions
13. Health checks instructions
14. Meta documentation tasks

---

## 🤝 Contributing

When adding new items to this TODO:
1. Specify priority (High/Medium/Low)
2. Include best practices section
3. Link to relevant documentation
4. Add acceptance criteria (checklist)
5. Estimate complexity if possible

When completing items:
1. Update status to "In Progress" → "Done"
2. Link to PR/commit
3. Update related documentation
4. Test with fresh workspace scaffold
