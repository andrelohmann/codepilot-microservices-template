# Scaffold Golang Stream Processing Service

**Objective:** Create a production-ready Golang stream processing service with Kafka/NATS support, windowing, state management, and complete directory structure.

---

## 📋 Service Specification

Before running this prompt, gather the following information:

1. **Service Name:** `[e.g., stream-golang-analytics, stream-golang-events]`
2. **Service Purpose:** `[Brief description of stream processing logic]`
3. **Message Broker:** `[Kafka, NATS JetStream, or Redis Streams]`
4. **Input Topics/Streams:** `[List of input sources]`
5. **Output Topics/Streams:** `[List of output destinations]`
6. **Processing Pattern:** `[Fan-out, Fan-in, Pipeline, Windowing, Aggregation]`
7. **State Management:** `[In-memory, Redis, or stateless]`
8. **Parallelism:** `[Number of processors/consumers]`

---

## 🏗️ Architecture Overview

```
┌────────────────────────────────────────────────────────────────────┐
│              Stream Processing Architecture                        │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                     Input Sources                            │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │   Kafka    │  │    NATS    │  │   Redis    │            │ │
│  │  │   Topic    │  │ JetStream  │  │  Streams   │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
│            ↓                ↓                ↓                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                  Consumer Groups                             │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │ Consumer 1 │  │ Consumer 2 │  │ Consumer N │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
│            ↓                ↓                ↓                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │               Message Channel (Buffered)                     │ │
│  └─────────────────────────┬────────────────────────────────────┘ │
│                            │                                      │
│                            ↓                                      │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                   Processor Pool                             │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │Processor 1 │  │Processor 2 │  │Processor N │            │ │
│  │  │            │  │            │  │            │            │ │
│  │  │ ┌────────┐ │  │ ┌────────┐ │  │ ┌────────┐ │            │ │
│  │  │ │Transform│ │  │ │Filter  │ │  │ │Aggregate│ │           │ │
│  │  │ └────────┘ │  │ └────────┘ │  │ └────────┘ │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
│            ↓                ↓                ↓                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                  State Store (Optional)                      │ │
│  │  ┌────────────┐  ┌────────────┐                             │ │
│  │  │   Redis    │  │  In-Memory │                             │ │
│  │  │   (Dist)   │  │   (Local)  │                             │ │
│  │  └────────────┘  └────────────┘                             │ │
│  └──────────────────────────────────────────────────────────────┘ │
│                            │                                      │
│                            ↓                                      │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                     Output Sinks                             │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │   Kafka    │  │    NATS    │  │ PostgreSQL │            │ │
│  │  │   Topic    │  │ JetStream  │  │  Database  │            │ │
│  │  └────────────┘  └────────────┘  └────────────┘            │ │
│  └──────────────────────────────────────────────────────────────┘ │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📁 Directory Structure

Create the following complete directory structure:

```
services/[service-name]/
├── cmd/
│   └── processor/
│       └── main.go              ← Application entry point
│
├── internal/
│   ├── config/                  ← Configuration
│   │   └── config.go
│   │
│   ├── consumer/                ← Message consumers
│   │   ├── consumer.go          ← Consumer interface
│   │   ├── kafka_consumer.go
│   │   ├── nats_consumer.go
│   │   └── redis_consumer.go
│   │
│   ├── producer/                ← Message producers
│   │   ├── producer.go
│   │   ├── kafka_producer.go
│   │   └── nats_producer.go
│   │
│   ├── processor/               ← Stream processors
│   │   ├── processor.go         ← Processor interface
│   │   ├── pipeline.go          ← Processing pipeline
│   │   ├── transform.go         ← Transformation logic
│   │   ├── filter.go            ← Filtering logic
│   │   └── aggregate.go         ← Aggregation logic
│   │
│   ├── window/                  ← Windowing operations
│   │   ├── window.go
│   │   ├── tumbling.go          ← Tumbling window
│   │   ├── sliding.go           ← Sliding window
│   │   └── session.go           ← Session window
│   │
│   ├── state/                   ← State management
│   │   ├── store.go             ← State store interface
│   │   ├── memory_store.go      ← In-memory store
│   │   └── redis_store.go       ← Redis store
│   │
│   ├── models/                  ← Data models
│   │   ├── message.go
│   │   └── event.go
│   │
│   └── metrics/                 ← Metrics collection
│       └── metrics.go
│
├── pkg/                         ← Public libraries
│   ├── logger/
│   │   └── logger.go
│   └── serializer/
│       ├── json.go
│       └── avro.go
│
├── tests/                       ← Tests
│   ├── integration/
│   │   └── processor_test.go
│   └── unit/
│       └── transform_test.go
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
    github.com/segmentio/kafka-go v0.4.47
    github.com/nats-io/nats.go v1.31.0
    github.com/redis/go-redis/v9 v9.3.0
    github.com/rs/zerolog v1.31.0
    github.com/prometheus/client_golang v1.17.0
    github.com/stretchr/testify v1.8.4
)
```

### Step 2: Create cmd/processor/main.go

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
    "github.com/yourorg/[service-name]/internal/consumer"
    "github.com/yourorg/[service-name]/internal/processor"
    "github.com/yourorg/[service-name]/internal/producer"
    "github.com/yourorg/[service-name]/internal/state"
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

    // Initialize state store
    var stateStore state.Store
    if cfg.UseDistributedState {
        stateStore, err = state.NewRedisStore(cfg.RedisURL, logger)
    } else {
        stateStore = state.NewMemoryStore()
    }
    if err != nil {
        logger.Fatal().Err(err).Msg("Failed to initialize state store")
    }
    defer stateStore.Close()

    // Create consumer
    var msgConsumer consumer.Consumer
    switch cfg.BrokerType {
    case "kafka":
        msgConsumer, err = consumer.NewKafkaConsumer(
            cfg.KafkaBrokers,
            cfg.KafkaTopic,
            cfg.KafkaGroupID,
            logger,
        )
    case "nats":
        msgConsumer, err = consumer.NewNATSConsumer(
            cfg.NATSURL,
            cfg.NATSStream,
            cfg.NATSConsumer,
            logger,
        )
    default:
        logger.Fatal().Str("type", cfg.BrokerType).Msg("Unsupported broker type")
    }
    if err != nil {
        logger.Fatal().Err(err).Msg("Failed to create consumer")
    }
    defer msgConsumer.Close()

    // Create producer
    var msgProducer producer.Producer
    switch cfg.BrokerType {
    case "kafka":
        msgProducer, err = producer.NewKafkaProducer(cfg.KafkaBrokers, logger)
    case "nats":
        msgProducer, err = producer.NewNATSProducer(cfg.NATSURL, logger)
    default:
        logger.Fatal().Str("type", cfg.BrokerType).Msg("Unsupported broker type")
    }
    if err != nil {
        logger.Fatal().Err(err).Msg("Failed to create producer")
    }
    defer msgProducer.Close()

    // Create pipeline
    pipeline := processor.NewPipeline(
        cfg.WorkerCount,
        stateStore,
        msgProducer,
        logger,
    )

    // Start pipeline
    ctx, cancel := context.WithCancel(context.Background())
    defer cancel()

    go func() {
        logger.Info().
            Int("workers", cfg.WorkerCount).
            Msg("Starting stream processor")
        
        if err := pipeline.Start(ctx); err != nil {
            logger.Fatal().Err(err).Msg("Pipeline failed")
        }
    }()

    // Start consuming
    go func() {
        logger.Info().Msg("Starting message consumer")
        if err := msgConsumer.Consume(ctx, pipeline.Process); err != nil {
            logger.Error().Err(err).Msg("Consumer error")
        }
    }()

    // Wait for interrupt
    quit := make(chan os.Signal, 1)
    signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
    <-quit

    logger.Info().Msg("Shutting down processor...")

    // Graceful shutdown
    shutdownCtx, shutdownCancel := context.WithTimeout(context.Background(), 30*time.Second)
    defer shutdownCancel()

    if err := pipeline.Stop(shutdownCtx); err != nil {
        logger.Error().Err(err).Msg("Pipeline shutdown error")
    }

    logger.Info().Msg("Processor stopped")
}
```

