# Scaffold FastAPI Service

**Objective:** Create a production-ready FastAPI service with complete directory structure, Docker configuration, testing setup, and comprehensive documentation.

---

## 📋 Service Specification

Before running this prompt, gather the following information:

1. **Service Name:** `[e.g., api, user-service, payment-service]`
2. **Service Purpose:** `[Brief description of what this service does]`
3. **Database Required:** `[Yes/No - PostgreSQL]`
4. **Cache Required:** `[Yes/No - Redis]`
5. **Message Queue Required:** `[Yes/No - RabbitMQ]`
6. **External APIs:** `[List any external services to integrate]`
7. **Authentication:** `[JWT, OAuth, API Key, None]`
8. **Port:** `[Default: 8000, or specify custom]`

---

## 🏗️ Architecture Overview

```
┌────────────────────────────────────────────────────────────────────┐
│                      FastAPI Service Architecture                  │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                        API Layer                             │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │  Routers   │  │Middleware  │  │Dependencies│            │ │
│  │  │ (Endpoints)│  │  (Auth,    │  │  (DB, etc) │            │ │
│  │  └──────┬─────┘  │  Logging)  │  └──────┬─────┘            │ │
│  │         │        └──────┬─────┘         │                  │ │
│  └─────────┼───────────────┼───────────────┼──────────────────┘ │
│            │               │               │                    │
│            ↓               ↓               ↓                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                      Service Layer                           │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │  Business  │  │Validation  │  │  External  │            │ │
│  │  │   Logic    │  │  (Pydantic)│  │  API Calls │            │ │
│  │  └──────┬─────┘  └────────────┘  └────────────┘            │ │
│  └─────────┼────────────────────────────────────────────────────┘ │
│            │                                                      │
│            ↓                                                      │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                    Repository Layer                          │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │  Database  │  │   Cache    │  │   Queue    │            │ │
│  │  │  (SQLAlch.)│  │  (Redis)   │  │ (RabbitMQ) │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
└────────────┼────────────────┼────────────────┼────────────────────┘
             │                │                │
             ↓                ↓                ↓
    ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
    │ PostgreSQL  │  │    Redis    │  │  RabbitMQ   │
    │  (Backing   │  │  (Backing   │  │  (Backing   │
    │   Service)  │  │   Service)  │  │   Service)  │
    └─────────────┘  └─────────────┘  └─────────────┘
```

---

## 📁 Directory Structure

Create the following complete directory structure:

```
services/[service-name]/
├── app/
│   ├── __init__.py
│   ├── main.py                    ← FastAPI application entry point
│   ├── config.py                  ← Configuration (Pydantic settings)
│   ├── dependencies.py            ← Dependency injection
│   │
│   ├── api/                       ← API layer
│   │   ├── __init__.py
│   │   ├── deps.py                ← API dependencies
│   │   └── v1/                    ← API version 1
│   │       ├── __init__.py
│   │       ├── router.py          ← Main router aggregator
│   │       └── endpoints/         ← Individual endpoint routers
│   │           ├── __init__.py
│   │           ├── health.py      ← Health check endpoint
│   │           ├── users.py       ← Example: User endpoints
│   │           └── auth.py        ← Example: Auth endpoints
│   │
│   ├── core/                      ← Core utilities
│   │   ├── __init__.py
│   │   ├── security.py            ← Auth, JWT, password hashing
│   │   ├── logging.py             ← Structured logging
│   │   └── exceptions.py          ← Custom exception handlers
│   │
│   ├── models/                    ← Database models (SQLAlchemy)
│   │   ├── __init__.py
│   │   ├── base.py                ← Base model class
│   │   └── user.py                ← Example: User model
│   │
│   ├── schemas/                   ← Pydantic schemas (request/response)
│   │   ├── __init__.py
│   │   ├── user.py                ← Example: User schemas
│   │   └── auth.py                ← Example: Auth schemas
│   │
│   ├── services/                  ← Business logic layer
│   │   ├── __init__.py
│   │   ├── user_service.py        ← Example: User service
│   │   └── auth_service.py        ← Example: Auth service
│   │
│   ├── repositories/              ← Data access layer
│   │   ├── __init__.py
│   │   ├── base.py                ← Base repository
│   │   └── user_repository.py     ← Example: User repository
│   │
│   ├── database.py                ← Database connection & session
│   ├── cache.py                   ← Redis cache client
│   └── queue.py                   ← RabbitMQ queue client
│
├── tests/
│   ├── __init__.py
│   ├── conftest.py                ← Pytest fixtures
│   ├── .env.test                  ← Test environment config
│   ├── unit/                      ← Unit tests
│   │   ├── __init__.py
│   │   ├── test_services.py
│   │   └── test_models.py
│   ├── integration/               ← Integration tests
│   │   ├── __init__.py
│   │   ├── test_database.py
│   │   └── test_api.py
│   └── e2e/                       ← End-to-end tests
│       ├── __init__.py
│       └── test_user_flows.py
│
├── alembic/                       ← Database migrations
│   ├── versions/
│   ├── env.py
│   └── script.py.mako
│
├── docs/
│   └── api-documentation.md       ← Service-specific API docs
│
├── Dockerfile                     ← Multi-stage Docker build
├── .dockerignore
├── requirements.txt               ← Production dependencies
├── requirements-dev.txt           ← Development dependencies
├── pyproject.toml                 ← Project metadata
├── pytest.ini                     ← Pytest configuration
├── .coveragerc                    ← Coverage configuration
├── alembic.ini                    ← Alembic configuration
├── .env.example                   ← Environment variables template
├── .gitignore
└── README.md                      ← Service README
```

---

## 🐍 Core Files Implementation

### 1. `app/main.py` - FastAPI Application Entry Point

```python
"""
FastAPI application entry point.
"""
from contextlib import asynccontextmanager
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from fastapi.responses import JSONResponse

from app.config import settings
from app.core.logging import setup_logging
from app.core.exceptions import register_exception_handlers
from app.database import engine, Base
from app.api.v1.router import api_router

# Setup logging
logger = setup_logging()


@asynccontextmanager
async def lifespan(app: FastAPI):
    """
    Application lifespan events.
    Startup and shutdown logic.
    """
    # Startup
    logger.info("Starting application...")
    logger.info(f"Environment: {settings.environment}")
    logger.info(f"Database: {settings.postgres_host}:{settings.postgres_port}")
    
    # Create database tables (development only - use Alembic in production)
    if settings.is_development:
        async with engine.begin() as conn:
            await conn.run_sync(Base.metadata.create_all)
    
    yield
    
    # Shutdown
    logger.info("Shutting down application...")
    await engine.dispose()


# Create FastAPI application
app = FastAPI(
    title=settings.project_name,
    version=settings.version,
    description=settings.description,
    debug=settings.debug_mode,
    lifespan=lifespan,
)

# CORS middleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.cors_origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Register exception handlers
register_exception_handlers(app)

# Include API router
app.include_router(api_router, prefix="/api/v1")


@app.get("/", tags=["Root"])
async def root():
    """Root endpoint."""
    return {
        "service": settings.project_name,
        "version": settings.version,
        "environment": settings.environment,
    }
```

### 2. `app/config.py` - Configuration with Pydantic

