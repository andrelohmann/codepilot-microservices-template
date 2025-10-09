---
description: "Scaffold a Fiber (Go) REST API service"
mode: agent
tools: [file, workspace, terminal]
---

# Scaffold Fiber Service

Create a production-ready `${input:serviceName}` service using Go Fiber framework with clean architecture and PostgreSQL integration.

## Reference Documentation

- **[Fiber Tech-Stack Guide](../../../../docs/tech-stacks/apis/fiber.md)** - Complete implementation details and examples
- **[Fiber Instructions](../instructions/service-api-fiber.instructions.md)** - Copilot coding guidance

## Service Configuration

Ask the user for:
- **Service Name**: `${input:serviceName}` (e.g., `api-fiber-orders`, `api-fiber-catalog`)
- **Purpose**: `${input:servicePurpose}` (brief description)
- **Port**: `${input:port:8080}` (default: 8080)
- **Database**: `${input:database:PostgreSQL}` (PostgreSQL, None)
- **Authentication**: `${input:auth:JWT}` (JWT, API Key, None)

## Project Structure

```
services/${input:serviceName}/
├── cmd/api/main.go          # Application entry
├── internal/
│   ├── config/              # Configuration
│   ├── handlers/            # HTTP handlers
│   ├── middleware/          # Middleware (auth, logging)
│   ├── models/              # Domain models
│   ├── repository/          # Data access layer
│   ├── services/            # Business logic
│   └── validators/          # Request validation
├── pkg/                     # Shared packages
├── migrations/              # Database migrations
├── go.mod
├── Dockerfile
└── README.md
```

## Implementation Steps

1. **Initialize Go Module**
   - Create `go.mod` in `services/` directory
   - Install dependencies: `fiber/v2`, `gorm`, `validator/v10`

2. **Core Application Files**
   - `cmd/api/main.go` - Fiber app setup with routes, middleware
   - `internal/config/config.go` - Configuration from environment
   - `internal/handlers/health.go` - Health check endpoint
   - Refer to [tech-stack guide](../../../../docs/tech-stacks/apis/fiber.md#quick-start) for templates

3. **Database Setup** (if ${input:database} != None)
   - `internal/repository/postgres.go` - GORM connection
   - `internal/models/*.go` - GORM models
   - `migrations/` - SQL migration files
   - Use `golang-migrate` for migrations

4. **HTTP Handlers**
   - `internal/handlers/*.go` - Route handlers with Fiber context
   - Request validation with `validator/v10`
   - Response DTOs with JSON tags
   - Proper error handling

5. **Repository Layer**
   - `internal/repository/*.go` - Database interfaces
   - GORM implementation with methods
   - Connection pooling configuration

6. **Business Logic Layer**
   - `internal/services/*.go` - Service interfaces
   - Business logic separated from handlers
   - Dependency injection pattern

7. **Middleware**
   - `internal/middleware/logger.go` - Request logging
   - `internal/middleware/auth.go` - JWT validation (if required)
   - `internal/middleware/cors.go` - CORS configuration
   - `internal/middleware/recover.go` - Panic recovery

8. **Request Validation**
   - `internal/validators/*.go` - Validation structs
   - Use `validator/v10` tags
   - Custom validation functions

9. **Authentication** (if ${input:auth} != None)
   - `internal/services/auth.go` - JWT generation/validation
   - `internal/middleware/auth.go` - Protected routes
   - Refer to [auth examples](../../../../docs/tech-stacks/apis/fiber.md#authentication)

10. **Testing Setup**
    - `*_test.go` files alongside code
    - HTTP tests with `httptest`
    - Mock repository interfaces
    - Table-driven tests

11. **Docker Configuration**
    - `Dockerfile` - Multi-stage Go build
    - `.dockerignore` - Exclude unnecessary files
    - Health check integration

12. **Docker Compose Integration**
    - Update `docker-compose.yml` in project root
    - Add service with ports, environment, database dependency

13. **Development Configuration**
    - `.devcontainer/` - VS Code devcontainer
    - `.env.example` - Environment template
    - `.air.toml` - Hot reload configuration (optional)

14. **Verify Service**
    - Start service: `docker compose up ${input:serviceName}`
    - Test health endpoint: `curl http://localhost:${input:port}/health`
    - Run migrations: `migrate -path migrations -database $DATABASE_URL up`

## Code Templates

All code templates are available in the [Fiber Tech-Stack Documentation](../../../../docs/tech-stacks/apis/fiber.md):
- Fiber app setup
- Route handlers
- Repository interfaces
- GORM models
- Middleware patterns
- Error handling
- Testing patterns

## Completion Checklist

- [ ] Directory structure created
- [ ] Go module initialized
- [ ] Fiber app configured with routes
- [ ] Database connection set up (if required)
- [ ] GORM models defined
- [ ] Handlers with validation implemented
- [ ] Repository layer created
- [ ] Middleware configured
- [ ] Authentication implemented (if required)
- [ ] Tests written and passing
- [ ] Dockerfile builds successfully
- [ ] docker-compose.yml updated
- [ ] README.md documentation complete
- [ ] Service accessible at `http://localhost:${input:port}`
- [ ] Health endpoint returns 200 OK

## Post-Scaffolding

After scaffolding completes:
1. Run migrations: `docker compose exec ${input:serviceName} migrate -path migrations -database $DATABASE_URL up`
2. Run tests: `docker compose exec ${input:serviceName} go test ./...`
3. Refer to [instructions file](../instructions/service-api-fiber.instructions.md) for Copilot coding guidance

---

**Note**: This prompt creates the scaffolding only. Refer to the [complete tech-stack documentation](../../../../docs/tech-stacks/apis/fiber.md) for detailed implementation patterns, best practices, and code examples.