### Step 3: Create internal/config/config.go

```go
package config

import (
    "fmt"
    "os"
    "strconv"
    "strings"
)

type Config struct {
    // General
    WorkerCount         int
    BatchSize           int
    
    // Broker
    BrokerType          string
    
    // Kafka
    KafkaBrokers        []string
    KafkaTopic          string
    KafkaGroupID        string
    KafkaOutputTopic    string
    
    // NATS
    NATSURL             string
    NATSStream          string
    NATSConsumer        string
    NATSOutputSubject   string
    
    // Redis (for state)
    RedisURL            string
    UseDistributedState bool
    
    // Processing
    WindowSizeSeconds   int
    Environment         string
}

func Load() (*Config, error) {
    workerCount, err := strconv.Atoi(getEnv("WORKER_COUNT", "10"))
    if err != nil {
        return nil, fmt.Errorf("invalid WORKER_COUNT: %w", err)
    }

    batchSize, err := strconv.Atoi(getEnv("BATCH_SIZE", "100"))
    if err != nil {
        return nil, fmt.Errorf("invalid BATCH_SIZE: %w", err)
    }

    windowSize, err := strconv.Atoi(getEnv("WINDOW_SIZE_SECONDS", "60"))
    if err != nil {
        return nil, fmt.Errorf("invalid WINDOW_SIZE_SECONDS: %w", err)
    }

    useDistributedState, _ := strconv.ParseBool(getEnv("USE_DISTRIBUTED_STATE", "false"))

    cfg := &Config{
        WorkerCount:         workerCount,
        BatchSize:           batchSize,
        BrokerType:          getEnv("BROKER_TYPE", "kafka"),
        
        // Kafka
        KafkaBrokers:        strings.Split(getEnv("KAFKA_BROKERS", "localhost:9092"), ","),
        KafkaTopic:          getEnv("KAFKA_TOPIC", "input-topic"),
        KafkaGroupID:        getEnv("KAFKA_GROUP_ID", "stream-processor"),
        KafkaOutputTopic:    getEnv("KAFKA_OUTPUT_TOPIC", "output-topic"),
        
        // NATS
        NATSURL:             getEnv("NATS_URL", "nats://localhost:4222"),
        NATSStream:          getEnv("NATS_STREAM", "events"),
        NATSConsumer:        getEnv("NATS_CONSUMER", "processor"),
        NATSOutputSubject:   getEnv("NATS_OUTPUT_SUBJECT", "processed"),
        
        // Redis
        RedisURL:            getEnv("REDIS_URL", "redis://localhost:6379"),
        UseDistributedState: useDistributedState,
        
        // Processing
        WindowSizeSeconds:   windowSize,
        Environment:         getEnv("ENVIRONMENT", "development"),
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

### Step 4: Create internal/models/message.go

```go
package models

