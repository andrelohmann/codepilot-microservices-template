# Scaffold Golang Worker Service

**Objective:** Create a production-ready Golang worker service with worker pools, job processing, and complete directory structure.

---

## 📋 Service Specification

Before running this prompt, gather the following information:

1. **Service Name:** `[e.g., worker-golang-reports, worker-golang-processor]`
2. **Service Purpose:** `[Brief description of what jobs this worker processes]`
3. **Job Source:** `[Redis, NATS, RabbitMQ, Kafka]`
4. **Database Required:** `[Yes/No - PostgreSQL]`
5. **Cache Required:** `[Yes/No - Redis]`
6. **Concurrent Workers:** `[Number of parallel workers, e.g., 10]`
7. **Job Types:** `[List of job types to process]`

---

## 🏗️ Architecture Overview

```
┌────────────────────────────────────────────────────────────────────┐
│                    Golang Worker Architecture                      │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                     Worker Manager                           │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │   Worker   │  │   Worker   │  │   Worker   │            │ │
│  │  │   Pool 1   │  │   Pool 2   │  │   Pool N   │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
│            ↓                ↓                ↓                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                      Job Queue (Channel)                     │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │   Job 1    │  │   Job 2    │  │   Job N    │            │ │
│  │  └────────────┘  └────────────┘  └────────────┘            │ │
│  └──────────────────────────────────────────────────────────────┘ │
│                             ↑                                     │
│                             │                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                      Job Producer                            │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │   Redis    │  │    NATS    │  │  RabbitMQ  │            │ │
│  │  │  Consumer  │  │  Consumer  │  │  Consumer  │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
└────────────┼────────────────┼────────────────┼────────────────────┘
             │                │                │
             ↓                ↓                ↓
    ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
    │    Redis    │  │    NATS     │  │  RabbitMQ   │
    │  (Message   │  │  (Message   │  │  (Message   │
    │   Queue)    │  │   Queue)    │  │   Queue)    │
    └─────────────┘  └─────────────┘  └─────────────┘
```

---

## 📁 Directory Structure

Create the following complete directory structure:

```
services/[service-name]/
├── cmd/
│   └── worker/
│       └── main.go              ← Application entry point
│
├── internal/
│   ├── config/                  ← Configuration
│   │   └── config.go
│   │
│   ├── worker/                  ← Worker implementation
│   │   ├── manager.go           ← Worker pool manager
│   │   ├── worker.go            ← Individual worker
│   │   └── metrics.go           ← Metrics collection
│   │
│   ├── jobs/                    ← Job definitions
│   │   ├── job.go               ← Job interface
│   │   ├── email_job.go         ← Example: Email job
│   │   ├── report_job.go        ← Example: Report job
│   │   └── processor.go         ← Job processor
│   │
│   ├── queue/                   ← Queue consumers
│   │   ├── consumer.go          ← Consumer interface
│   │   ├── redis_consumer.go    ← Redis consumer
│   │   └── nats_consumer.go     ← NATS consumer
│   │
│   ├── services/                ← Business logic
│   │   ├── email_service.go
│   │   └── report_service.go
│   │
│   └── database/                ← Database access
│       └── postgres.go
│
├── pkg/                         ← Public libraries
│   ├── logger/
│   │   └── logger.go
│   └── retry/
│       └── retry.go
│
├── tests/                       ← Tests
│   ├── integration/
│   │   └── worker_test.go
│   └── unit/
│       └── jobs_test.go
│
├── scripts/                     ← Helper scripts
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
    github.com/redis/go-redis/v9 v9.3.0
    github.com/nats-io/nats.go v1.31.0
    github.com/jackc/pgx/v5 v5.5.0
    github.com/rs/zerolog v1.31.0
    github.com/prometheus/client_golang v1.17.0
    github.com/stretchr/testify v1.8.4
)
```

### Step 2: Create cmd/worker/main.go

