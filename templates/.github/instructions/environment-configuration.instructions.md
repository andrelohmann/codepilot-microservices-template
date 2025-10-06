---
applyTo: "**/config.py,**/config.ts,**/.env.example"
description: Environment variable configuration standards
---

# Environment Configuration

All configuration via environment variables. No hardcoded values.

## Naming Convention

`SERVICE_CATEGORY_NAME` in UPPERCASE_SNAKE_CASE

Examples: `POSTGRES_HOST`, `API_SECRET_KEY`, `REDIS_MAX_CONNECTIONS`, `AWS_S3_BUCKET`

## File Structure

- `config/.env.example` - Committed template
- `config/.env` - Gitignored actual values
- `.gitignore` must exclude `*.env` except `!*.env.example`

## .env.example Format

```bash
# Application
ENVIRONMENT=development
LOG_LEVEL=debug

# Database
POSTGRES_PASSWORD=changeme  # ⚠️ CHANGE THIS
```

Mark secrets with `⚠️ CHANGE THIS`.

## Validation

Pydantic:
```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    postgres_password: str = Field(min_length=8)
    model_config = {"env_file": "./config/.env"}
```

Zod:
```typescript
const config = z.object({
  postgresPassword: z.string().min(8),
}).parse(process.env);
```

## Docker Integration

```yaml
services:
  api:
    env_file: [./config/.env]
```

## Anti-Patterns

- ❌ Hardcoded values
- ❌ Committing `.env` with secrets
- ❌ No validation