import (
    "encoding/json"
    "time"
)

type Message struct {
    ID        string                 `json:"id"`
    Type      string                 `json:"type"`
    Payload   map[string]interface{} `json:"payload"`
    Timestamp time.Time              `json:"timestamp"`
    Metadata  map[string]string      `json:"metadata,omitempty"`
}

func (m *Message) Marshal() ([]byte, error) {
    return json.Marshal(m)
}

func UnmarshalMessage(data []byte) (*Message, error) {
    var msg Message
    if err := json.Unmarshal(data, &msg); err != nil {
        return nil, err
    }
    return &msg, nil
}
```

### Step 5: Create internal/consumer/kafka_consumer.go

```go
package consumer

import (
    "context"
    "fmt"

    "github.com/segmentio/kafka-go"
    "github.com/rs/zerolog"
)

type KafkaConsumer struct {
    reader *kafka.Reader
    logger zerolog.Logger
}

func NewKafkaConsumer(brokers []string, topic, groupID string, logger zerolog.Logger) (*KafkaConsumer, error) {
    reader := kafka.NewReader(kafka.ReaderConfig{
        Brokers:        brokers,
        Topic:          topic,
        GroupID:        groupID,
        MinBytes:       10e3,  // 10KB
        MaxBytes:       10e6,  // 10MB
        CommitInterval: 1,     // Commit every second
    })

    return &KafkaConsumer{
        reader: reader,
        logger: logger,
    }, nil
}

func (c *KafkaConsumer) Consume(ctx context.Context, handler func([]byte) error) error {
    for {
        select {
        case <-ctx.Done():
            return ctx.Err()
        default:
            msg, err := c.reader.ReadMessage(ctx)
            if err != nil {
                c.logger.Error().Err(err).Msg("Failed to read message")
                continue
            }

            c.logger.Debug().
                Str("topic", msg.Topic).
                Int("partition", msg.Partition).
                Int64("offset", msg.Offset).
                Msg("Received message")

            if err := handler(msg.Value); err != nil {
                c.logger.Error().Err(err).Msg("Failed to process message")
                // Depending on error handling strategy, may want to retry or send to DLQ
            }
        }
    }
}

