# Scaffold Fiber API Service

**Objective:** Create a production-ready Go Fiber API service with complete directory structure, Docker configuration, testing setup, and comprehensive documentation.

---

## 📋 Service Specification

Before running this prompt, gather the following information:

1. **Service Name:** `[e.g., api-fiber-catalog, api-fiber-orders]`
2. **Service Purpose:** `[Brief description of what this service does]`
3. **Database Required:** `[Yes/No - PostgreSQL]`
4. **Cache Required:** `[Yes/No - Redis]`
5. **Message Queue Required:** `[Yes/No - NATS/RabbitMQ]`
6. **External APIs:** `[List any external services to integrate]`
7. **Authentication:** `[JWT, OAuth, None]`
8. **Port:** `[Default: 8080, or specify custom]`

---

## 🏗️ Architecture Overview

```
┌────────────────────────────────────────────────────────────────────┐
│                      Fiber Service Architecture                    │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                     Handler Layer                            │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │  Handlers  │  │ Middleware │  │Validators  │            │ │
│  │  │  (Routes)  │  │  (Auth)    │  │  (Struct)  │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
│            ↓                ↓                ↓                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                      Service Layer                           │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │  Business  │  │   DTOs     │  │  External  │            │ │
│  │  │   Logic    │  │ (Request/  │  │   Clients  │            │ │
│  │  │            │  │ Response)  │  │            │            │ │
│  │  └──────┬─────┘  └────────────┘  └────────────┘            │ │
│  └─────────┼────────────────────────────────────────────────────┘ │
│            │                                                      │
│            ↓                                                      │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                    Repository Layer                          │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │  pgx/GORM  │  │   Cache    │  │NATS Client │            │ │
│  │  │Repositories│  │  (Redis)   │  │            │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
└────────────┼────────────────┼────────────────┼────────────────────┘
             │                │                │
             ↓                ↓                ↓
    ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
    │ PostgreSQL  │  │    Redis    │  │    NATS     │
    │  (Backing   │  │  (Backing   │  │  (Backing   │
    │   Service)  │  │   Service)  │  │   Service)  │
    └─────────────┘  └─────────────┘  └─────────────┘
```

---

## 📁 Directory Structure

Create the following complete directory structure:

```
services/[service-name]/
├── cmd/
│   └── server/
│       └── main.go              ← Application entry point
│
├── internal/
│   ├── config/                  ← Configuration
│   │   └── config.go
│   │
│   ├── handlers/                ← HTTP handlers
│   │   ├── health.go
│   │   └── users.go
│   │
│   ├── middleware/              ← Middleware
│   │   ├── auth.go
│   │   ├── logger.go
│   │   ├── recover.go
│   │   └── cors.go
│   │
│   ├── models/                  ← Domain models
│   │   └── user.go
│   │
│   ├── repository/              ← Data access
│   │   ├── repository.go
│   │   └── user_repository.go
│   │
│   ├── services/                ← Business logic
│   │   └── user_service.go
│   │
│   ├── dto/                     ← Data transfer objects
│   │   └── user_dto.go
│   │
│   └── database/                ← Database setup
│       └── postgres.go
│
├── pkg/                         ← Public libraries
│   ├── logger/
│   │   └── logger.go
│   ├── validator/
│   │   └── validator.go
│   └── errors/
│       └── errors.go
│
├── migrations/                  ← Database migrations
│   └── 001_initial.sql
│
├── tests/                       ← Tests
│   ├── integration/
│   │   └── users_test.go
│   └── unit/
│       └── user_service_test.go
│
├── scripts/                     ← Helper scripts
│   ├── migrate.sh
│   └── test.sh
│
├── .devcontainer/
│   └── [service-name]-container/
│       └── devcontainer.json
│
├── Dockerfile
├── docker-compose.yml
├── go.mod
├── go.sum
├── .env.example
├── .gitignore
├── Makefile
└── README.md
```

---

## 🔨 Implementation Steps

### Step 1: Create go.mod

