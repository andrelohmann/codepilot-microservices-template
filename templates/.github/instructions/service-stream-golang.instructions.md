---
applyTo: "**/stream-golang-*/**"
description: Golang real-time stream processing with Kafka, NATS, or RabbitMQ integration
---

# Golang Stream Processing Service Instructions

Apply to real-time data stream processing services using Go.

**Reference**: [Complete Golang Stream Tech Stack Documentation](../../../docs/tech-stacks/streams/golang.md)  
**Scaffold**: Use the `scaffold-stream-golang-service.prompt.md` for new services

## Project Structure

```
services/stream-golang-{purpose}/
├── cmd/processor/main.go
├── internal/
│   ├── config/
│   ├── consumers/
│   ├── processors/
│   ├── producers/
│   └── state/
└── go.mod
```

## Quick Reference Patterns

### 1. Kafka Consumer

```go
config := kafka.ReaderConfig{
    Brokers:        []string{"kafka:9092"},
    Topic:          "events",
    GroupID:        "processor-group",
    MinBytes:       10e3,
    MaxBytes:       10e6,
    CommitInterval: time.Second,
}
reader := kafka.NewReader(config)
defer reader.Close()

for {
    msg, err := reader.ReadMessage(ctx)
    if err != nil {
        break
    }
    if err := ProcessMessage(ctx, msg); err != nil {
        // Send to DLQ
    }
    // Auto-commit
}
```

### 2. Message Processor with Worker Pool

```go
func ProcessStream(ctx context.Context, reader *kafka.Reader) {
    messages := make(chan kafka.Message, 100)
    var wg sync.WaitGroup
    
    // Worker pool
    for i := 0; i < runtime.NumCPU(); i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            for msg := range messages {
                if err := ProcessMessage(ctx, msg); err != nil {
                    log.Error().Err(err).Msg("processing failed")
                }
            }
        }()
    }
    
    // Consumer
    for {
        msg, err := reader.FetchMessage(ctx)
        if err != nil {
            break
        }
        select {
        case messages <- msg:
            reader.CommitMessages(ctx, msg)
        case <-ctx.Done():
            close(messages)
            wg.Wait()
            return
        }
    }
}
```

### 3. Windowed Aggregation

```go
type Window struct {
    Start    time.Time
    End      time.Time
    Messages []Message
    Count    int
}

// 1-minute tumbling windows
windowDuration := time.Minute

func ProcessWindowed(ctx context.Context) {
    windows := make(map[time.Time]*Window)
    ticker := time.NewTicker(windowDuration)
    
    for {
        select {
        case msg := <-messages:
            windowStart := msg.Timestamp.Truncate(windowDuration)
            if _, exists := windows[windowStart]; !exists {
                windows[windowStart] = &Window{Start: windowStart, End: windowStart.Add(windowDuration)}
            }
            windows[windowStart].Messages = append(windows[windowStart].Messages, msg)
            windows[windowStart].Count++
            
        case <-ticker.C:
            // Flush completed windows
            now := time.Now().Truncate(windowDuration)
            for start, window := range windows {
                if start.Before(now) {
                    FlushWindow(window)
                    delete(windows, start)
                }
            }
        }
    }
}
```

### 4. NATS JetStream

```go
js, _ := nc.JetStream()
sub, _ := js.PullSubscribe("events", "processor", 
    nats.Durable("processor-durable"),
    nats.AckWait(30*time.Second),
    nats.MaxDeliver(3),
)

for {
    msgs, _ := sub.Fetch(10, nats.MaxWait(5*time.Second))
    for _, msg := range msgs {
        if err := ProcessMessage(ctx, msg); err == nil {
            msg.Ack()
        } else {
            msg.Nak()
        }
    }
}
```

## Code Style

- Consumer goroutine per partition
- Worker pool for parallel processing
- Buffered channels between stages
- Context-based cancellation for graceful shutdown
- Idempotent processing (deduplicate by message ID)
- Manual offset commits for exactly-once semantics
- Dead letter queue for failed messages
- Exponential backoff for retries
- State in Redis for distributed processing
- Time-based (tumbling/sliding) or count-based windowing
- Prometheus metrics (messages processed, lag, latency)
- Batch consumption for throughput

## Stream Patterns

**Fan-out**: Single input → multiple processors  
**Fan-in**: Multiple inputs → single processor  
**Pipeline**: Chained processing stages  
**Windowing**: Time or count-based aggregation  
**Exactly-Once**: Transactional producers + idempotency keys

## Anti-Patterns

❌ Blocking operations in processing loop  
❌ Not handling consumer rebalancing  
❌ Losing messages on error  
❌ Not committing offsets  
❌ Processing messages out of order within partition  
❌ Not implementing backpressure  
❌ Unbounded state growth  
❌ Not monitoring consumer lag
