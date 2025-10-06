---
applyTo: "docker-compose*.yml"
---

# Docker Compose Guardrails

## Marker System

Insert services only inside designated markers:

```yaml
##<<BACKING_SERVICES>>
  postgres:
    image: postgres:16
##</BACKING_SERVICES>>

##<<SERVICES>>
  api:
    image: my-api
##</SERVICES>>

##<<DEVELOPMENT_SERVICES>>  # Only in docker-compose.development.yml
  ollama:
    image: ollama/ollama
##</DEVELOPMENT_SERVICES>>
```

Rules:
- Never remove or rename marker lines
- Always add blank lines between services

## Environment Configuration

Define global anchor at top of both compose files:
```yaml
x-env: &default-env
  env_file: [./config/.env]
```

Every service must include:
```yaml
services:
  my-service:
    <<: *default-env  # Place immediately after service name
    image: my-image
```

## Production vs Development

**docker-compose.yml** (production):
- ✅ Base configuration
- ❌ No volume mounts for code
- ❌ No debug ports
- ❌ No dev environment variables

**docker-compose.development.yml** (development overrides):
- ✅ Volume mounts (e.g., `.:/workspace`)
- ✅ Debug ports and settings
- ✅ Hot-reload configurations
- ✅ Development-only services

## Checklist

- [ ] Service in correct marker region
- [ ] Blank lines separate services
- [ ] `<<: *default-env` included
- [ ] Production config in `docker-compose.yml`
- [ ] Dev-specific config in `docker-compose.development.yml`