```go
module github.com/yourorg/[service-name]

go 1.21

require (
    github.com/gofiber/fiber/v2 v2.51.0
    github.com/gofiber/contrib/jwt v1.0.7
    github.com/jackc/pgx/v5 v5.5.0
    github.com/redis/go-redis/v9 v9.3.0
    github.com/rs/zerolog v1.31.0
    github.com/go-playground/validator/v10 v10.16.0
    github.com/golang-migrate/migrate/v4 v4.17.0
    github.com/stretchr/testify v1.8.4
)
```

### Step 2: Create cmd/server/main.go

```go
package main

import (
    "context"
    "fmt"
    "log"
    "os"
    "os/signal"
    "syscall"
    "time"

    "github.com/gofiber/fiber/v2"
    "github.com/gofiber/fiber/v2/middleware/cors"
    "github.com/gofiber/fiber/v2/middleware/recover"
    "github.com/rs/zerolog"

    "github.com/yourorg/[service-name]/internal/config"
    "github.com/yourorg/[service-name]/internal/database"
    "github.com/yourorg/[service-name]/internal/handlers"
    "github.com/yourorg/[service-name]/internal/middleware"
    "github.com/yourorg/[service-name]/internal/repository"
    "github.com/yourorg/[service-name]/internal/services"
)

func main() {
    // Load configuration
    cfg, err := config.Load()
    if err != nil {
        log.Fatal("Failed to load configuration:", err)
    }

    // Setup logger
    zerolog.TimeFieldFormat = zerolog.TimeFormatUnix
    logger := zerolog.New(os.Stdout).With().Timestamp().Logger()

    // Initialize database
    db, err := database.NewPostgresDB(cfg.DatabaseURL)
    if err != nil {
        logger.Fatal().Err(err).Msg("Failed to connect to database")
    }
    defer db.Close()

    // Initialize repositories
    userRepo := repository.NewUserRepository(db)

    // Initialize services
    userService := services.NewUserService(userRepo, logger)

    // Create Fiber app
    app := fiber.New(fiber.Config{
        ErrorHandler: func(c *fiber.Ctx, err error) error {
            code := fiber.StatusInternalServerError
            if e, ok := err.(*fiber.Error); ok {
                code = e.Code
            }
            return c.Status(code).JSON(fiber.Map{
                "error": err.Error(),
            })
        },
        ReadTimeout:  10 * time.Second,
        WriteTimeout: 10 * time.Second,
    })

    // Global middleware
    app.Use(recover.New())
    app.Use(middleware.Logger(logger))
    app.Use(cors.New(cors.Config{
        AllowOrigins: cfg.CORSOrigins,
        AllowHeaders: "Origin, Content-Type, Accept, Authorization",
    }))

    // Health check
    app.Get("/health", handlers.HealthCheck)
    app.Get("/ready", handlers.ReadyCheck(db))

    // API v1 routes
    v1 := app.Group("/api/v1")
    
    // Users routes
    usersHandler := handlers.NewUsersHandler(userService, logger)
    users := v1.Group("/users")
    users.Get("/", usersHandler.ListUsers)
    users.Get("/:id", usersHandler.GetUser)
    users.Post("/", usersHandler.CreateUser)
    users.Put("/:id", middleware.AuthRequired(cfg.JWTSecret), usersHandler.UpdateUser)
    users.Delete("/:id", middleware.AuthRequired(cfg.JWTSecret), usersHandler.DeleteUser)

    // Start server in goroutine
    go func() {
        addr := fmt.Sprintf(":%d", cfg.Port)
        logger.Info().Msgf("Starting server on %s", addr)
        if err := app.Listen(addr); err != nil {
            logger.Fatal().Err(err).Msg("Server failed to start")
        }
    }()

    // Graceful shutdown
    quit := make(chan os.Signal, 1)
    signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
    <-quit

    logger.Info().Msg("Shutting down server...")
    ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
    defer cancel()

    if err := app.ShutdownWithContext(ctx); err != nil {
        logger.Error().Err(err).Msg("Server forced to shutdown")
    }

    logger.Info().Msg("Server exited")
}
```

### Step 3: Create internal/config/config.go

