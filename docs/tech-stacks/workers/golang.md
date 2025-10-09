# Go Native Worker - High-Performance Background Processing

Lightweight, high-throughput worker built with Go's goroutines and channels for maximum performance.

---

## Overview

**Naming Convention:** `worker-golang-{purpose}`

**Examples:**
- `worker-golang-indexer` - High-speed indexing worker
- `worker-golang-sync` - Data synchronization worker
- `worker-golang-scraper` - Web scraping worker

**Go Native Worker** leverages Go's concurrency primitives (goroutines, channels) to build high-performance, low-resource workers without heavy dependencies. Ideal for throughput-critical scenarios.

---

## Technology Stack

```
┌─────────────────────────────────────────────┐
│ Runtime & Language                          │
│  - Go 1.21+                                 │
│  - goroutines + channels (concurrency)      │
│  - context package (cancellation)           │
│  - sync package (synchronization)           │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Queue & Messaging                           │
│  - Redis (redis/go-redis)                   │
│  - NATS JetStream                           │
│  - Kafka (segmentio/kafka-go)               │
│  - RabbitMQ (streadway/amqp)               │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Worker Pool Pattern                         │
│  - sync.WaitGroup (coordination)            │
│  - Worker pool with channels                │
│  - Job queue (buffered channel)             │
│  - Graceful shutdown                        │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Monitoring & Logging                        │
│  - zerolog (structured logging)             │
│  - prometheus/client_golang (metrics)       │
│  - pprof (profiling)                        │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Testing                                     │
│  - testing package (standard lib)           │
│  - testify (assertions)                     │
│  - gomock (mocking)                         │
└─────────────────────────────────────────────┘
```

---

## Features

### Core Features
- ✅ **Goroutine-based** - Lightweight concurrency (1000s of workers)
- ✅ **Channel-based queue** - Efficient job distribution
- ✅ **Worker pool pattern** - Configurable concurrency
- ✅ **Graceful shutdown** - Complete in-flight jobs before exit
- ✅ **Context cancellation** - Timeout and cancellation support
- ✅ **No external dependencies** - Pure Go (optional brokers)
- ✅ **High throughput** - 10K-50K jobs/sec per instance
- ✅ **Low memory** - 15-50 MB per worker instance

### Performance Characteristics
- **Startup time:** < 100ms
- **Memory per worker:** 15-50 MB
- **Throughput:** 15K-50K jobs/sec (single instance)
- **Latency:** < 1ms per job (simple tasks)
- **Scalability:** Horizontal (add more instances)

---

## Ideal Use Cases

### ✅ Perfect For:
1. **High-Throughput Indexing**
   - Search index updates
   - Database indexing
   - Cache warming

2. **Real-Time Data Synchronization**
   - Database replication
   - Cache invalidation
   - Data mirroring

3. **Log Processing & Aggregation**
   - Parse and store logs
   - Metrics collection
   - Event aggregation

4. **Web Scraping & Crawling**
   - Concurrent HTTP requests
   - Page parsing
   - Data extraction

5. **Event Processing**
   - Kafka/NATS consumers
   - Event transformation
   - Event routing

6. **System-Level Workers**
   - File processing
   - System monitoring
   - Resource cleanup

### ❌ Not Ideal For:
- ❌ Python ecosystem (pandas, ML) → Use Celery
- ❌ Node.js ecosystem → Use BullMQ
- ❌ Complex workflow orchestration → Use Celery Canvas
- ❌ UI monitoring (no built-in UI) → Use BullMQ/Celery

---

## Project Structure

```
worker-golang-{purpose}/
├── cmd/
│   └── worker/
│       └── main.go           # Entry point
├── internal/
│   ├── worker/
│   │   ├── worker.go         # Worker pool implementation
│   │   ├── job.go            # Job types
│   │   └── processor.go      # Job processing logic
│   ├── queue/
│   │   ├── redis.go          # Redis queue implementation
│   │   ├── nats.go           # NATS queue implementation
│   │   └── interface.go      # Queue interface
│   ├── config/
│   │   └── config.go         # Configuration
│   └── metrics/
│       └── metrics.go        # Prometheus metrics
├── pkg/
│   └── logger/
│       └── logger.go         # Logging setup
├── tests/
│   ├── worker_test.go
│   └── integration_test.go
├── go.mod
├── go.sum
├── Dockerfile
└── README.md
```

---

## Code Examples