```python
"""
Application configuration.
All configuration via environment variables.
"""
from pydantic import Field, PostgresDsn, validator
from pydantic_settings import BaseSettings
from typing import List


class Settings(BaseSettings):
    """Application settings."""
    
    # ────────────────────────────────────────────
    # Application
    # ────────────────────────────────────────────
    project_name: str = "[Service Name]"
    version: str = "1.0.0"
    description: str = "[Service Description]"
    environment: str = Field(
        ...,
        pattern="^(development|staging|production)$"
    )
    log_level: str = Field(default="info", pattern="^(debug|info|warning|error)$")
    debug_mode: bool = False
    
    # ────────────────────────────────────────────
    # API
    # ────────────────────────────────────────────
    api_host: str = "0.0.0.0"
    api_port: int = Field(default=8000, ge=1024, le=65535)
    cors_origins: List[str] = []
    
    @validator("cors_origins", pre=True)
    def parse_cors_origins(cls, v):
        if isinstance(v, str):
            return [origin.strip() for origin in v.split(",") if origin.strip()]
        return v
    
    # ────────────────────────────────────────────
    # Database (PostgreSQL)
    # ────────────────────────────────────────────
    postgres_host: str
    postgres_port: int = 5432
    postgres_db: str
    postgres_user: str
    postgres_password: str = Field(..., min_length=8)
    postgres_pool_size: int = Field(default=10, ge=1)
    postgres_max_overflow: int = Field(default=20, ge=0)
    
    @property
    def postgres_url(self) -> str:
        return (
            f"postgresql+asyncpg://{self.postgres_user}:{self.postgres_password}"
            f"@{self.postgres_host}:{self.postgres_port}/{self.postgres_db}"
        )
    
    # ────────────────────────────────────────────
    # Redis (Cache)
    # ────────────────────────────────────────────
    redis_host: str
    redis_port: int = 6379
    redis_password: str | None = None
    redis_db: int = Field(default=0, ge=0, le=15)
    
    @property
    def redis_url(self) -> str:
        auth = f":{self.redis_password}@" if self.redis_password else ""
        return f"redis://{auth}{self.redis_host}:{self.redis_port}/{self.redis_db}"
    
    # ────────────────────────────────────────────
    # Security (JWT)
    # ────────────────────────────────────────────
    secret_key: str = Field(..., min_length=32)
    access_token_expire_minutes: int = Field(default=30, ge=1)
    refresh_token_expire_days: int = Field(default=7, ge=1)
    
    # ────────────────────────────────────────────
    # Computed Properties
    # ────────────────────────────────────────────
    @property
    def is_production(self) -> bool:
        return self.environment == "production"
    
    @property
    def is_development(self) -> bool:
        return self.environment == "development"
    
    # ────────────────────────────────────────────
    # Pydantic Config
    # ────────────────────────────────────────────
    model_config = {
        "env_file": "./config/.env",
        "env_file_encoding": "utf-8",
        "case_sensitive": False,
        "extra": "ignore",
    }


# Singleton instance
settings = Settings()

# Validate production settings
if settings.is_production and settings.debug_mode:
    raise ValueError("DEBUG_MODE must be False in production")
```

### 3. `app/database.py` - Database Connection

```python
"""
Database connection and session management.
"""
from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine, async_sessionmaker
from sqlalchemy.orm import DeclarativeBase

from app.config import settings

# Create async engine
engine = create_async_engine(
    settings.postgres_url,
    echo=settings.debug_mode,
    pool_size=settings.postgres_pool_size,
    max_overflow=settings.postgres_max_overflow,
    pool_pre_ping=True,
)

# Create async session factory
AsyncSessionLocal = async_sessionmaker(
    engine,
    class_=AsyncSession,
    expire_on_commit=False,
    autocommit=False,
    autoflush=False,
)


class Base(DeclarativeBase):
    """Base class for all models."""
    pass


async def get_db() -> AsyncSession:
    """
    Dependency for database sessions.
    Yields a database session and ensures cleanup.
    """
    async with AsyncSessionLocal() as session:
        try:
            yield session
        finally:
            await session.close()
```

### 4. `app/api/v1/endpoints/health.py` - Health Check

```python
"""
Health check endpoint.
"""
from fastapi import APIRouter, Depends
from sqlalchemy import text
from sqlalchemy.ext.asyncio import AsyncSession

from app.database import get_db
from app.cache import get_redis_client

router = APIRouter()


@router.get("/health", tags=["Health"])
async def health_check(db: AsyncSession = Depends(get_db)):
    """
    Health check endpoint.
    Verifies database and cache connectivity.
    """
    try:
        # Check database
        await db.execute(text("SELECT 1"))
        db_status = "healthy"
    except Exception as e:
        db_status = f"unhealthy: {str(e)}"
    
    try:
        # Check Redis
        redis = await get_redis_client()
        await redis.ping()
        cache_status = "healthy"
    except Exception as e:
        cache_status = f"unhealthy: {str(e)}"
    
    return {
        "status": "healthy" if db_status == "healthy" and cache_status == "healthy" else "degraded",
        "database": db_status,
        "cache": cache_status,
    }
```