func (c *KafkaConsumer) Close() error {
    return c.reader.Close()
}
```

### Step 6: Create internal/producer/kafka_producer.go

```go
package producer

import (
    "context"
    "fmt"

    "github.com/segmentio/kafka-go"
    "github.com/rs/zerolog"
)

type KafkaProducer struct {
    writer *kafka.Writer
    logger zerolog.Logger
}

func NewKafkaProducer(brokers []string, logger zerolog.Logger) (*KafkaProducer, error) {
    writer := &kafka.Writer{
        Addr:         kafka.TCP(brokers...),
        Balancer:     &kafka.LeastBytes{},
        BatchSize:    100,
        RequiredAcks: kafka.RequireOne,
    }

    return &KafkaProducer{
        writer: writer,
        logger: logger,
    }, nil
}

func (p *KafkaProducer) Produce(ctx context.Context, topic string, key, value []byte) error {
    err := p.writer.WriteMessages(ctx, kafka.Message{
        Topic: topic,
        Key:   key,
        Value: value,
    })

    if err != nil {
        p.logger.Error().Err(err).Str("topic", topic).Msg("Failed to produce message")
        return fmt.Errorf("failed to produce message: %w", err)
    }

    p.logger.Debug().Str("topic", topic).Msg("Message produced")
    return nil
}

func (p *KafkaProducer) Close() error {
    return p.writer.Close()
}
```

### Step 7: Create internal/processor/pipeline.go

```go
package processor

import (
    "context"
    "fmt"
    "sync"
    "time"

    "github.com/rs/zerolog"
    
    "github.com/yourorg/[service-name]/internal/models"
    "github.com/yourorg/[service-name]/internal/producer"
    "github.com/yourorg/[service-name]/internal/state"
)

type Pipeline struct {
    workerCount int
    messageChan chan []byte
    stateStore  state.Store
    producer    producer.Producer
    logger      zerolog.Logger
    wg          sync.WaitGroup
}

func NewPipeline(
    workerCount int,
    stateStore state.Store,
    producer producer.Producer,
    logger zerolog.Logger,
) *Pipeline {
    return &Pipeline{
        workerCount: workerCount,
        messageChan: make(chan []byte, 1000),
        stateStore:  stateStore,
        producer:    producer,
        logger:      logger,
    }
}

func (p *Pipeline) Start(ctx context.Context) error {
    p.logger.Info().Int("workers", p.workerCount).Msg("Starting pipeline workers")

    for i := 0; i < p.workerCount; i++ {
        p.wg.Add(1)
        go p.worker(ctx, i)
    }

    p.wg.Wait()
    return nil
}

func (p *Pipeline) worker(ctx context.Context, id int) {
    defer p.wg.Done()

    p.logger.Info().Int("worker_id", id).Msg("Worker started")

    for {
        select {
        case <-ctx.Done():
            p.logger.Info().Int("worker_id", id).Msg("Worker stopping")
            return

        case msgData, ok := <-p.messageChan:
            if !ok {
                p.logger.Info().Int("worker_id", id).Msg("Message channel closed")
                return
            }

            p.processMessage(ctx, id, msgData)
        }
    }
}

func (p *Pipeline) processMessage(ctx context.Context, workerID int, msgData []byte) {
    start := time.Now()

    // Unmarshal message
    msg, err := models.UnmarshalMessage(msgData)
    if err != nil {
        p.logger.Error().Err(err).Msg("Failed to unmarshal message")
        return
    }

    // Transform
    transformed := p.transform(msg)

    // Filter
    if !p.filter(transformed) {
        p.logger.Debug().Str("message_id", msg.ID).Msg("Message filtered out")
        return
    }

    // Aggregate (if needed)
    aggregated, err := p.aggregate(ctx, transformed)
    if err != nil {
        p.logger.Error().Err(err).Msg("Aggregation failed")
        return
    }

    // Produce output
    if aggregated != nil {
        outputData, err := aggregated.Marshal()
        if err != nil {
            p.logger.Error().Err(err).Msg("Failed to marshal output")
            return
        }

        if err := p.producer.Produce(ctx, "output-topic", []byte(aggregated.ID), outputData); err != nil {
            p.logger.Error().Err(err).Msg("Failed to produce output")
            return
        }
    }

    duration := time.Since(start)
    p.logger.Info().
        Int("worker_id", workerID).
        Str("message_id", msg.ID).
        Dur("duration", duration).
        Msg("Message processed")
}

