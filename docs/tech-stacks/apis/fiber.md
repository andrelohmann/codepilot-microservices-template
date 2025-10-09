# Fiber - Go API Services

High-performance Go web framework for building fast and scalable APIs with minimal memory footprint.

## Overview

Fiber is an Express-inspired web framework built on top of Fasthttp, the fastest HTTP engine for Go.

**Perfect for:**
- High-throughput APIs
- Low-latency requirements  
- System services
- API gateways/proxies
- Resource-constrained environments

---

## Naming Convention

```
api-fiber-{purpose}
```

### Examples

```
api-fiber-gateway         # API gateway/proxy
api-fiber-metrics         # Metrics aggregation
api-fiber-proxy           # Reverse proxy
api-fiber-auth            # Authentication service
api-fiber-websocket       # WebSocket server
```

---

## Technology Stack

### Core Technologies

- **Go** 1.21+
- **Fiber** v2
- **GORM** or **pgx** (database)
- **validator/v10** (validation)
- **zerolog** (logging)

### Key Dependencies

**Production:**
```go
require (
    github.com/gofiber/fiber/v2 v2.50.0
    github.com/gofiber/jwt/v4 v4.0.0
    gorm.io/gorm v1.25.5
    gorm.io/driver/postgres v1.5.4
    github.com/go-playground/validator/v10 v10.16.0
    github.com/rs/zerolog v1.31.0
)
```

---

## Key Features

### 1. High Performance

```go
app := fiber.New(fiber.Config{
    Prefork:       true,  // Use multiple processes
    StrictRouting: true,
    CaseSensitive: true,
})

// Fastest routing
app.Get("/users/:id", getUser)
```

### 2. Express-like API

```go
package main

import "github.com/gofiber/fiber/v2"

func main() {
    app := fiber.New()

    app.Get("/", func(c *fiber.Ctx) error {
        return c.SendString("Hello, World!")
    })

    app.Listen(":3000")
}
```

### 3. Middleware Support

```go
import (
    "github.com/gofiber/fiber/v2"
    "github.com/gofiber/fiber/v2/middleware/logger"
    "github.com/gofiber/fiber/v2/middleware/cors"
)

app := fiber.New()
app.Use(logger.New())
app.Use(cors.New())
```

### 4. Request/Response Handling

```go
type CreateUserRequest struct {
    Email    string `json:"email" validate:"required,email"`
    Password string `json:"password" validate:"required,min=8"`
}

app.Post("/users", func(c *fiber.Ctx) error {
    var req CreateUserRequest
    if err := c.BodyParser(&req); err != nil {
        return c.Status(400).JSON(fiber.Map{"error": "Invalid request"})
    }

    if err := validator.Validate(req); err != nil {
        return c.Status(400).JSON(fiber.Map{"error": err.Error()})
    }

    user := createUser(req)
    return c.Status(201).JSON(user)
})
```

### 5. WebSocket Support

```go
import "github.com/gofiber/websocket/v2"

app.Get("/ws", websocket.New(func(c *websocket.Conn) {
    for {
        msgType, msg, err := c.ReadMessage()
        if err != nil {
            break
        }
        c.WriteMessage(msgType, msg)
    }
}))
```

---

## Ideal Use Cases

### 1. High-Throughput APIs ⭐⭐⭐

**Why Fiber:**
- Handles 100k+ requests/second
- Low memory footprint
- Native Go concurrency
- Minimal garbage collection pauses

**Example:**
```go
// High-performance endpoint
app.Get("/metrics", func(c *fiber.Ctx) error {
    // Handle thousands of concurrent requests
    metrics := collectMetrics()
    return c.JSON(metrics)
})
```

### 2. API Gateways/Proxies ⭐⭐⭐

**Why Fiber:**
- Fast request forwarding
- Low latency
- Efficient connection pooling
- Native concurrency for parallel requests

**Example:**
```go
app.All("/api/*", func(c *fiber.Ctx) error {
    url := "http://backend:8080" + c.Params("*")
    
    agent := fiber.AcquireAgent()
    defer fiber.ReleaseAgent(agent)
    
    agent.Request().SetRequestURI(url)
    agent.Request().Header.SetMethod(c.Method())
    
    if err := agent.Parse(); err != nil {
        return err
    }
    
    _, body, _ := agent.Bytes()
    return c.Send(body)
})
```

### 3. Low-Latency Requirements ⭐⭐⭐

**Why Fiber:**
- Sub-millisecond response times
- Zero-copy operations where possible
- Fasthttp engine
- Compiled binary (no startup time)

### 4. System Services ⭐⭐

**Why Fiber:**
- Small binary size
- Low resource usage
- Easy deployment
- Great for system-level APIs

---

## Project Structure

```
services/api-fiber-{purpose}/
├── cmd/
│   └── server/
│       └── main.go                # Application entry point
├── internal/
│   ├── handlers/
│   │   ├── user_handler.go
│   │   └── auth_handler.go
│   ├── models/
│   │   └── user.go
│   ├── services/
│   │   └── user_service.go
│   ├── middleware/
│   │   └── auth.go
│   ├── db/
│   │   └── database.go
│   └── config/
│       └── config.go
├── pkg/
│   └── utils/
│       └── validator.go
├── Dockerfile
├── go.mod
├── go.sum
├── .env.example
└── README.md
```