### 5. `Dockerfile` - Multi-Stage Build

```dockerfile
# ============================================
# Base Stage
# ============================================
FROM python:3.11-slim AS base

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    curl \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# Install production dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt


# ============================================
# Development Stage
# ============================================
FROM base AS development

# Install development dependencies
COPY requirements-dev.txt .
RUN pip install --no-cache-dir -r requirements-dev.txt

# Create non-root user
RUN useradd -m -u 1000 devuser && chown -R devuser:devuser /app
USER devuser

# Source code mounted via volume
EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--reload"]


# ============================================
# Production Stage
# ============================================
FROM base AS production

# Copy application code
COPY ./app /app/app
COPY ./alembic /app/alembic
COPY ./alembic.ini /app/alembic.ini

# Create non-root user
RUN useradd -m -u 1001 appuser && chown -R appuser:appuser /app
USER appuser

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=40s --retries=3 \
  CMD curl -f http://localhost:8000/api/v1/health || exit 1

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "4"]
```

### 6. `.devcontainer/[service-name]/devcontainer.json` - VS Code Dev Container

Location: Project root `.devcontainer/[service-name]/devcontainer.json`

```json
{
  "name": "[Service Name] Development",
  "dockerComposeFile": [
    "../../docker-compose.development.yml"
  ],
  "service": "[service-name]",
  "workspaceFolder": "/app",
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-python.python",
        "ms-python.vscode-pylance",
        "ms-python.black-formatter",
        "charliermarsh.ruff",
        "ms-azuretools.vscode-docker",
        "tamasfe.even-better-toml"
      ],
      "settings": {
        "python.defaultInterpreterPath": "/usr/local/bin/python",
        "python.linting.enabled": true,
        "python.linting.ruffEnabled": true,
        "python.formatting.provider": "black",
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
          "source.organizeImports": "explicit"
        }
      }
    }
  },
  "postCreateCommand": "pip install -r requirements-dev.txt && alembic upgrade head",
  "remoteUser": "devuser"
}
```

### 7. `requirements.txt` - Production Dependencies

```txt
# FastAPI
fastapi==0.104.1
uvicorn[standard]==0.24.0

# Database
sqlalchemy[asyncio]==2.0.23
asyncpg==0.29.0
alembic==1.12.1

# Redis
redis[hiredis]==5.0.1

# RabbitMQ (optional)
aio-pika==9.3.1

# Validation
pydantic==2.5.0
pydantic-settings==2.1.0
email-validator==2.1.0

# Security
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
python-multipart==0.0.6

# HTTP Client
httpx==0.25.2

# Utilities
python-dotenv==1.0.0
```

### 8. `requirements-dev.txt` - Development Dependencies

```txt
# Install production dependencies
-r requirements.txt

# Testing
pytest==7.4.3
pytest-cov==4.1.0
pytest-asyncio==0.21.1
pytest-mock==3.12.0
faker==20.1.0
factory-boy==3.3.0
testcontainers==3.7.1

# Code Quality
ruff==0.1.6
black==23.11.0
mypy==1.7.1
types-redis==4.6.0.11

# Debugging
ipdb==0.13.13
debugpy==1.8.0
```

### 9. `.env.example` - Environment Variables Template