### Job Definition

```go
// internal/worker/job.go
package worker

import "context"

type Job struct {
    ID      string
    Type    string
    Payload map[string]interface{}
}

type Processor interface {
    Process(ctx context.Context, job Job) error
}
```

### Worker Pool Implementation

```go
// internal/worker/worker.go
package worker

import (
    "context"
    "sync"
    "time"
)

type Pool struct {
    workers   int
    jobs      chan Job
    processor Processor
    wg        sync.WaitGroup
    logger    Logger
    metrics   Metrics
}

func NewPool(workers int, processor Processor, logger Logger, metrics Metrics) *Pool {
    return &Pool{
        workers:   workers,
        jobs:      make(chan Job, workers*2), // Buffered channel
        processor: processor,
        logger:    logger,
        metrics:   metrics,
    }
}

func (p *Pool) Start(ctx context.Context) {
    p.logger.Info("Starting worker pool", "workers", p.workers)
    
    for i := 0; i < p.workers; i++ {
        p.wg.Add(1)
        go p.worker(ctx, i)
    }
}

func (p *Pool) worker(ctx context.Context, id int) {
    defer p.wg.Done()
    
    p.logger.Info("Worker started", "worker_id", id)
    
    for {
        select {
        case <-ctx.Done():
            p.logger.Info("Worker stopping", "worker_id", id)
            return
        case job, ok := <-p.jobs:
            if !ok {
                return
            }
            
            p.processJob(ctx, id, job)
        }
    }
}

func (p *Pool) processJob(ctx context.Context, workerID int, job Job) {
    start := time.Now()
    
    p.logger.Info("Processing job", 
        "worker_id", workerID, 
        "job_id", job.ID, 
        "job_type", job.Type,
    )
    
    // Process with timeout
    ctx, cancel := context.WithTimeout(ctx, 30*time.Second)
    defer cancel()
    
    err := p.processor.Process(ctx, job)
    duration := time.Since(start)
    
    if err != nil {
        p.logger.Error("Job failed", 
            "job_id", job.ID, 
            "error", err, 
            "duration", duration,
        )
        p.metrics.JobFailed(job.Type, duration)
    } else {
        p.logger.Info("Job completed", 
            "job_id", job.ID, 
            "duration", duration,
        )
        p.metrics.JobCompleted(job.Type, duration)
    }
}

func (p *Pool) Submit(job Job) {
    p.jobs <- job
}

func (p *Pool) Shutdown() {
    p.logger.Info("Shutting down worker pool")
    close(p.jobs)
    p.wg.Wait()
    p.logger.Info("Worker pool stopped")
}
```

### Redis Queue Implementation

```go
// internal/queue/redis.go
package queue

import (
    "context"
    "encoding/json"
    "time"
    
    "github.com/redis/go-redis/v9"
    "worker/internal/worker"
)

type RedisQueue struct {
    client *redis.Client
    key    string
}

func NewRedisQueue(addr, password, key string) (*RedisQueue, error) {
    client := redis.NewClient(&redis.Options{
        Addr:     addr,
        Password: password,
        DB:       0,
    })
    
    // Test connection
    ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
    defer cancel()
    
    if err := client.Ping(ctx).Err(); err != nil {
        return nil, err
    }
    
    return &RedisQueue{
        client: client,
        key:    key,
    }, nil
}

func (q *RedisQueue) Pop(ctx context.Context) (*worker.Job, error) {
    // BRPOP with timeout
    result, err := q.client.BRPop(ctx, 5*time.Second, q.key).Result()
    if err != nil {
        if err == redis.Nil {
            return nil, nil // No job available
        }
        return nil, err
    }
    
    // result[0] is the key, result[1] is the value
    var job worker.Job
    if err := json.Unmarshal([]byte(result[1]), &job); err != nil {
        return nil, err
    }
    
    return &job, nil
}

func (q *RedisQueue) Push(ctx context.Context, job worker.Job) error {
    data, err := json.Marshal(job)
    if err != nil {
        return err
    }
    
    return q.client.LPush(ctx, q.key, data).Err()
}

func (q *RedisQueue) Close() error {
    return q.client.Close()
}
```

### Job Processor Example

