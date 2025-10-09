---
applyTo: "**/api-fiber-*/**"
description: Fiber API service with Go, high performance, and Express-like routing
---

# Fiber API Service - Copilot Guidance

Quick reference for GitHub Copilot when working with Fiber API services.

## Reference Documentation

- **[Complete Fiber Tech-Stack Guide](../../../docs/tech-stacks/apis/fiber.md)** - Full implementation details, examples, and best practices
- **[Scaffold Prompt](../prompts/scaffold-api-fiber-service.prompt.md)** - Generate new Fiber services

## Naming Convention

`api-fiber-{purpose}` (e.g., `api-fiber-auth`, `api-fiber-analytics`)

## Project Structure

```
services/api-fiber-{purpose}/
├── cmd/server/
│   └── main.go                # Application entry point
├── internal/
│   ├── config/                # Configuration
│   ├── handlers/              # HTTP handlers (controllers)
│   ├── middleware/            # Custom middleware
│   ├── models/                # Data models/structs
│   ├── repository/            # Database access layer
│   └── services/              # Business logic
├── pkg/                       # Shared/exported packages
├── go.mod
├── Dockerfile
└── README.md
```

## Copilot Coding Guidance

### Use These Patterns

1. **Clean Architecture** - Handler → Service → Repository
2. **Context Passing** - Use `context.Context` for cancellation
3. **Error Handling** - Always check errors, use fiber.Error
4. **Validation** - Use validator tags on structs
5. **Dependency Injection** - Pass dependencies via structs
6. **Interface Design** - Define interfaces for repositories

### Quick Patterns Reference

**Route Handler**:
```go
func (h *UserHandler) CreateUser(c *fiber.Ctx) error {
    var req CreateUserRequest
    if err := c.BodyParser(&req); err != nil {
        return fiber.NewError(fiber.StatusBadRequest, "Invalid request body")
    }
    
    if err := h.validator.Struct(req); err != nil {
        return fiber.NewError(fiber.StatusBadRequest, err.Error())
    }
    
    user, err := h.service.CreateUser(c.Context(), req)
    if err != nil {
        return err
    }
    
    return c.Status(fiber.StatusCreated).JSON(fiber.Map{
        "data": user,
    })
}
```

**Validated Request Struct**:
```go
type CreateUserRequest struct {
    Email    string `json:"email" validate:"required,email"`
    Password string `json:"password" validate:"required,min=8"`
    Name     string `json:"name" validate:"required"`
}
```

**Repository Interface**:
```go
type UserRepository interface {
    Create(ctx context.Context, user *models.User) error
    FindByID(ctx context.Context, id string) (*models.User, error)
    FindByEmail(ctx context.Context, email string) (*models.User, error)
}
```

**Middleware Example**:
```go
func AuthMiddleware(secret string) fiber.Handler {
    return func(c *fiber.Ctx) error {
        token := c.Get("Authorization")
        if token == "" {
            return fiber.NewError(fiber.StatusUnauthorized, "Missing token")
        }
        
        // Validate JWT token
        claims, err := validateJWT(token, secret)
        if err != nil {
            return fiber.NewError(fiber.StatusUnauthorized, "Invalid token")
        }
        
        c.Locals("userID", claims.UserID)
        return c.Next()
    }
}
```

### Code Style

- **Naming**: PascalCase for exported, camelCase for unexported
- **Error Handling**: Check all errors, use `fiber.NewError` with status
- **Package Organization**: `internal/` for private, `pkg/` for shared
- **Imports**: Group stdlib, external, local with blank lines
- **Contexts**: Pass `context.Context` as first parameter

### Anti-Patterns to Avoid

❌ Ignoring error returns (`_, _ = ...`)  
❌ Using `interface{}` without type assertions  
❌ Global state beyond config/logger  
❌ Direct DB access from handlers  
❌ Forgetting `defer` for resource cleanup  
❌ Not using contexts for cancellation  
❌ Returning HTML in JSON API  

For detailed examples, deployment guides, testing strategies, and advanced patterns, refer to the [Fiber Tech-Stack Documentation](../../../docs/tech-stacks/apis/fiber.md).