```bash
# ============================================
# [Service Name] Configuration Template
# Copy to config/.env and fill with actual values
# ============================================

# Application
ENVIRONMENT=development
LOG_LEVEL=debug
DEBUG_MODE=true
PROJECT_NAME=[Service Name]
VERSION=1.0.0
DESCRIPTION=[Service Description]

# API
API_HOST=0.0.0.0
API_PORT=8000
CORS_ORIGINS=http://localhost:3000

# Database (PostgreSQL)
POSTGRES_HOST=postgres
POSTGRES_PORT=5432
POSTGRES_DB=[service_name]_db
POSTGRES_USER=postgres
POSTGRES_PASSWORD=changeme
POSTGRES_POOL_SIZE=10
POSTGRES_MAX_OVERFLOW=20

# Redis (Cache)
REDIS_HOST=redis
REDIS_PORT=6379
REDIS_PASSWORD=changeme
REDIS_DB=0

# Security (JWT)
SECRET_KEY=changeme_at_least_32_characters_long
ACCESS_TOKEN_EXPIRE_MINUTES=30
REFRESH_TOKEN_EXPIRE_DAYS=7
```

### 10. `pytest.ini` - Pytest Configuration

```ini
[pytest]
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
addopts = 
    --verbose
    --strict-markers
    --cov=app
    --cov-report=term-missing
    --cov-report=html
    --cov-report=xml
    --cov-fail-under=80
    -ra
markers =
    unit: Unit tests
    integration: Integration tests
    e2e: End-to-end tests
    slow: Slow tests
asyncio_mode = auto
```

---

## 📊 Docker Compose Integration

### Add to `docker-compose.yml` (Production)

```yaml
##<<SERVICES>>
[service-name]:
  build:
    context: ./services/[service-name]
    dockerfile: Dockerfile
    target: production
  image: myapp/[service-name]:latest
  container_name: [service-name]
  restart: unless-stopped
  environment:
    - ENVIRONMENT=production
    - LOG_LEVEL=info
  env_file:
    - ./config/.env
  networks:
    - app-network
  depends_on:
    - postgres
    - redis
  healthcheck:
    test: ["CMD", "curl", "-f", "http://localhost:8000/api/v1/health"]
    interval: 30s
    timeout: 10s
    retries: 3
    start_period: 40s
  deploy:
    resources:
      limits:
        cpus: '1.0'
        memory: 1G
      reservations:
        cpus: '0.5'
        memory: 512M
##<<SERVICES>>
```

### Add to `docker-compose.development.yml` (Development)

```yaml
##<<SERVICES>>
[service-name]:
  build:
    context: ./services/[service-name]
    dockerfile: Dockerfile
    target: development
  image: myapp/[service-name]:dev
  container_name: [service-name]-dev
  environment:
    - ENVIRONMENT=development
    - LOG_LEVEL=debug
    - PYTHONUNBUFFERED=1
  env_file:
    - ./config/.env
  volumes:
    - ./services/[service-name]:/app
  ports:
    - "[port]:8000"
  networks:
    - app-network
  depends_on:
    - postgres
    - redis
  command: uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
##<<SERVICES>>
```

---

## 📝 Documentation Requirements

### 1. Service README (`services/[service-name]/README.md`)

```markdown
# [Service Name]

[Brief description of what this service does]

## Features

- Feature 1
- Feature 2
- Feature 3

## Architecture

[ASCII art diagram showing service components]

## API Endpoints

See [API Documentation](./docs/api-documentation.md) for detailed endpoint descriptions.

### Summary

- `GET /api/v1/health` - Health check
- `POST /api/v1/users` - Create user
- `GET /api/v1/users/{id}` - Get user by ID
- `POST /api/v1/auth/login` - User login

## Development

### Setup

1. Copy environment variables:
   ```bash
   cp config/.env.example config/.env
   ```

2. Start development environment:
   ```bash
   docker-compose -f docker-compose.development.yml up -d
   ```

3. Run database migrations:
   ```bash
   docker exec [service-name]-dev alembic upgrade head
   ```

### Running Tests

```bash
# All tests
docker exec [service-name]-dev pytest

# Unit tests only
docker exec [service-name]-dev pytest tests/unit