func (p *Pipeline) transform(msg *models.Message) *models.Message {
    // Add your transformation logic here
    // Example: Enrich with additional data
    if msg.Metadata == nil {
        msg.Metadata = make(map[string]string)
    }
    msg.Metadata["processed_at"] = time.Now().Format(time.RFC3339)
    
    return msg
}

func (p *Pipeline) filter(msg *models.Message) bool {
    // Add your filtering logic here
    // Example: Filter by message type
    return msg.Type != "ignore"
}

func (p *Pipeline) aggregate(ctx context.Context, msg *models.Message) (*models.Message, error) {
    // Add your aggregation logic here
    // Example: Count messages by type
    key := fmt.Sprintf("count:%s", msg.Type)
    
    count, err := p.stateStore.Increment(ctx, key)
    if err != nil {
        return nil, err
    }

    msg.Metadata["total_count"] = fmt.Sprintf("%d", count)
    
    return msg, nil
}

func (p *Pipeline) Process(msgData []byte) error {
    select {
    case p.messageChan <- msgData:
        return nil
    default:
        return fmt.Errorf("message channel is full")
    }
}

func (p *Pipeline) Stop(ctx context.Context) error {
    p.logger.Info().Msg("Stopping pipeline")

    close(p.messageChan)

    done := make(chan struct{})
    go func() {
        p.wg.Wait()
        close(done)
    }()

    select {
    case <-done:
        p.logger.Info().Msg("All workers stopped gracefully")
        return nil
    case <-ctx.Done():
        return fmt.Errorf("shutdown timeout exceeded")
    }
}
```

### Step 8: Create internal/state/memory_store.go

```go
package state

import (
    "context"
    "sync"
)

type MemoryStore struct {
    data map[string]int64
    mu   sync.RWMutex
}

func NewMemoryStore() *MemoryStore {
    return &MemoryStore{
        data: make(map[string]int64),
    }
}

func (s *MemoryStore) Get(ctx context.Context, key string) (int64, error) {
    s.mu.RLock()
    defer s.mu.RUnlock()
    
    return s.data[key], nil
}

func (s *MemoryStore) Set(ctx context.Context, key string, value int64) error {
    s.mu.Lock()
    defer s.mu.Unlock()
    
    s.data[key] = value
    return nil
}

func (s *MemoryStore) Increment(ctx context.Context, key string) (int64, error) {
    s.mu.Lock()
    defer s.mu.Unlock()
    
    s.data[key]++
    return s.data[key], nil
}

func (s *MemoryStore) Delete(ctx context.Context, key string) error {
    s.mu.Lock()
    defer s.mu.Unlock()
    
    delete(s.data, key)
    return nil
}

func (s *MemoryStore) Close() error {
    return nil
}
```

### Step 9: Create internal/state/redis_store.go

```go
package state

import (
    "context"
    "fmt"

    "github.com/redis/go-redis/v9"
    "github.com/rs/zerolog"
)

type RedisStore struct {
    client *redis.Client
    logger zerolog.Logger
}

func NewRedisStore(redisURL string, logger zerolog.Logger) (*RedisStore, error) {
    opts, err := redis.ParseURL(redisURL)
    if err != nil {
        return nil, fmt.Errorf("failed to parse redis URL: %w", err)
    }

    client := redis.NewClient(opts)

    if err := client.Ping(context.Background()).Err(); err != nil {
        return nil, fmt.Errorf("failed to connect to redis: %w", err)
    }

    return &RedisStore{
        client: client,
        logger: logger,
    }, nil
}

func (s *RedisStore) Get(ctx context.Context, key string) (int64, error) {
    val, err := s.client.Get(ctx, key).Int64()
    if err == redis.Nil {
        return 0, nil
    }
    return val, err
}

func (s *RedisStore) Set(ctx context.Context, key string, value int64) error {
    return s.client.Set(ctx, key, value, 0).Err()
}

func (s *RedisStore) Increment(ctx context.Context, key string) (int64, error) {
    return s.client.Incr(ctx, key).Result()
}

