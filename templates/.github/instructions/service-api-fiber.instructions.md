---
applyTo:
  - "services/api-fiber-*/**"
---

# Fiber API Service Instructions

Apply to Go-based REST APIs using the Fiber framework.

## Technology Stack

- **Framework:** Fiber v2 (Express-inspired)
- **Language:** Go 1.21+
- **Validation:** go-playground/validator
- **Database:** PostgreSQL via pgx/sqlx or GORM
- **Logging:** zerolog
- **Configuration:** viper or godotenv

## Project Structure

```
services/api-fiber-{purpose}/
├── cmd/server/main.go
├── internal/
│   ├── config/
│   ├── handlers/
│   ├── middleware/
│   ├── models/
│   ├── repository/
│   └── services/
├── pkg/
├── go.mod
├── Dockerfile
└── README.md
```

## Core Patterns

**Route Definition:**
- Group routes by feature domain
- Use `app.Group()` for versioning (`/api/v1`)
- Separate handler registration from business logic
- Example: `v1.Get("/users", handlers.ListUsers)`

**Middleware Stack:**
- Logger middleware for request/response logging
- Recover middleware for panic recovery
- CORS middleware with explicit origins
- Auth middleware using JWT validation
- Rate limiting per route or globally

**Error Handling:**
- Return `fiber.Error` with appropriate status codes
- Use custom error types for domain errors
- Format: `{"error": "message", "code": "ERROR_CODE"}`
- Log errors with context using zerolog

**Request Validation:**
- Define structs with validation tags
- Use `c.BodyParser()` + `validator.Validate()`
- Return 400 Bad Request with field-specific errors
- Validate path/query params explicitly

**Database Access:**
- Repository pattern with interfaces
- Use contexts for cancellation (`context.Context`)
- Prepare statements for repeated queries
- Handle `sql.ErrNoRows` explicitly
- Connection pooling configured in `database.Config`

**Configuration:**
- Environment variables with `.env` file
- Struct-based config loaded at startup
- Required: `PORT`, `DATABASE_URL`, `JWT_SECRET`
- Optional: `LOG_LEVEL`, `CORS_ORIGINS`, `RATE_LIMIT`

**Response Formats:**
- Success: `{"data": {...}, "meta": {...}}`
- Error: `{"error": "message", "details": [...]}`
- Pagination: `{"data": [...], "meta": {"page": 1, "total": 100}}`
- Empty lists return `[]` not null

## Testing Standards

- Unit tests in `_test.go` files alongside implementation
- Integration tests in `tests/` directory
- Use `httptest` for handler testing
- Mock interfaces for repository tests
- Table-driven tests for multiple scenarios

## Performance Optimizations

- Enable Fiber's prefork mode in production
- Use connection pooling (min 5, max 25 connections)
- Implement response compression
- Cache frequently accessed data in memory
- Use zero-allocation routing where possible

## Security Requirements

- Helmet-style security headers via middleware
- JWT tokens with expiration and refresh
- SQL injection prevention via parameterized queries
- Input sanitization for all user inputs
- HTTPS only in production (enforce via middleware)

## Health Checks

- `/health` endpoint returning `{"status": "ok"}` (200)
- `/ready` endpoint checking database connectivity
- Include version and uptime in health response
- Docker HEALTHCHECK using `/health`

## Anti-Patterns

❌ Using `interface{}` without type assertions  
❌ Ignoring error returns (`_, _ = ...`)  
❌ Global state beyond config and logger  
❌ Direct database access from handlers  
❌ Forgetting to close resources (defer `Close()`)  
❌ Returning HTML instead of JSON for APIs  
❌ Not using contexts for cancellation  

## Dependencies

Required:
- `github.com/gofiber/fiber/v2`
- `github.com/go-playground/validator/v10`
- `github.com/rs/zerolog`
- `github.com/jackc/pgx/v5` or `gorm.io/gorm`

Optional:
- `github.com/golang-jwt/jwt/v5` (auth)
- `github.com/gofiber/helmet/v2` (security)
- `github.com/gofiber/limiter/v2` (rate limiting)