```go
// internal/worker/processor.go
package worker

import (
    "context"
    "fmt"
    "time"
)

type IndexProcessor struct {
    // Dependencies (database, search engine, etc.)
}

func NewIndexProcessor() *IndexProcessor {
    return &IndexProcessor{}
}

func (p *IndexProcessor) Process(ctx context.Context, job Job) error {
    switch job.Type {
    case "index_document":
        return p.indexDocument(ctx, job)
    case "delete_document":
        return p.deleteDocument(ctx, job)
    default:
        return fmt.Errorf("unknown job type: %s", job.Type)
    }
}

func (p *IndexProcessor) indexDocument(ctx context.Context, job Job) error {
    docID, ok := job.Payload["document_id"].(string)
    if !ok {
        return fmt.Errorf("missing document_id")
    }
    
    // Fetch document from database
    doc, err := p.fetchDocument(ctx, docID)
    if err != nil {
        return err
    }
    
    // Index in search engine
    if err := p.indexInSearchEngine(ctx, doc); err != nil {
        return err
    }
    
    return nil
}

func (p *IndexProcessor) deleteDocument(ctx context.Context, job Job) error {
    docID, ok := job.Payload["document_id"].(string)
    if !ok {
        return fmt.Errorf("missing document_id")
    }
    
    // Delete from search engine
    if err := p.deleteFromSearchEngine(ctx, docID); err != nil {
        return err
    }
    
    return nil
}

func (p *IndexProcessor) fetchDocument(ctx context.Context, id string) (interface{}, error) {
    // Database query
    time.Sleep(10 * time.Millisecond) // Simulate DB query
    return map[string]string{"id": id, "title": "Document"}, nil
}

func (p *IndexProcessor) indexInSearchEngine(ctx context.Context, doc interface{}) error {
    // Search engine API call
    time.Sleep(5 * time.Millisecond) // Simulate API call
    return nil
}

func (p *IndexProcessor) deleteFromSearchEngine(ctx context.Context, id string) error {
    // Search engine API call
    time.Sleep(5 * time.Millisecond) // Simulate API call
    return nil
}
```

### Main Entry Point

```go
// cmd/worker/main.go
package main

import (
    "context"
    "os"
    "os/signal"
    "syscall"
    
    "worker/internal/config"
    "worker/internal/queue"
    "worker/internal/worker"
    "worker/pkg/logger"
)

func main() {
    // Load configuration
    cfg := config.Load()
    
    // Setup logger
    log := logger.New(cfg.LogLevel)
    
    // Setup metrics
    metrics := NewMetrics()
    
    // Create queue
    q, err := queue.NewRedisQueue(cfg.RedisAddr, cfg.RedisPassword, cfg.QueueKey)
    if err != nil {
        log.Fatal("Failed to connect to Redis", "error", err)
    }
    defer q.Close()
    
    // Create processor
    processor := worker.NewIndexProcessor()
    
    // Create worker pool
    pool := worker.NewPool(cfg.Workers, processor, log, metrics)
    
    // Start worker pool
    ctx, cancel := context.WithCancel(context.Background())
    pool.Start(ctx)
    
    // Start consuming jobs from queue
    go consumeJobs(ctx, q, pool, log)
    
    // Wait for shutdown signal
    sigCh := make(chan os.Signal, 1)
    signal.Notify(sigCh, os.Interrupt, syscall.SIGTERM)
    <-sigCh
    
    log.Info("Received shutdown signal")
    
    // Graceful shutdown
    cancel()
    pool.Shutdown()
    
    log.Info("Worker stopped")
}

func consumeJobs(ctx context.Context, q *queue.RedisQueue, pool *worker.Pool, log logger.Logger) {
    for {
        select {
        case <-ctx.Done():
            return
        default:
            job, err := q.Pop(ctx)
            if err != nil {
                log.Error("Failed to pop job", "error", err)
                continue
            }
            
            if job == nil {
                continue // No job available
            }
            
            pool.Submit(*job)
        }
    }
}
```

### Metrics