func (s *RedisStore) Delete(ctx context.Context, key string) error {
    return s.client.Del(ctx, key).Err()
}

func (s *RedisStore) Close() error {
    return s.client.Close()
}
```

### Step 10: Create Dockerfile

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
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o processor ./cmd/processor

# Runtime stage
FROM alpine:latest

RUN apk --no-cache add ca-certificates

WORKDIR /root/

# Copy binary from builder
COPY --from=builder /app/processor .

CMD ["./processor"]
```

### Step 11: Create docker-compose.yml

```yaml
version: '3.8'

services:
  [service-name]:
    build: .
    environment:
      - WORKER_COUNT=10
      - BATCH_SIZE=100
      - BROKER_TYPE=kafka
      - KAFKA_BROKERS=kafka:9092
      - KAFKA_TOPIC=input-events
      - KAFKA_GROUP_ID=stream-processor
      - KAFKA_OUTPUT_TOPIC=processed-events
      - REDIS_URL=redis://redis:6379
      - USE_DISTRIBUTED_STATE=true
      - WINDOW_SIZE_SECONDS=60
      - ENVIRONMENT=development
    depends_on:
      - kafka
      - redis
    networks:
      - microservices

  kafka:
    image: confluentinc/cp-kafka:latest
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    depends_on:
      - zookeeper
    networks:
      - microservices

  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
    networks:
      - microservices

  redis:
    image: redis:7-alpine
    networks:
      - microservices

networks:
  microservices:
    external: true
```

### Step 12: Create .env.example

```bash
WORKER_COUNT=10
BATCH_SIZE=100
BROKER_TYPE=kafka
KAFKA_BROKERS=localhost:9092
KAFKA_TOPIC=input-events
KAFKA_GROUP_ID=stream-processor
KAFKA_OUTPUT_TOPIC=processed-events
NATS_URL=nats://localhost:4222
NATS_STREAM=events
NATS_CONSUMER=processor
NATS_OUTPUT_SUBJECT=processed
REDIS_URL=redis://localhost:6379
USE_DISTRIBUTED_STATE=false
WINDOW_SIZE_SECONDS=60
ENVIRONMENT=development
```

### Step 13: Create Makefile

```makefile
.PHONY: build run test

build:
	go build -o bin/processor cmd/processor/main.go

run:
	go run cmd/processor/main.go

test:
	go test -v -race -coverprofile=coverage.out ./...

docker-build:
	docker build -t [service-name]:latest .

docker-run:
	docker-compose up -d
```

### Step 14: Create README.md

```markdown
# Stream Processing Service

Real-time stream processing with Kafka/NATS support, stateful operations, and high throughput.

## Features

- Kafka and NATS JetStream support
- Parallel processing with worker pools
- Stateful aggregations
- Windowing operations
- Exactly-once semantics (with Kafka)
- Metrics and monitoring
- Graceful shutdown

## Prerequisites

- Go 1.21+
- Docker & Docker Compose
- Kafka or NATS

## Getting Started

```bash
# Install dependencies
go mod download

# Run locally
make run

# Run with Docker
docker-compose up -d
```

## Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| WORKER_COUNT | Number of processors | 10 |
| BATCH_SIZE | Batch size | 100 |
| BROKER_TYPE | kafka or nats | kafka |
| USE_DISTRIBUTED_STATE | Use Redis for state | false |

## Architecture

- **Consumer**: Reads from Kafka/NATS
- **Processor**: Transforms, filters, aggregates
- **State Store**: In-memory or Redis
- **Producer**: Writes to output topics

## Testing

```bash
make test
```

## License

[Your License]
```

---

## ✅ Completion Checklist

- [ ] All files created
- [ ] go.mod configured
- [ ] Kafka consumer implemented
- [ ] NATS consumer implemented
- [ ] Message processing pipeline
- [ ] State management (memory + Redis)
- [ ] Producer implementation
- [ ] Transformation logic
- [ ] Aggregation logic
- [ ] Dockerfile created
- [ ] docker-compose.yml complete
- [ ] .env.example configured
- [ ] README.md with documentation
- [ ] Tested with Kafka
- [ ] Tested with NATS
- [ ] Metrics collection working

---

**Next Steps:**
1. Implement specific business logic
2. Add windowing operations
3. Configure monitoring
4. Set up alerting
5. Optimize throughput
6. Add dead letter queue
7. Implement backpressure handling