# With coverage
docker exec [service-name]-dev pytest --cov=app
```

## Deployment

See [Deployment Guide](../../docs/operations/deployment.md)

## Configuration

All configuration via environment variables. See `.env.example` for required variables.

## License

[License information]
```

### 2. API Documentation (`services/[service-name]/docs/api-documentation.md`)

```markdown
# [Service Name] API Documentation

## Base URL

- Development: `http://localhost:[port]/api/v1`
- Production: `https://api.example.com/v1`

## Authentication

All protected endpoints require JWT token:

```
Authorization: Bearer <access_token>
```

## Endpoints

### Health Check

**GET** `/health`

Returns service health status.

**Response (200 OK):**
```json
{
  "status": "healthy",
  "database": "healthy",
  "cache": "healthy"
}
```

[Additional endpoint documentation...]
```

---

## ✅ Implementation Checklist

Use this checklist to verify completeness:

### Project Structure
- [ ] All directories created according to structure
- [ ] `__init__.py` files in all Python packages
- [ ] `.gitkeep` files in empty directories

### Core Implementation
- [ ] `app/main.py` with FastAPI application
- [ ] `app/config.py` with Pydantic settings
- [ ] `app/database.py` with async SQLAlchemy
- [ ] `app/cache.py` with Redis client
- [ ] Health check endpoint implemented
- [ ] Exception handlers registered
- [ ] CORS middleware configured

### Security
- [ ] JWT authentication implemented
- [ ] Password hashing configured
- [ ] Secrets in `.env` (not hardcoded)
- [ ] Non-root user in Dockerfile

### Testing
- [ ] `tests/conftest.py` with fixtures
- [ ] Unit tests created
- [ ] Integration tests created
- [ ] Test coverage >80%
- [ ] `pytest.ini` configured
- [ ] `.coveragerc` configured

### Docker
- [ ] Production-ready Dockerfile (no dev stages)
- [ ] `.dockerignore` file
- [ ] Health check in Dockerfile
- [ ] Service added to `docker-compose.yml`
- [ ] Service added to `docker-compose.development.yml`
- [ ] `.devcontainer/[service-name]/devcontainer.json` created at project root

### Configuration
- [ ] `.env.example` with all variables
- [ ] `.env` added to `.gitignore`
- [ ] Environment validation in `config.py`
- [ ] Database connection URL constructed
- [ ] Redis connection URL constructed

### Dependencies
- [ ] `requirements.txt` with production deps
- [ ] `requirements-dev.txt` with dev deps
- [ ] `pyproject.toml` with project metadata
- [ ] Versions pinned for reproducibility

### Documentation
- [ ] Service README.md
- [ ] API documentation
- [ ] ASCII art architecture diagram
- [ ] Setup instructions
- [ ] Deployment instructions

### Database
- [ ] Alembic initialized
- [ ] Initial migration created
- [ ] Models inherit from Base
- [ ] Repositories implement CRUD operations

### Code Quality
- [ ] Ruff configured for linting
- [ ] Black configured for formatting
- [ ] Type hints on all functions
- [ ] Docstrings on all public functions

---

## 🚀 Next Steps

After scaffolding is complete:

1. **Update Configuration:** Fill `config/.env` with actual values
2. **Run Migrations:** `docker exec [service-name]-dev alembic upgrade head`
3. **Run Tests:** `docker exec [service-name]-dev pytest`
4. **Start Development:** Open in VS Code with Dev Container
5. **Implement Features:** Add business logic, endpoints, tests
6. **Document:** Update API documentation as you add endpoints
7. **Deploy:** Follow deployment procedures in `docs/operations/deployment.md`

---

## 📚 Related Documentation

- [Production vs Development Configuration](../instructions/production-vs-development.instructions.md)
- [Environment Configuration](../instructions/environment-configuration.instructions.md)
- [Testing Standards](../instructions/testing-standards.instructions.md)
- [Documentation Standards](../../docs/DOCUMENTATION_STANDARDS.md)
- [System Architecture Overview](../../docs/architecture/overview.md)

---

**Last Updated:** 2025-10-06