```go
// internal/metrics/metrics.go
package metrics

import (
    "time"
    
    "github.com/prometheus/client_golang/prometheus"
    "github.com/prometheus/client_golang/prometheus/promauto"
)

type Metrics struct {
    jobsProcessed *prometheus.CounterVec
    jobDuration   *prometheus.HistogramVec
}

func New() *Metrics {
    return &Metrics{
        jobsProcessed: promauto.NewCounterVec(
            prometheus.CounterOpts{
                Name: "worker_jobs_processed_total",
                Help: "Total number of processed jobs",
            },
            []string{"job_type", "status"},
        ),
        jobDuration: promauto.NewHistogramVec(
            prometheus.HistogramOpts{
                Name:    "worker_job_duration_seconds",
                Help:    "Job processing duration",
                Buckets: prometheus.DefBuckets,
            },
            []string{"job_type"},
        ),
    }
}

func (m *Metrics) JobCompleted(jobType string, duration time.Duration) {
    m.jobsProcessed.WithLabelValues(jobType, "success").Inc()
    m.jobDuration.WithLabelValues(jobType).Observe(duration.Seconds())
}

func (m *Metrics) JobFailed(jobType string, duration time.Duration) {
    m.jobsProcessed.WithLabelValues(jobType, "failure").Inc()
    m.jobDuration.WithLabelValues(jobType).Observe(duration.Seconds())
}
```

---

## Testing

### Unit Test

```go
// tests/worker_test.go
package tests

import (
    "context"
    "testing"
    "time"
    
    "github.com/stretchr/testify/assert"
    "worker/internal/worker"
)

type MockProcessor struct {
    processFunc func(ctx context.Context, job worker.Job) error
}

func (m *MockProcessor) Process(ctx context.Context, job worker.Job) error {
    if m.processFunc != nil {
        return m.processFunc(ctx, job)
    }
    return nil
}

func TestWorkerPool(t *testing.T) {
    // Create mock processor
    var processed int
    processor := &MockProcessor{
        processFunc: func(ctx context.Context, job worker.Job) error {
            processed++
            time.Sleep(10 * time.Millisecond)
            return nil
        },
    }
    
    // Create worker pool
    pool := worker.NewPool(5, processor, &mockLogger{}, &mockMetrics{})
    
    ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
    defer cancel()
    
    pool.Start(ctx)
    
    // Submit jobs
    for i := 0; i < 10; i++ {
        pool.Submit(worker.Job{
            ID:   fmt.Sprintf("job-%d", i),
            Type: "test",
        })
    }
    
    // Wait for completion
    time.Sleep(500 * time.Millisecond)
    pool.Shutdown()
    
    assert.Equal(t, 10, processed)
}
```

---

## Deployment

### Docker

```dockerfile
# Dockerfile
FROM golang:1.21-alpine AS builder

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -o worker cmd/worker/main.go

FROM alpine:latest

RUN apk --no-cache add ca-certificates

WORKDIR /app

COPY --from=builder /app/worker .

CMD ["./worker"]
```

### Docker Compose

```yaml
version: '3.8'
services:
  worker:
    build: .
    environment:
      - REDIS_ADDR=redis:6379
      - WORKERS=10
      - LOG_LEVEL=info
    depends_on:
      - redis
    restart: unless-stopped
    deploy:
      replicas: 3  # Scale horizontally

  redis:
    image: redis:7-alpine
    volumes:
      - redis-data:/data
    restart: unless-stopped

volumes:
  redis-data:
```

---

## Best Practices

### ✅ Do:
- Use **context.Context** for cancellation and timeouts
- Implement **graceful shutdown** (finish in-flight jobs)
- Use **buffered channels** for job queue
- Set **worker count** based on CPU cores
- Use **structured logging** (zerolog, zap)
- Implement **Prometheus metrics**
- Use **WaitGroup** for worker coordination
- Handle **panics** in goroutines
- Use **dependency injection** for testability

### ❌ Don't:
- Don't **panic** without recovery
- Don't use **unbuffered channels** for high-throughput queues
- Don't **block** on channel operations without select/timeout
- Don't **ignore context cancellation**
- Don't **forget to close channels**
- Don't create **goroutine leaks**

---

## Related Documentation

- [Workers Overview](./README.md)
- [BullMQ Worker](./bullmq.md) - Node.js alternative
- [Celery Worker](./celery.md) - Python alternative
- [Naming Conventions](../NAMING-CONVENTIONS.md)

---

## Reference Files

- **Instruction File:** `service-worker-golang.instructions.md`
- **Scaffold Prompt:** `scaffold-worker-golang-service.md`

---

## Next Steps

1. **Choose queue** (Redis for simplicity, NATS for performance)
2. **Implement worker pool** with goroutines and channels
3. **Define job types** and processor interface
4. **Add metrics** (Prometheus)
5. **Write tests** (unit and integration)
6. **Deploy with Docker** and scale horizontally
7. **Monitor performance** and tune worker count