---

## Testing

### Unit Tests

```go
func TestCreateUser(t *testing.T) {
    app := fiber.New()
    app.Post("/users", createUserHandler)

    req := httptest.NewRequest("POST", "/users", strings.NewReader(`{
        "email": "test@example.com",
        "password": "password123"
    }`))
    req.Header.Set("Content-Type", "application/json")

    resp, _ := app.Test(req)
    assert.Equal(t, 201, resp.StatusCode)
}
```

### Test Frameworks

- `testing` - Go standard testing
- `github.com/stretchr/testify` - Assertions
- `httptest` - HTTP testing

### Run Tests

```bash
# Run all tests
go test ./...

# Run with coverage
go test -cover ./...

# Run with verbose output
go test -v ./...

# Run specific test
go test -run TestCreateUser ./...
```

---

## Deployment

### Dockerfile

```dockerfile
# Multi-stage build
FROM golang:1.21-alpine AS builder

WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main cmd/server/main.go

FROM alpine:latest

RUN apk --no-cache add ca-certificates
WORKDIR /root/

COPY --from=builder /app/main .

EXPOSE 3000

# Non-root user
RUN adduser -D -u 1000 apiuser
USER apiuser

CMD ["./main"]
```

### Environment Variables

```bash
# .env.example
PORT=3000
ENV=production

# Database
DB_HOST=localhost
DB_PORT=5432
DB_USER=user
DB_PASSWORD=password
DB_NAME=dbname

# JWT
JWT_SECRET=your-secret-key
JWT_EXPIRATION=3600
```

---

## Performance Tips

### 1. Use Prefork for Multi-Core

```go
app := fiber.New(fiber.Config{
    Prefork: true, // Spawn multiple processes
})
```

### 2. Pool Database Connections

```go
db, err := gorm.Open(postgres.Open(dsn), &gorm.Config{})
sqlDB, _ := db.DB()
sqlDB.SetMaxOpenConns(25)
sqlDB.SetMaxIdleConns(25)
sqlDB.SetConnMaxLifetime(5 * time.Minute)
```

### 3. Use Connection Pooling for Outbound Requests

```go
var httpClient = &http.Client{
    Transport: &http.Transport{
        MaxIdleConns:        100,
        MaxIdleConnsPerHost: 100,
    },
    Timeout: 10 * time.Second,
}
```

---

## Best Practices

### 1. Use Struct Tags for Validation

```go
type User struct {
    Email    string `json:"email" validate:"required,email"`
    Password string `json:"password" validate:"required,min=8"`
    Age      int    `json:"age" validate:"gte=18,lte=120"`
}
```

### 2. Repository Pattern

```go
type UserRepository interface {
    Create(user *User) error
    FindByID(id uint) (*User, error)
    FindByEmail(email string) (*User, error)
}

type userRepository struct {
    db *gorm.DB
}

func (r *userRepository) Create(user *User) error {
    return r.db.Create(user).Error
}
```

### 3. Graceful Shutdown

```go
func main() {
    app := fiber.New()
    
    // Setup routes...
    
    // Graceful shutdown
    c := make(chan os.Signal, 1)
    signal.Notify(c, os.Interrupt, syscall.SIGTERM)
    
    go func() {
        <-c
        _ = app.Shutdown()
    }()
    
    app.Listen(":3000")
}
```

---

## Common Pitfalls

### ❌ Not Handling Errors Properly

```go
// Wrong
app.Get("/users/:id", func(c *fiber.Ctx) error {
    user, _ := findUser(c.Params("id")) // Ignoring error!
    return c.JSON(user)
})

// Correct
app.Get("/users/:id", func(c *fiber.Ctx) error {
    user, err := findUser(c.Params("id"))
    if err != nil {
        return c.Status(404).JSON(fiber.Map{"error": "User not found"})
    }
    return c.JSON(user)
})
```

### ❌ Not Using Context Properly

```go
// Wrong - blocking
app.Get("/data", func(c *fiber.Ctx) error {
    data := slowOperation() // Blocks!
    return c.JSON(data)
})

// Correct - with timeout
app.Get("/data", func(c *fiber.Ctx) error {
    ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
    defer cancel()
    
    data, err := slowOperationWithContext(ctx)
    if err != nil {
        return c.Status(500).JSON(fiber.Map{"error": err.Error()})
    }
    return c.JSON(data)
})
```

---

## Related Files

- **Instruction File:** `templates/.github/instructions/service-api-fiber.instructions.md`
- **Scaffold Prompt:** `templates/.github/prompts/scaffold-api-fiber-service.md`

---

## See Also

- **[API Services Overview](./README.md)** - Compare with other API technologies
- **[FastAPI](./fastapi.md)** - Python alternative
- **[NestJS](./nestjs.md)** - Node.js alternative
- **[Workers - Golang](../workers/golang.md)** - Background jobs with Go

---

**Last Updated:** 2025-10-09  
**Template Version:** 1.0.0