```go
package config

import (
    "fmt"
    "os"
    "strconv"
)

type Config struct {
    Port        int
    DatabaseURL string
    RedisURL    string
    JWTSecret   string
    CORSOrigins string
    Environment string
}

func Load() (*Config, error) {
    port, err := strconv.Atoi(getEnv("PORT", "8080"))
    if err != nil {
        return nil, fmt.Errorf("invalid PORT: %w", err)
    }

    cfg := &Config{
        Port:        port,
        DatabaseURL: getEnv("DATABASE_URL", ""),
        RedisURL:    getEnv("REDIS_URL", "redis://localhost:6379"),
        JWTSecret:   getEnv("JWT_SECRET", ""),
        CORSOrigins: getEnv("CORS_ORIGINS", "*"),
        Environment: getEnv("ENVIRONMENT", "development"),
    }

    if cfg.DatabaseURL == "" {
        return nil, fmt.Errorf("DATABASE_URL is required")
    }

    if cfg.JWTSecret == "" && cfg.Environment == "production" {
        return nil, fmt.Errorf("JWT_SECRET is required in production")
    }

    return cfg, nil
}

func getEnv(key, defaultValue string) string {
    if value := os.Getenv(key); value != "" {
        return value
    }
    return defaultValue
}
```

### Step 4: Create internal/models/user.go

```go
package models

import (
    "time"
)

type User struct {
    ID        int       `json:"id" db:"id"`
    Email     string    `json:"email" db:"email"`
    Name      string    `json:"name" db:"name"`
    CreatedAt time.Time `json:"created_at" db:"created_at"`
    UpdatedAt time.Time `json:"updated_at" db:"updated_at"`
}
```

### Step 5: Create internal/dto/user_dto.go

```go
package dto

type CreateUserRequest struct {
    Email string `json:"email" validate:"required,email"`
    Name  string `json:"name" validate:"required,min=2,max=100"`
}

type UpdateUserRequest struct {
    Email string `json:"email" validate:"omitempty,email"`
    Name  string `json:"name" validate:"omitempty,min=2,max=100"`
}

type UserResponse struct {
    ID        int    `json:"id"`
    Email     string `json:"email"`
    Name      string `json:"name"`
    CreatedAt string `json:"created_at"`
    UpdatedAt string `json:"updated_at"`
}
```

### Step 6: Create internal/repository/user_repository.go

```go
package repository

import (
    "context"
    "database/sql"

    "github.com/yourorg/[service-name]/internal/models"
)

type UserRepository interface {
    FindAll(ctx context.Context) ([]models.User, error)
    FindByID(ctx context.Context, id int) (*models.User, error)
    Create(ctx context.Context, user *models.User) error
    Update(ctx context.Context, user *models.User) error
    Delete(ctx context.Context, id int) error
}

type userRepository struct {
    db *sql.DB
}

func NewUserRepository(db *sql.DB) UserRepository {
    return &userRepository{db: db}
}

func (r *userRepository) FindAll(ctx context.Context) ([]models.User, error) {
    query := `SELECT id, email, name, created_at, updated_at FROM users ORDER BY id`
    rows, err := r.db.QueryContext(ctx, query)
    if err != nil {
        return nil, err
    }
    defer rows.Close()

    var users []models.User
    for rows.Next() {
        var user models.User
        if err := rows.Scan(&user.ID, &user.Email, &user.Name, &user.CreatedAt, &user.UpdatedAt); err != nil {
            return nil, err
        }
        users = append(users, user)
    }

    return users, nil
}

func (r *userRepository) FindByID(ctx context.Context, id int) (*models.User, error) {
    query := `SELECT id, email, name, created_at, updated_at FROM users WHERE id = $1`
    var user models.User
    err := r.db.QueryRowContext(ctx, query, id).Scan(
        &user.ID, &user.Email, &user.Name, &user.CreatedAt, &user.UpdatedAt,
    )
    if err == sql.ErrNoRows {
        return nil, nil
    }
    if err != nil {
        return nil, err
    }
    return &user, nil
}

func (r *userRepository) Create(ctx context.Context, user *models.User) error {
    query := `INSERT INTO users (email, name) VALUES ($1, $2) RETURNING id, created_at, updated_at`
    return r.db.QueryRowContext(ctx, query, user.Email, user.Name).Scan(
        &user.ID, &user.CreatedAt, &user.UpdatedAt,
    )
}

func (r *userRepository) Update(ctx context.Context, user *models.User) error {
    query := `UPDATE users SET email = $1, name = $2, updated_at = NOW() WHERE id = $3 RETURNING updated_at`
    return r.db.QueryRowContext(ctx, query, user.Email, user.Name, user.ID).Scan(&user.UpdatedAt)
}

func (r *userRepository) Delete(ctx context.Context, id int) error {
    query := `DELETE FROM users WHERE id = $1`
    result, err := r.db.ExecContext(ctx, query, id)
    if err != nil {
        return err
    }
    rows, err := result.RowsAffected()
    if err != nil {
        return err
    }
    if rows == 0 {
        return sql.ErrNoRows
    }
    return nil
}
```

