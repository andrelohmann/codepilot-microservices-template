---
description: "Scaffold a FastAPI REST API service"
mode: agent
tools: [file, workspace, terminal]
---

# Scaffold FastAPI Service

Create a production-ready `${input:serviceName}` service using FastAPI with complete project structure, database integration, and Docker configuration.

## Reference Documentation

- **[FastAPI Tech-Stack Guide](../../../../docs/tech-stacks/apis/fastapi.md)** - Complete implementation details and examples
- **[FastAPI Instructions](../instructions/service-api-fastapi.instructions.md)** - Copilot coding guidance

## Service Configuration

Ask the user for:
- **Service Name**: `${input:serviceName}` (e.g., `api-fastapi-users`, `api-fastapi-orders`)
- **Purpose**: `${input:servicePurpose}` (brief description)
- **Port**: `${input:port:8000}` (default: 8000)
- **Database**: `${input:database:PostgreSQL}` (PostgreSQL, None)
- **Authentication**: `${input:auth:JWT}` (JWT, API Key, None)

## Project Structure

```
services/${input:serviceName}/
├── app/
│   ├── main.py              # FastAPI app
│   ├── config.py            # Settings
│   ├── api/v1/              # API routes
│   │   ├── router.py
│   │   └── endpoints/
│   ├── core/                # Security, logging
│   ├── models/              # SQLAlchemy models
│   ├── schemas/             # Pydantic schemas
│   ├── services/            # Business logic
│   └── db/                  # Database setup
├── tests/
├── alembic/                 # Migrations
├── Dockerfile
├── requirements.txt
└── README.md
```

## Implementation Steps

1. **Create Directory Structure**
   - Generate folder hierarchy in `services/`
   - Create all `__init__.py` files

2. **Core Application Files**
   - `app/main.py` - FastAPI app with CORS, routers, exception handlers
   - `app/config.py` - Pydantic Settings for environment variables
   - `app/dependencies.py` - Database session dependency
   - Refer to [tech-stack guide](../../../../docs/tech-stacks/apis/fastapi.md#quick-start) for templates

3. **Database Setup** (if ${input:database} != None)
   - `app/db/base.py` - SQLAlchemy Base
   - `app/db/session.py` - Database session factory
   - `app/models/*.py` - Example User model
   - `alembic/` - Initialize migrations: `alembic init alembic`

4. **API Layer**
   - `app/api/v1/endpoints/health.py` - Health check endpoint
   - `app/api/v1/endpoints/*.py` - Domain endpoints (users, items, etc.)
   - `app/api/v1/router.py` - Aggregate all routers
   - Include proper request/response models (Pydantic schemas)

5. **Schemas (Pydantic Models)**
   - `app/schemas/*.py` - Request/response models with validation
   - Example: UserCreate, UserResponse, UserUpdate

6. **Business Logic**
   - `app/services/*.py` - Service layer for business logic
   - Keep routers thin, logic in services

7. **Authentication** (if ${input:auth} != None)
   - `app/core/security.py` - JWT tokens, password hashing
   - `app/api/v1/endpoints/auth.py` - Login/register endpoints
   - Refer to [auth examples](../../../../docs/tech-stacks/apis/fastapi.md#authentication)

8. **Testing Setup**
   - `tests/conftest.py` - Pytest fixtures (test DB, test client)
   - `tests/test_*.py` - Endpoint tests
   - Use TestClient from `fastapi.testclient`

9. **Docker Configuration**
   - `Dockerfile` - Multi-stage build (FastAPI + dependencies)
   - `.dockerignore` - Exclude unnecessary files
   - Health check endpoint integration

10. **Dependencies**
    - `requirements.txt` - FastAPI, SQLAlchemy, Alembic, pytest, etc.
    - Pin versions for reproducibility

11. **Documentation**
    - `README.md` - Service overview, setup instructions, API endpoints
    - OpenAPI docs auto-generated at `/docs`

12. **Docker Compose Integration**
    - Update `docker-compose.yml` in project root
    - Add service with ports, environment, depends_on (database)
    - Add to appropriate network

13. **Development Configuration**
    - `.devcontainer/` - VS Code devcontainer configuration
    - Environment variables template (`.env.example`)

14. **Verify Service**
    - Start service: `docker compose up ${input:serviceName}`
    - Test health endpoint: `curl http://localhost:${input:port}/health`
    - Check OpenAPI docs: `http://localhost:${input:port}/docs`

## Code Templates

All code templates are available in the [FastAPI Tech-Stack Documentation](../../../../docs/tech-stacks/apis/fastapi.md):
- FastAPI app setup
- Pydantic Settings
- SQLAlchemy models
- API routers with dependencies
- Authentication middleware
- Database migrations
- Testing patterns

## Completion Checklist

- [ ] Directory structure created
- [ ] FastAPI app configured with routers
- [ ] Database models and migrations set up
- [ ] Pydantic schemas defined
- [ ] API endpoints implemented
- [ ] Authentication configured (if required)
- [ ] Tests written and passing
- [ ] Dockerfile builds successfully
- [ ] docker-compose.yml updated
- [ ] README.md documentation complete
- [ ] Service accessible at `http://localhost:${input:port}/docs`
- [ ] Health check endpoint returns 200 OK

## Post-Scaffolding

After scaffolding completes:
1. Run migrations: `docker compose exec ${input:serviceName} alembic upgrade head`
2. Run tests: `docker compose exec ${input:serviceName} pytest`
3. Review OpenAPI documentation at `/docs`
4. Refer to [instructions file](../instructions/service-api-fastapi.instructions.md) for Copilot coding guidance

---

**Note**: This prompt creates the scaffolding only. Refer to the [complete tech-stack documentation](../../../../docs/tech-stacks/apis/fastapi.md) for detailed implementation patterns, best practices, and code examples.
