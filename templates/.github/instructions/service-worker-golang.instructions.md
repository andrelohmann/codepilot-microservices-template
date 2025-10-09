---
applyTo:
  - "services/worker-golang-*/**"
---

# Golang Worker Service Instructions

Apply to background job processing services using Go with goroutines and channels.

## Technology Stack

- **Language:** Go 1.21+
- **Concurrency:** Goroutines and channels
- **Queue:** Redis (optional), In-memory channels
- **Logging:** zerolog
- **Metrics:** Prometheus (optional)
- **Configuration:** viper or godotenv

## Project Structure

```
services/worker-golang-{purpose}/
├── cmd/worker/main.go
├── internal/
│   ├── config/
│   ├── jobs/
│   ├── handlers/
│   ├── queue/
│   └── services/
├── pkg/
├── go.mod
├── Dockerfile
└── README.md
```

## Core Patterns

**Worker Pool:**
- Use `sync.WaitGroup` for worker lifecycle management
- Create fixed-size worker pools (default: NumCPU workers)
- Worker function: `func worker(id int, jobs <-chan Job, results chan<- Result)`
- Graceful shutdown with context cancellation

**Job Processing:**
- Define job interface: `type Job interface { Execute(ctx context.Context) error }`
- Implement retry logic with exponential backoff
- Max retries configurable per job type (default: 3)
- Job timeout enforcement via `context.WithTimeout`

**Channel Usage:**
- Buffered channels for job queue (buffer size: 100-1000)
- Unbuffered channels for results
- Close channels in sender, not receiver
- Use `select` with `context.Done()` for cancellation

**Error Handling:**
- Wrap errors with context: `fmt.Errorf("job failed: %w", err)`
- Log errors with job ID and attempt number
- Failed jobs sent to dead letter queue after max retries
- Panic recovery in each worker goroutine

**Graceful Shutdown:**
- Listen for SIGINT/SIGTERM signals
- Cancel context to stop accepting new jobs
- Wait for in-flight jobs to complete (timeout: 30s)
- Flush metrics and close connections

**Queue Integration:**
- Redis Pub/Sub for distributed workers (optional)
- In-memory channels for single-instance workers
- Job serialization using JSON or Protocol Buffers
- Acknowledge jobs only after successful processing

**Concurrency Control:**
- Use `sync.Mutex` for shared state protection
- Rate limiting with `golang.org/x/time/rate`
- Semaphore pattern for resource limiting
- Atomic operations for counters (`sync/atomic`)

**Metrics & Monitoring:**
- Track jobs processed, failed, in-flight
- Measure job execution duration
- Expose `/metrics` endpoint for Prometheus
- Health check endpoint: `/health`

## Job Types

**Example Job Structure:**
```go
type EmailJob struct {
    ID        string
    To        string
    Subject   string
    Body      string
    Retries   int
    MaxRetries int
}

func (j *EmailJob) Execute(ctx context.Context) error {
    // Implementation
}
```

## Configuration

Required:
- `WORKER_COUNT` (default: runtime.NumCPU())
- `QUEUE_SIZE` (default: 1000)
- `MAX_RETRIES` (default: 3)
- `SHUTDOWN_TIMEOUT` (default: 30s)

Optional:
- `REDIS_URL` (for distributed queuing)
- `METRICS_PORT` (default: 9090)
- `LOG_LEVEL` (default: info)

## Testing Standards

- Test worker pool startup/shutdown
- Test job execution with timeouts
- Test retry logic and backoff
- Mock external dependencies
- Benchmark concurrent job processing

## Performance Optimizations

- Pre-allocate worker goroutines at startup
- Use buffered channels sized to workload
- Profile with pprof for bottlenecks
- Implement job batching where applicable
- Consider sync.Pool for frequent allocations

## Security Requirements

- Validate job payloads before execution
- Sanitize external inputs
- Use TLS for Redis connections
- Authenticate metrics endpoints
- Limit resource consumption per job

## Anti-Patterns

❌ Creating goroutines without cleanup  
❌ Unbounded goroutine spawning  
❌ Ignoring context cancellation  
❌ Deadlocks from improper channel usage  
❌ Not closing channels  
❌ Race conditions on shared state  
❌ Missing panic recovery in goroutines  
❌ Blocking operations without timeouts  

## Dependencies

Required:
- `github.com/rs/zerolog`
- Standard library (`context`, `sync`, `time`)

Optional:
- `github.com/go-redis/redis/v9` (distributed queue)
- `github.com/prometheus/client_golang` (metrics)
- `golang.org/x/time/rate` (rate limiting)
- `github.com/spf13/viper` (configuration)

## Lifecycle Hooks

- `OnStart()` - Initialize connections
- `OnShutdown()` - Cleanup resources
- `OnJobStart()` - Per-job setup
- `OnJobComplete()` - Per-job cleanup
- `OnJobError()` - Error handling