### Step 7: Create internal/services/user_service.go

```go
package services

import (
    "context"
    "fmt"

    "github.com/rs/zerolog"
    "github.com/yourorg/[service-name]/internal/models"
    "github.com/yourorg/[service-name]/internal/repository"
)

type UserService interface {
    GetAllUsers(ctx context.Context) ([]models.User, error)
    GetUserByID(ctx context.Context, id int) (*models.User, error)
    CreateUser(ctx context.Context, user *models.User) error
    UpdateUser(ctx context.Context, user *models.User) error
    DeleteUser(ctx context.Context, id int) error
}

type userService struct {
    repo   repository.UserRepository
    logger zerolog.Logger
}

func NewUserService(repo repository.UserRepository, logger zerolog.Logger) UserService {
    return &userService{
        repo:   repo,
        logger: logger,
    }
}

func (s *userService) GetAllUsers(ctx context.Context) ([]models.User, error) {
    users, err := s.repo.FindAll(ctx)
    if err != nil {
        s.logger.Error().Err(err).Msg("Failed to fetch users")
        return nil, fmt.Errorf("failed to fetch users: %w", err)
    }
    return users, nil
}

func (s *userService) GetUserByID(ctx context.Context, id int) (*models.User, error) {
    user, err := s.repo.FindByID(ctx, id)
    if err != nil {
        s.logger.Error().Err(err).Int("user_id", id).Msg("Failed to fetch user")
        return nil, fmt.Errorf("failed to fetch user: %w", err)
    }
    if user == nil {
        return nil, fmt.Errorf("user not found")
    }
    return user, nil
}

func (s *userService) CreateUser(ctx context.Context, user *models.User) error {
    if err := s.repo.Create(ctx, user); err != nil {
        s.logger.Error().Err(err).Msg("Failed to create user")
        return fmt.Errorf("failed to create user: %w", err)
    }
    s.logger.Info().Int("user_id", user.ID).Msg("User created successfully")
    return nil
}

func (s *userService) UpdateUser(ctx context.Context, user *models.User) error {
    if err := s.repo.Update(ctx, user); err != nil {
        s.logger.Error().Err(err).Int("user_id", user.ID).Msg("Failed to update user")
        return fmt.Errorf("failed to update user: %w", err)
    }
    s.logger.Info().Int("user_id", user.ID).Msg("User updated successfully")
    return nil
}

func (s *userService) DeleteUser(ctx context.Context, id int) error {
    if err := s.repo.Delete(ctx, id); err != nil {
        s.logger.Error().Err(err).Int("user_id", id).Msg("Failed to delete user")
        return fmt.Errorf("failed to delete user: %w", err)
    }
    s.logger.Info().Int("user_id", id).Msg("User deleted successfully")
    return nil
}
```

### Step 8: Create internal/handlers/users.go

