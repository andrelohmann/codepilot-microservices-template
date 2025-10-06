---
applyTo: "**/Dockerfile,**/docker-compose*.yml,**/.devcontainer/*/devcontainer.json"
description: Production vs development configuration patterns
---

# Production vs Development

Separate configurations for prod and dev environments.

## Core Principles

- **Dockerfiles are ALWAYS production-ready** (no dev stages, no dev dependencies)
- **Dev dependencies injected via .devcontainer/SERVICE_NAME/devcontainer.json** for each service
- Production: `docker-compose.yml` with production config
- Development: `docker-compose.development.yml` for local dev environment

## Dockerfile (Production-Ready)

```dockerfile
FROM python:3.11-slim

# Install production dependencies only
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . /app
WORKDIR /app

# Production user (uid 1001)
RUN useradd -u 1001 -m appuser
USER appuser

# Health check and command
HEALTHCHECK CMD curl -f http://localhost:8000/health
CMD ["uvicorn", "main:app", "--host", "0.0.0.0"]
```

## Development Container

Dev dependencies in `.devcontainer/api/devcontainer.json`:
```json
{
  "name": "API Service",
  "dockerComposeFile": "../../docker-compose.development.yml",
  "service": "api",
  "postCreateCommand": "pip install pytest ruff black mypy debugpy",
  "customizations": {
    "vscode": {
      "extensions": ["ms-python.python", "ms-python.debugpy"]
    }
  }
}
```

Location: `.devcontainer/SERVICE_NAME/devcontainer.json` (e.g., `.devcontainer/api/`, `.devcontainer/worker/`)

## Production Compose

- Named volumes (no bind mounts)
- Health checks, resource limits, restart: `unless-stopped`
- No exposed ports (except via reverse proxy)
- Environment: `ENVIRONMENT=production`, `LOG_LEVEL=info`

## Development Compose

- Bind mount source code for hot reload
- All ports exposed, dev services (pgAdmin, Ollama)
- Environment: `ENVIRONMENT=development`, `LOG_LEVEL=debug`
- Reload: `--reload` (uvicorn), `--watch` (nodemon)

## Anti-Patterns

- ❌ Dev dependencies in Dockerfile
- ❌ Multi-stage Dockerfile with dev target
- ❌ Bind mounts in production
- ❌ Exposed management UIs in production