```go
package main

import (
    "context"
    "log"
    "os"
    "os/signal"
    "syscall"
    "time"

    "github.com/rs/zerolog"

    "github.com/yourorg/[service-name]/internal/config"
    "github.com/yourorg/[service-name]/internal/jobs"
    "github.com/yourorg/[service-name]/internal/queue"
    "github.com/yourorg/[service-name]/internal/services"
    "github.com/yourorg/[service-name]/internal/worker"
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

    // Initialize services
    emailService := services.NewEmailService(logger)
    reportService := services.NewReportService(logger)

    // Create job processor
    processor := jobs.NewProcessor(emailService, reportService, logger)

    // Create consumer based on configuration
    var consumer queue.Consumer
    switch cfg.QueueType {
    case "redis":
        consumer, err = queue.NewRedisConsumer(cfg.RedisURL, logger)
    case "nats":
        consumer, err = queue.NewNATSConsumer(cfg.NATSURL, logger)
    default:
        log.Fatal("Unsupported queue type:", cfg.QueueType)
    }
    if err != nil {
        logger.Fatal().Err(err).Msg("Failed to create consumer")
    }
    defer consumer.Close()

    // Create worker manager
    manager := worker.NewManager(
        cfg.WorkerCount,
        cfg.QueueSize,
        processor,
        logger,
    )

    // Start worker manager
    ctx, cancel := context.WithCancel(context.Background())
    defer cancel()

    go func() {
        logger.Info().
            Int("workers", cfg.WorkerCount).
            Int("queue_size", cfg.QueueSize).
            Msg("Starting worker manager")
        
        if err := manager.Start(ctx); err != nil {
            logger.Fatal().Err(err).Msg("Worker manager failed")
        }
    }()

    // Start consuming jobs
    go func() {
        logger.Info().Msg("Starting job consumer")
        if err := consumer.Consume(ctx, manager.Submit); err != nil {
            logger.Error().Err(err).Msg("Consumer error")
        }
    }()

    // Wait for interrupt signal
    quit := make(chan os.Signal, 1)
    signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
    <-quit

    logger.Info().Msg("Shutting down worker...")

    // Graceful shutdown
    shutdownCtx, shutdownCancel := context.WithTimeout(context.Background(), 30*time.Second)
    defer shutdownCancel()

    if err := manager.Stop(shutdownCtx); err != nil {
        logger.Error().Err(err).Msg("Worker shutdown error")
    }

    logger.Info().Msg("Worker stopped")
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
    WorkerCount int
    QueueSize   int
    QueueType   string
    RedisURL    string
    NATSURL     string
    DatabaseURL string
    Environment string
}

func Load() (*Config, error) {
    workerCount, err := strconv.Atoi(getEnv("WORKER_COUNT", "10"))
    if err != nil {
        return nil, fmt.Errorf("invalid WORKER_COUNT: %w", err)
    }

    queueSize, err := strconv.Atoi(getEnv("QUEUE_SIZE", "100"))
    if err != nil {
        return nil, fmt.Errorf("invalid QUEUE_SIZE: %w", err)
    }

    cfg := &Config{
        WorkerCount: workerCount,
        QueueSize:   queueSize,
        QueueType:   getEnv("QUEUE_TYPE", "redis"),
        RedisURL:    getEnv("REDIS_URL", "redis://localhost:6379"),
        NATSURL:     getEnv("NATS_URL", "nats://localhost:4222"),
        DatabaseURL: getEnv("DATABASE_URL", ""),
        Environment: getEnv("ENVIRONMENT", "development"),
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

### Step 4: Create internal/jobs/job.go

```go
package jobs

import (
    "context"
)

type JobType string

const (
    JobTypeEmail  JobType = "email"
    JobTypeReport JobType = "report"
)

type Job interface {
    Execute(ctx context.Context) error
    Type() JobType
    ID() string
}

type BaseJob struct {
    JobID   string
    JobType JobType
    Payload map[string]interface{}
}

func (j *BaseJob) ID() string {
    return j.JobID
}

func (j *BaseJob) Type() JobType {
    return j.JobType
}
```

### Step 5: Create internal/jobs/email_job.go

```go
package jobs

import (
    "context"
    "fmt"
)

type EmailJob struct {
    BaseJob
    To      string
    Subject string
    Body    string
}

func NewEmailJob(id, to, subject, body string) *EmailJob {
    return &EmailJob{
        BaseJob: BaseJob{
            JobID:   id,
            JobType: JobTypeEmail,
        },
        To:      to,
        Subject: subject,
        Body:    body,
    }
}

func (j *EmailJob) Execute(ctx context.Context) error {
    // Email sending logic here
    fmt.Printf("Sending email to %s: %s\n", j.To, j.Subject)
    return nil
}
```

### Step 6: Create internal/jobs/processor.go

```go
package jobs

import (
    "context"
    "fmt"

    "github.com/rs/zerolog"
    "github.com/yourorg/[service-name]/internal/services"
)

type Processor struct {
    emailService  *services.EmailService
    reportService *services.ReportService
    logger        zerolog.Logger
}

func NewProcessor(
    emailService *services.EmailService,
    reportService *services.ReportService,
    logger zerolog.Logger,
) *Processor {
    return &Processor{
        emailService:  emailService,
        reportService: reportService,
        logger:        logger,
    }
}