```go
package handlers

import (
    "strconv"

    "github.com/gofiber/fiber/v2"
    "github.com/go-playground/validator/v10"
    "github.com/rs/zerolog"

    "github.com/yourorg/[service-name]/internal/dto"
    "github.com/yourorg/[service-name]/internal/models"
    "github.com/yourorg/[service-name]/internal/services"
)

type UsersHandler struct {
    service  services.UserService
    logger   zerolog.Logger
    validate *validator.Validate
}

func NewUsersHandler(service services.UserService, logger zerolog.Logger) *UsersHandler {
    return &UsersHandler{
        service:  service,
        logger:   logger,
        validate: validator.New(),
    }
}

func (h *UsersHandler) ListUsers(c *fiber.Ctx) error {
    users, err := h.service.GetAllUsers(c.Context())
    if err != nil {
        return fiber.NewError(fiber.StatusInternalServerError, err.Error())
    }
    return c.JSON(users)
}

func (h *UsersHandler) GetUser(c *fiber.Ctx) error {
    id, err := strconv.Atoi(c.Params("id"))
    if err != nil {
        return fiber.NewError(fiber.StatusBadRequest, "Invalid user ID")
    }

    user, err := h.service.GetUserByID(c.Context(), id)
    if err != nil {
        return fiber.NewError(fiber.StatusNotFound, err.Error())
    }

    return c.JSON(user)
}

func (h *UsersHandler) CreateUser(c *fiber.Ctx) error {
    var req dto.CreateUserRequest
    if err := c.BodyParser(&req); err != nil {
        return fiber.NewError(fiber.StatusBadRequest, "Invalid request body")
    }

    if err := h.validate.Struct(req); err != nil {
        return fiber.NewError(fiber.StatusBadRequest, err.Error())
    }

    user := &models.User{
        Email: req.Email,
        Name:  req.Name,
    }

    if err := h.service.CreateUser(c.Context(), user); err != nil {
        return fiber.NewError(fiber.StatusInternalServerError, err.Error())
    }

    return c.Status(fiber.StatusCreated).JSON(user)
}

func (h *UsersHandler) UpdateUser(c *fiber.Ctx) error {
    id, err := strconv.Atoi(c.Params("id"))
    if err != nil {
        return fiber.NewError(fiber.StatusBadRequest, "Invalid user ID")
    }

    var req dto.UpdateUserRequest
    if err := c.BodyParser(&req); err != nil {
        return fiber.NewError(fiber.StatusBadRequest, "Invalid request body")
    }

    if err := h.validate.Struct(req); err != nil {
        return fiber.NewError(fiber.StatusBadRequest, err.Error())
    }

    user := &models.User{
        ID:    id,
        Email: req.Email,
        Name:  req.Name,
    }

    if err := h.service.UpdateUser(c.Context(), user); err != nil {
        return fiber.NewError(fiber.StatusInternalServerError, err.Error())
    }

    return c.JSON(user)
}

func (h *UsersHandler) DeleteUser(c *fiber.Ctx) error {
    id, err := strconv.Atoi(c.Params("id"))
    if err != nil {
        return fiber.NewError(fiber.StatusBadRequest, "Invalid user ID")
    }

    if err := h.service.DeleteUser(c.Context(), id); err != nil {
        return fiber.NewError(fiber.StatusNotFound, err.Error())
    }

    return c.SendStatus(fiber.StatusNoContent)
}
```

### Step 9: Create internal/handlers/health.go

```go
package handlers

import (
    "database/sql"

    "github.com/gofiber/fiber/v2"
)

func HealthCheck(c *fiber.Ctx) error {
    return c.JSON(fiber.Map{
        "status": "healthy",
    })
}

func ReadyCheck(db *sql.DB) fiber.Handler {
    return func(c *fiber.Ctx) error {
        if err := db.Ping(); err != nil {
            return c.Status(fiber.StatusServiceUnavailable).JSON(fiber.Map{
                "status": "not ready",
                "error":  "database connection failed",
            })
        }
        return c.JSON(fiber.Map{
            "status": "ready",
        })
    }
}
```

### Step 10: Create internal/middleware/auth.go

