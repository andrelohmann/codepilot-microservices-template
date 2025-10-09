---
applyTo:
  - "services/stream-golang-*/**"
---

# Golang Stream Processing Service Instructions

Apply to real-time data stream processing services using Go.

## Technology Stack

- **Language:** Go 1.21+
- **Streaming:** Kafka, NATS, or Redis Streams
- **Processing:** Goroutines with channels
- **State:** In-memory or Redis
- **Metrics:** Prometheus
- **Logging:** zerolog

## Project Structure

```
services/stream-golang-{purpose}/
├── cmd/processor/main.go
├── internal/
│   ├── config/
│   ├── consumers/
│   ├── processors/
│   ├── producers/
│   ├── state/
│   └── metrics/
├── pkg/
├── go.mod
├── Dockerfile
└── README.md
```

## Core Patterns

**Consumer Setup:**
- Kafka consumer group with automatic offset management
- NATS JetStream durable consumers
- Redis Streams with consumer groups
- Batch consumption for throughput
- Graceful shutdown with offset commit

**Message Processing:**
- Single message: `func ProcessMessage(ctx context.Context, msg Message) error`
- Batch processing: `func ProcessBatch(ctx context.Context, msgs []Message) error`
- Idempotent processing (deduplicate by message ID)
- Error handling with retry logic
- Dead letter queue for failed messages

**Stream Patterns:**
- **Fan-out:** Single input, multiple processors
- **Fan-in:** Multiple inputs, single processor
- **Pipeline:** Chained processing stages
- **Windowing:** Time or count-based windows
- **Aggregation:** Group and reduce operations

**Concurrency Model:**
- Consumer goroutine per partition
- Processor pool for message handling
- Buffered channels between stages
- Backpressure handling
- Context-based cancellation

**State Management:**
- In-memory state with sync.Map or regular map + mutex
- Redis for distributed state
- Periodic state snapshots
- State TTL for cleanup
- Consistent hashing for partitioning

**Exactly-Once Semantics:**
- Transactional producers (Kafka)
- Idempotency keys for deduplication
- State + offset atomic commits
- Retry with same message ID

**Error Handling:**
- Transient errors: retry with exponential backoff
- Permanent errors: send to DLQ
- Log with context (topic, partition, offset, message ID)
- Circuit breaker for downstream failures

**Throughput Optimization:**
- Batch message consumption
- Parallel processing with worker pools
- Async producers with callbacks
- Compression for large messages
- Tune buffer sizes and batch sizes

## Kafka Integration

**Consumer Configuration:**
- Group ID for coordination
- Auto offset reset: earliest/latest
- Enable auto commit or manual commit
- Max poll records: 500-1000
- Session timeout: 30s

**Producer Configuration:**
- Acks: all (strongest guarantee)
- Retries: 3-5
- Idempotence: enabled
- Compression: snappy/lz4
- Batch size: 16KB-1MB

**Example Consumer:**
```go
config := kafka.ReaderConfig{
    Brokers:        []string{"kafka:9092"},
    Topic:          "events",
    GroupID:        "processor-group",
    MinBytes:       10e3,  // 10KB
    MaxBytes:       10e6,  // 10MB
    CommitInterval: time.Second,
}
reader := kafka.NewReader(config)
defer reader.Close()
```

## NATS Integration

**JetStream Setup:**
- Stream with retention policy
- Durable consumer with ack policy
- Max deliver attempts
- Ack wait timeout
- Flow control enabled

**Example Consumer:**
```go
js, _ := nc.JetStream()
sub, _ := js.PullSubscribe("events", "processor", 
    nats.Durable("processor-durable"),
    nats.AckWait(30*time.Second),
    nats.MaxDeliver(3),
)
```

## Redis Streams Integration

**Consumer Group:**
- XGROUP CREATE for consumer group
- XREADGROUP for consumption
- XACK for acknowledgment
- XPENDING for monitoring
- Claim abandoned messages

## Windowing Operations

**Time Windows:**
- Tumbling: Non-overlapping fixed intervals
- Sliding: Overlapping windows
- Session: Gap-based windows

**Count Windows:**
- Fixed count per window
- Trigger on count threshold

**Example Tumbling Window:**
```go
type Window struct {
    Start    time.Time
    End      time.Time
    Messages []Message
}

// 1-minute tumbling windows
windowDuration := time.Minute
windowStart := time.Now().Truncate(windowDuration)
```

## Metrics & Monitoring

Track:
- Messages processed per second
- Processing latency (p50, p95, p99)
- Consumer lag per partition
- Error rate and types
- State size and memory usage

**Prometheus Metrics:**
```go
messagesProcessed := prometheus.NewCounterVec(
    prometheus.CounterOpts{
        Name: "stream_messages_processed_total",
    },
    []string{"topic", "status"},
)
```

## Testing Standards

- Unit tests for processors
- Mock message producers/consumers
- Integration tests with embedded Kafka/Redis
- Benchmarks for throughput
- Chaos testing for failure scenarios

## Performance Considerations

- Consumer count = partition count (max)
- Worker pool size based on CPU cores
- Buffer sizes prevent blocking
- Profile with pprof
- Monitor GC pressure

## Anti-Patterns

❌ Blocking operations in processing loop  
❌ Not handling consumer rebalancing  
❌ Losing messages on error  
❌ Not committing offsets  
❌ Processing messages out of order within partition  
❌ Not implementing backpressure  
❌ Unbounded state growth  
❌ Not monitoring consumer lag  

## Dependencies

Required:
- `github.com/segmentio/kafka-go` (Kafka)
- `github.com/nats-io/nats.go` (NATS)
- `github.com/redis/go-redis/v9` (Redis)
- `github.com/rs/zerolog` (logging)

Optional:
- `github.com/prometheus/client_golang` (metrics)
- `github.com/sony/gobreaker` (circuit breaker)

## Configuration

Required:
- `KAFKA_BROKERS` or `NATS_URL` or `REDIS_URL`
- `TOPIC` or `STREAM_NAME`
- `CONSUMER_GROUP`
- `WORKER_COUNT` (default: NumCPU)

Optional:
- `BATCH_SIZE` (default: 100)
- `FLUSH_INTERVAL` (default: 1s)
- `MAX_RETRIES` (default: 3)
- `DLQ_TOPIC` (dead letter queue)

## Deployment Considerations

- Stateless for horizontal scaling
- One instance per partition minimum
- Leader election for singleton tasks
- Health checks include consumer lag
- Graceful shutdown with timeout

## Common Use Cases

**1. Event Processing:**
- Consume events from Kafka
- Enrich with external data
- Filter and transform
- Produce to output topic

**2. Real-time Analytics:**
- Window-based aggregations
- Count, sum, average operations
- State stored in Redis
- Results published to dashboard

**3. Change Data Capture:**
- Consume database changes
- Transform to domain events
- Publish to event bus
- Update search indices