func (p *Processor) Process(ctx context.Context, job Job) error {
    p.logger.Info().
        Str("job_id", job.ID()).
        Str("job_type", string(job.Type())).
        Msg("Processing job")

    if err := job.Execute(ctx); err != nil {
        p.logger.Error().
            Err(err).
            Str("job_id", job.ID()).
            Msg("Job execution failed")
        return fmt.Errorf("job execution failed: %w", err)
    }

    p.logger.Info().
        Str("job_id", job.ID()).
        Msg("Job completed successfully")

    return nil
}
```

### Step 7: Create internal/worker/manager.go

```go
package worker

import (
    "context"
    "fmt"
    "sync"
    "time"

    "github.com/rs/zerolog"
    "github.com/yourorg/[service-name]/internal/jobs"
)

type Manager struct {
    workerCount int
    jobQueue    chan jobs.Job
    processor   *jobs.Processor
    logger      zerolog.Logger
    wg          sync.WaitGroup
    metrics     *Metrics
}

func NewManager(
    workerCount int,
    queueSize int,
    processor *jobs.Processor,
    logger zerolog.Logger,
) *Manager {
    return &Manager{
        workerCount: workerCount,
        jobQueue:    make(chan jobs.Job, queueSize),
        processor:   processor,
        logger:      logger,
        metrics:     NewMetrics(),
    }
}

func (m *Manager) Start(ctx context.Context) error {
    m.logger.Info().
        Int("worker_count", m.workerCount).
        Msg("Starting workers")

    for i := 0; i < m.workerCount; i++ {
        m.wg.Add(1)
        go m.worker(ctx, i)
    }

    m.wg.Wait()
    return nil
}

func (m *Manager) worker(ctx context.Context, id int) {
    defer m.wg.Done()

    m.logger.Info().Int("worker_id", id).Msg("Worker started")

    for {
        select {
        case <-ctx.Done():
            m.logger.Info().Int("worker_id", id).Msg("Worker stopping")
            return

        case job, ok := <-m.jobQueue:
            if !ok {
                m.logger.Info().Int("worker_id", id).Msg("Job queue closed")
                return
            }

            m.processJob(ctx, id, job)
        }
    }
}

func (m *Manager) processJob(ctx context.Context, workerID int, job jobs.Job) {
    start := time.Now()
    m.metrics.JobStarted(job.Type())

    err := m.processor.Process(ctx, job)
    duration := time.Since(start)

    if err != nil {
        m.logger.Error().
            Err(err).
            Int("worker_id", workerID).
            Str("job_id", job.ID()).
            Dur("duration", duration).
            Msg("Job failed")
        m.metrics.JobFailed(job.Type())
    } else {
        m.logger.Info().
            Int("worker_id", workerID).
            Str("job_id", job.ID()).
            Dur("duration", duration).
            Msg("Job completed")
        m.metrics.JobCompleted(job.Type(), duration)
    }
}

func (m *Manager) Submit(job jobs.Job) error {
    select {
    case m.jobQueue <- job:
        return nil
    default:
        return fmt.Errorf("job queue is full")
    }
}

func (m *Manager) Stop(ctx context.Context) error {
    m.logger.Info().Msg("Stopping worker manager")

    close(m.jobQueue)

    done := make(chan struct{})
    go func() {
        m.wg.Wait()
        close(done)
    }()

    select {
    case <-done:
        m.logger.Info().Msg("All workers stopped gracefully")
        return nil
    case <-ctx.Done():
        return fmt.Errorf("shutdown timeout exceeded")
    }
}

func (m *Manager) GetMetrics() *Metrics {
    return m.metrics
}
```

### Step 8: Create internal/worker/metrics.go

```go
package worker

import (
    "sync"
    "time"

    "github.com/yourorg/[service-name]/internal/jobs"
)

type Metrics struct {
    mu              sync.RWMutex
    jobsProcessed   map[jobs.JobType]int64
    jobsFailed      map[jobs.JobType]int64
    jobsInProgress  int64
    totalDuration   map[jobs.JobType]time.Duration
}

func NewMetrics() *Metrics {
    return &Metrics{
        jobsProcessed:  make(map[jobs.JobType]int64),
        jobsFailed:     make(map[jobs.JobType]int64),
        totalDuration:  make(map[jobs.JobType]time.Duration),
    }
}

func (m *Metrics) JobStarted(jobType jobs.JobType) {
    m.mu.Lock()
    defer m.mu.Unlock()
    m.jobsInProgress++
}