```go
package middleware

import (
    "github.com/gofiber/fiber/v2"
    jwtware "github.com/gofiber/contrib/jwt"
)

func AuthRequired(secret string) fiber.Handler {
    return jwtware.New(jwtware.Config{
        SigningKey: jwtware.SigningKey{Key: []byte(secret)},
        ErrorHandler: func(c *fiber.Ctx, err error) error {
            return c.Status(fiber.StatusUnauthorized).JSON(fiber.Map{
                "error": "Unauthorized",
            })
        },
    })
}
```

### Step 11: Create internal/middleware/logger.go

```go
package middleware

import (
    "time"

    "github.com/gofiber/fiber/v2"
    "github.com/rs/zerolog"
)

func Logger(logger zerolog.Logger) fiber.Handler {
    return func(c *fiber.Ctx) error {
        start := time.Now()

        err := c.Next()

        logger.Info().
            Str("method", c.Method()).
            Str("path", c.Path()).
            Int("status", c.Response().StatusCode()).
            Dur("duration", time.Since(start)).
            Msg("Request processed")

        return err
    }
}
```

### Step 12: Create internal/database/postgres.go

```go
package database

import (
    "database/sql"
    "fmt"

    _ "github.com/jackc/pgx/v5/stdlib"
)

func NewPostgresDB(databaseURL string) (*sql.DB, error) {
    db, err := sql.Open("pgx", databaseURL)
    if err != nil {
        return nil, fmt.Errorf("failed to open database: %w", err)
    }

    if err := db.Ping(); err != nil {
        return nil, fmt.Errorf("failed to ping database: %w", err)
    }

    // Set connection pool settings
    db.SetMaxOpenConns(25)
    db.SetMaxIdleConns(5)

    return db, nil
}
```

### Step 13: Create migrations/001_initial.sql

```sql
-- Create users table
CREATE TABLE IF NOT EXISTS users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) NOT NULL UNIQUE,
    name VARCHAR(255) NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMP NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_users_email ON users(email);
```

### Step 14: Create Dockerfile

```dockerfile
# Build stage
FROM golang:1.21-alpine AS builder

WORKDIR /app

# Install dependencies
RUN apk add --no-cache git

# Copy go mod files
COPY go.mod go.sum ./
RUN go mod download

# Copy source code
COPY . .

# Build binary
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main ./cmd/server

# Runtime stage
FROM alpine:latest

RUN apk --no-cache add ca-certificates

WORKDIR /root/

# Copy binary from builder
COPY --from=builder /app/main .
COPY --from=builder /app/migrations ./migrations

EXPOSE 8080

CMD ["./main"]
```

### Step 15: Create docker-compose.yml

```yaml
version: '3.8'

services:
  [service-name]:
    build: .
    ports:
      - "8080:8080"
    environment:
      - PORT=8080
      - DATABASE_URL=postgres://user:password@postgres:5432/dbname?sslmode=disable
      - REDIS_URL=redis://redis:6379
      - JWT_SECRET=your-secret-key-change-in-production
      - ENVIRONMENT=development
    depends_on:
      - postgres
      - redis
    networks:
      - microservices

  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: dbname
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - microservices

  redis:
    image: redis:7-alpine
    networks:
      - microservices

volumes:
  postgres_data:

networks:
  microservices:
    external: true
```

### Step 16: Create .devcontainer/[service-name]-container/devcontainer.json

```json
{
  "name": "[service-name]",
  "dockerComposeFile": [
    "../../docker-compose.yml",
    "../../docker-compose.development.yml"
  ],
  "service": "[service-name]",
  "workspaceFolder": "/workspace",
  "customizations": {
    "vscode": {
      "extensions": [
        "golang.go",
        "ms-azuretools.vscode-docker"
      ],
      "settings": {
        "go.useLanguageServer": true,
        "go.lintTool": "golangci-lint",
        "go.lintOnSave": "workspace"
      }
    }
  },
  "forwardPorts": [8080],
  "postCreateCommand": "go mod download"
}
```

### Step 17: Create Makefile

```makefile
.PHONY: build run test lint migrate

build:
	go build -o bin/server cmd/server/main.go

run:
	go run cmd/server/main.go

test:
	go test -v -race -coverprofile=coverage.out ./...

lint:
	golangci-lint run

migrate-up:
	migrate -path migrations -database "$(DATABASE_URL)" up

migrate-down:
	migrate -path migrations -database "$(DATABASE_URL)" down 1

docker-build:
	docker build -t [service-name]:latest .

docker-run:
	docker-compose up -d
```

