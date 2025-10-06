---
applyTo: "docker-compose*.yml"
---

# Docker Compose Guardrails

When modifying `docker-compose.yml` or `docker-compose.development.yml`, follow these rules:

## 1. Marker System

Only insert new service blocks inside the designated marker regions:

**Backing Services** (databases, caches, queues):
```yaml
##<<BACKING_SERVICES>>

  service-name:
    ...

##</BACKING_SERVICES>>
```

**Application Services** (APIs, frontends, workers):
```yaml
##<<SERVICES>>

  service-name:
    ...

##</SERVICES>>
```

**Development Services** (only in `docker-compose.development.yml`):
```yaml
##<<DEVELOPMENT_SERVICES>>

  service-name:
    ...

##</DEVELOPMENT_SERVICES>>
```

- Do **not** remove or rename marker lines themselves.
- Do **not** reorder existing service entries outside the marker scope.

## 2. Service Separation

- **Always** add blank lines (whitespace) above and below each service definition to improve readability:

```yaml
##<<SERVICES>>

  api:
    image: my-api
    ports:
      - "8000:8000"

  frontend:
    image: my-frontend
    ports:
      - "3000:3000"

##</SERVICES>>
```

## 3. Environment Configuration

### Global Environment File Reference

**Always** define the global environment file anchor at the top of both compose files:

```yaml
x-env: &default-env
  env_file: [./config/.env]
```

### Service-Level Environment Injection

**Every service** must include the `default-env` anchor to load environment variables:

```yaml
services:
  my-service:
    <<: *default-env
    image: my-image
    ports:
      - "8080:8080"
```

Place the `<<: *default-env` line **immediately after the service name**, before other configurations.

## 4. Environment Separation

### docker-compose.yml
- Contains **production-ready** base configurations
- **Never** include development-specific settings:
  - ❌ Volume mounts for live code editing
  - ❌ Debug ports or debug mode flags
  - ❌ Development-only environment variables
  - ❌ Hot-reload configurations

### docker-compose.development.yml
- Contains **all development-specific** configurations and overrides
- **Always** place here:
  - ✅ Volume mounts for source code (e.g., `.:/workspace`)
  - ✅ Debug ports and debug mode settings
  - ✅ Development environment variables
  - ✅ Development-only services (Ollama, mocks, test databases)
  - ✅ Hot-reload and watch configurations

**Example separation:**

`docker-compose.yml` (production base):
```yaml
services:
  api:
    <<: *default-env
    build: ./services/api
    ports:
      - "8000:8000"
    restart: unless-stopped
```

`docker-compose.development.yml` (development overrides):
```yaml
services:
  api:
    volumes:
      - .:/workspace
    environment:
      - DEBUG=true
    ports:
      - "5678:5678"  # debugger port
```

## 5. Service Placement Rules

- **Backing services** (databases, Redis, message queues) → `##<<BACKING_SERVICES>>` in both files
- **Application services** (your microservices) → `##<<SERVICES>>` in both files
- **Development tools** (Ollama, mocks, test services) → `##<<DEVELOPMENT_SERVICES>>` in `docker-compose.development.yml` **only**

## Summary Checklist

When adding or modifying services:

- [ ] Service is placed within correct marker region
- [ ] Blank lines separate service from others
- [ ] `<<: *default-env` is included in service definition
- [ ] Production config is in `docker-compose.yml`
- [ ] Development-specific config is in `docker-compose.development.yml`
- [ ] Markers remain intact and unchanged