func (m *Metrics) JobCompleted(jobType jobs.JobType, duration time.Duration) {
    m.mu.Lock()
    defer m.mu.Unlock()
    m.jobsProcessed[jobType]++
    m.jobsInProgress--
    m.totalDuration[jobType] += duration
}

func (m *Metrics) JobFailed(jobType jobs.JobType) {
    m.mu.Lock()
    defer m.mu.Unlock()
    m.jobsFailed[jobType]++
    m.jobsInProgress--
}

func (m *Metrics) GetStats() map[string]interface{} {
    m.mu.RLock()
    defer m.mu.RUnlock()

    return map[string]interface{}{
        "jobs_processed":   m.jobsProcessed,
        "jobs_failed":      m.jobsFailed,
        "jobs_in_progress": m.jobsInProgress,
        "average_duration": m.calculateAverageDuration(),
    }
}

func (m *Metrics) calculateAverageDuration() map[jobs.JobType]time.Duration {
    avg := make(map[jobs.JobType]time.Duration)
    for jobType, total := range m.totalDuration {
        if count := m.jobsProcessed[jobType]; count > 0 {
            avg[jobType] = total / time.Duration(count)
        }
    }
    return avg
}
```

### Step 9: Create internal/queue/redis_consumer.go

```go
package queue

import (
    "context"
    "encoding/json"
    "fmt"

    "github.com/redis/go-redis/v9"
    "github.com/rs/zerolog"
    "github.com/yourorg/[service-name]/internal/jobs"
)

type RedisConsumer struct {
    client *redis.Client
    logger zerolog.Logger
}

func NewRedisConsumer(redisURL string, logger zerolog.Logger) (*RedisConsumer, error) {
    opts, err := redis.ParseURL(redisURL)
    if err != nil {
        return nil, fmt.Errorf("failed to parse redis URL: %w", err)
    }

    client := redis.NewClient(opts)

    if err := client.Ping(context.Background()).Err(); err != nil {
        return nil, fmt.Errorf("failed to connect to redis: %w", err)
    }

    return &RedisConsumer{
        client: client,
        logger: logger,
    }, nil
}

func (c *RedisConsumer) Consume(ctx context.Context, handler func(jobs.Job) error) error {
    for {
        select {
        case <-ctx.Done():
            return ctx.Err()

        default:
            result, err := c.client.BLPop(ctx, 0, "jobs:queue").Result()
            if err != nil {
                if err == redis.Nil {
                    continue
                }
                return fmt.Errorf("failed to pop from queue: %w", err)
            }

            if len(result) < 2 {
                continue
            }

            jobData := result[1]
            job, err := c.parseJob(jobData)
            if err != nil {
                c.logger.Error().Err(err).Msg("Failed to parse job")
                continue
            }

            if err := handler(job); err != nil {
                c.logger.Error().Err(err).Str("job_id", job.ID()).Msg("Failed to submit job")
            }
        }
    }
}

func (c *RedisConsumer) parseJob(data string) (jobs.Job, error) {
    var baseJob jobs.BaseJob
    if err := json.Unmarshal([]byte(data), &baseJob); err != nil {
        return nil, fmt.Errorf("failed to unmarshal job: %w", err)
    }

    switch baseJob.JobType {
    case jobs.JobTypeEmail:
        var emailJob jobs.EmailJob
        if err := json.Unmarshal([]byte(data), &emailJob); err != nil {
            return nil, fmt.Errorf("failed to unmarshal email job: %w", err)
        }
        return &emailJob, nil
    default:
        return nil, fmt.Errorf("unknown job type: %s", baseJob.JobType)
    }
}

func (c *RedisConsumer) Close() error {
    return c.client.Close()
}
```

### Step 10: Create internal/services/email_service.go

```go
package services

import (
    "github.com/rs/zerolog"
)

type EmailService struct {
    logger zerolog.Logger
}

func NewEmailService(logger zerolog.Logger) *EmailService {
    return &EmailService{
        logger: logger,
    }
}

func (s *EmailService) SendEmail(to, subject, body string) error {
    s.logger.Info().
        Str("to", to).
        Str("subject", subject).
        Msg("Sending email")

    // TODO: Implement actual email sending logic
    
    return nil
}
```

### Step 11: Create internal/services/report_service.go

```go
package services

import (
    "github.com/rs/zerolog"
)

type ReportService struct {
    logger zerolog.Logger
}

func NewReportService(logger zerolog.Logger) *ReportService {
    return &ReportService{
        logger: logger,
    }
}

