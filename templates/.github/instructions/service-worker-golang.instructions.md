---
applyTo:
  - "services/worker-golang-*/**"
---

# Golang Worker Service Instructions

Apply to background job processing services using Go with goroutines and channels.

**Reference**: [Complete Golang Worker Tech Stack Documentation](../../../docs/tech-stacks/workers/golang.md)  
**Scaffold**: Use the `scaffold-worker-golang-service.prompt.md` for new services

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
└── go.mod
```

## Quick Reference Patterns

### 1. Worker Pool

```go
func worker(id int, jobs <-chan Job, results chan<- Result, wg *sync.WaitGroup) {
    defer wg.Done()
    defer func() {
        if r := recover(); r != nil {
            log.Error().Msgf("worker %d panic: %v", id, r)
        }
    }()
    
    for job := range jobs {
        ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
        if err := job.Execute(ctx); err != nil {
            results <- Result{JobID: job.ID, Error: err}
        }
        cancel()
    }
}
```

### 2. Job with Retry

```go
type EmailJob struct {
    ID         string
    To         string
    Subject    string
    Body       string
    Retries    int
    MaxRetries int
}

func (j *EmailJob) Execute(ctx context.Context) error {
    for j.Retries <= j.MaxRetries {
        select {
        case <-ctx.Done():
            return ctx.Err()
        default:
            if err := sendEmail(j.To, j.Subject, j.Body); err != nil {
                j.Retries++
                time.Sleep(time.Duration(j.Retries) * time.Second) // Backoff
                continue
            }
            return nil
        }
    }
    return fmt.Errorf("max retries exceeded")
}
```

### 3. Graceful Shutdown

```go
func main() {
    ctx, cancel := context.WithCancel(context.Background())
    defer cancel()
    
    jobs := make(chan Job, 1000)
    results := make(chan Result, 100)
    var wg sync.WaitGroup
    
    // Start workers
    for i := 0; i < runtime.NumCPU(); i++ {
        wg.Add(1)
        go worker(i, jobs, results, &wg)
    }
    
    // Graceful shutdown
    sigChan := make(chan os.Signal, 1)
    signal.Notify(sigChan, syscall.SIGINT, syscall.SIGTERM)
    <-sigChan
    
    log.Info().Msg("Shutting down...")
    close(jobs)
    wg.Wait()
    close(results)
}
```

### 4. Rate Limiting

```go
import "golang.org/x/time/rate"

limiter := rate.NewLimiter(rate.Limit(10), 100) // 10 jobs/sec, burst 100

func worker(id int, jobs <-chan Job, limiter *rate.Limiter) {
    for job := range jobs {
        limiter.Wait(context.Background())
        job.Execute(context.Background())
    }
}
```

## Code Style

- Use `sync.WaitGroup` for worker lifecycle
- Fixed-size worker pools (default: `runtime.NumCPU()`)
- Buffered channels for job queue (100-1000)
- Always recover from panics in goroutines
- Context cancellation for graceful shutdown
- Exponential backoff for retries

## Anti-Patterns

❌ Creating goroutines without cleanup  
❌ Unbounded goroutine spawning  
❌ Ignoring context cancellation  
❌ Not closing channels  
❌ Race conditions on shared state  
❌ Missing panic recovery in goroutines  
❌ Blocking operations without timeouts