### Step 18: Create .env.example

```bash
PORT=8080
DATABASE_URL=postgres://user:password@localhost:5432/dbname?sslmode=disable
REDIS_URL=redis://localhost:6379
JWT_SECRET=your-secret-key-minimum-32-characters-long
CORS_ORIGINS=*
ENVIRONMENT=development
```

### Step 19: Create .gitignore

```
# Binaries
bin/
*.exe
*.exe~
*.dll
*.so
*.dylib

# Test coverage
*.out
coverage.html

# Go workspace file
go.work

# Environment files
.env
.env.local

# IDE
.idea/
.vscode/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db
```

### Step 20: Create README.md

```markdown
# [Service Name]

[Brief description of the service purpose]

## Features

- RESTful API with Fiber v2
- PostgreSQL database with pgx driver
- Redis caching support
- JWT authentication
- Structured logging with zerolog
- Request validation
- Docker & Docker Compose ready
- Health check endpoints

## Prerequisites

- Go 1.21+
- Docker & Docker Compose
- PostgreSQL 16+
- Redis 7+

## Getting Started

### Local Development

1. Copy environment file:
```bash
cp .env.example .env
```

2. Install dependencies:
```bash
go mod download
```

3. Run migrations:
```bash
make migrate-up
```

4. Start the server:
```bash
make run
```

### Docker Development

```bash
docker-compose up -d
```

## API Endpoints

### Health Checks
- `GET /health` - Basic health check
- `GET /ready` - Readiness check (includes DB ping)

### Users API
- `GET /api/v1/users` - List all users
- `GET /api/v1/users/:id` - Get user by ID
- `POST /api/v1/users` - Create user
- `PUT /api/v1/users/:id` - Update user (requires auth)
- `DELETE /api/v1/users/:id` - Delete user (requires auth)

## Testing

Run tests:
```bash
make test
```

Run with coverage:
```bash
go test -coverprofile=coverage.out ./...
go tool cover -html=coverage.out
```

## Project Structure

```
├── cmd/server/       # Application entry point
├── internal/         # Private application code
│   ├── config/       # Configuration
│   ├── handlers/     # HTTP handlers
│   ├── middleware/   # Custom middleware
│   ├── models/       # Domain models
│   ├── repository/   # Data access layer
│   ├── services/     # Business logic
│   └── database/     # Database setup
├── pkg/              # Public libraries
├── migrations/       # Database migrations
└── tests/            # Test files
```

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| PORT | Server port | 8080 |
| DATABASE_URL | PostgreSQL connection string | Required |
| REDIS_URL | Redis connection string | redis://localhost:6379 |
| JWT_SECRET | JWT signing secret | Required in production |
| CORS_ORIGINS | Allowed CORS origins | * |
| ENVIRONMENT | Environment (development/production) | development |

## License

[Your License]
```

---

## ✅ Completion Checklist

- [ ] All files created in correct directory structure
- [ ] go.mod and dependencies configured
- [ ] Main application entry point implemented
- [ ] Configuration management setup
- [ ] Database connection and repository layer
- [ ] Business logic services implemented
- [ ] HTTP handlers with validation
- [ ] Middleware (auth, logging, CORS)
- [ ] Health check endpoints
- [ ] Database migrations created
- [ ] Dockerfile with multi-stage build
- [ ] docker-compose.yml configured
- [ ] DevContainer configuration
- [ ] Makefile with common commands
- [ ] .env.example with all variables
- [ ] .gitignore configured
- [ ] README.md with documentation
- [ ] Service added to root docker-compose files
- [ ] Service tested locally
- [ ] Integration tests passing

---

**Next Steps:**
1. Customize the handlers and services for your specific domain
2. Add authentication/authorization logic
3. Implement caching strategies with Redis
4. Add message queue integration if needed
5. Set up CI/CD pipeline
6. Configure monitoring and logging