func (s *ReportService) GenerateReport(reportType string, params map[string]interface{}) error {
    s.logger.Info().
        Str("report_type", reportType).
        Msg("Generating report")

    // TODO: Implement actual report generation logic
    
    return nil
}
```

### Step 12: Create Dockerfile

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
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o worker ./cmd/worker

# Runtime stage
FROM alpine:latest

RUN apk --no-cache add ca-certificates

WORKDIR /root/

# Copy binary from builder
COPY --from=builder /app/worker .

CMD ["./worker"]
```

### Step 13: Create docker-compose.yml

```yaml
version: '3.8'

services:
  [service-name]:
    build: .
    environment:
      - WORKER_COUNT=10
      - QUEUE_SIZE=100
      - QUEUE_TYPE=redis
      - REDIS_URL=redis://redis:6379
      - NATS_URL=nats://nats:4222
      - DATABASE_URL=postgres://user:password@postgres:5432/dbname?sslmode=disable
      - ENVIRONMENT=development
    depends_on:
      - redis
      - nats
      - postgres
    networks:
      - microservices

  redis:
    image: redis:7-alpine
    networks:
      - microservices

  nats:
    image: nats:2-alpine
    networks:
      - microservices

  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: dbname
    networks:
      - microservices

networks:
  microservices:
    external: true
```

### Step 14: Create .devcontainer/[service-name]-container/devcontainer.json

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
        "go.lintTool": "golangci-lint"
      }
    }
  },
  "postCreateCommand": "go mod download"
}
```

### Step 15: Create Makefile

```makefile
.PHONY: build run test lint

build:
	go build -o bin/worker cmd/worker/main.go

run:
	go run cmd/worker/main.go

test:
	go test -v -race -coverprofile=coverage.out ./...

lint:
	golangci-lint run

docker-build:
	docker build -t [service-name]:latest .

docker-run:
	docker-compose up -d
```

### Step 16: Create .env.example

```bash
WORKER_COUNT=10
QUEUE_SIZE=100
QUEUE_TYPE=redis
REDIS_URL=redis://localhost:6379
NATS_URL=nats://localhost:4222
DATABASE_URL=postgres://user:password@localhost:5432/dbname?sslmode=disable
ENVIRONMENT=development
```

### Step 17: Create .gitignore

```
# Binaries
bin/
*.exe
*.exe~

# Test coverage
*.out
coverage.html

# Environment files
.env
.env.local

# IDE
.idea/
.vscode/
*.swp

# OS
.DS_Store
```

### Step 18: Create README.md

```markdown
# [Service Name]

[Brief description of the worker service purpose]

## Features

- Worker pool with configurable concurrency
- Multiple job types support
- Redis/NATS queue integration
- Graceful shutdown
- Metrics collection
- Structured logging
- Docker ready

## Prerequisites

- Go 1.21+
- Docker & Docker Compose
- Redis or NATS

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

3. Start the worker:
```bash
make run
```

### Docker Development

```bash
docker-compose up -d
```

## Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| WORKER_COUNT | Number of concurrent workers | 10 |
| QUEUE_SIZE | Job queue buffer size | 100 |
| QUEUE_TYPE | Queue type (redis/nats) | redis |
| REDIS_URL | Redis connection string | redis://localhost:6379 |
| NATS_URL | NATS connection string | nats://localhost:4222 |

## Job Types

### Email Job
Sends email notifications.

### Report Job
Generates reports.

## Testing

```bash
make test
```

## Project Structure

```
├── cmd/worker/       # Application entry point
├── internal/         # Private application code
│   ├── config/       # Configuration
│   ├── worker/       # Worker pool implementation
│   ├── jobs/         # Job definitions
│   ├── queue/        # Queue consumers
│   └── services/     # Business logic
└── tests/            # Test files
```

## License

[Your License]
```

---

## ✅ Completion Checklist

- [ ] All files created in correct directory structure
- [ ] go.mod and dependencies configured
- [ ] Worker pool manager implemented
- [ ] Job interface and types defined
- [ ] Queue consumer (Redis/NATS) implemented
- [ ] Business logic services created
- [ ] Metrics collection setup
- [ ] Graceful shutdown implemented
- [ ] Dockerfile with multi-stage build
- [ ] docker-compose.yml configured
- [ ] DevContainer configuration
- [ ] Makefile with common commands
- [ ] .env.example with all variables
- [ ] .gitignore configured
- [ ] README.md with documentation
- [ ] Service added to root docker-compose files
- [ ] Worker tested locally
- [ ] Integration tests passing

---

**Next Steps:**
1. Implement specific job types for your use case
2. Add retry logic with exponential backoff
3. Implement dead letter queue handling
4. Add Prometheus metrics endpoint
5. Set up monitoring and alerting
6. Configure CI/CD pipeline
