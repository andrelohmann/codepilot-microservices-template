# Golang Stream Processing - Kafka & NATS

High-performance stream processing with Go for real-time event handling and data pipelines.

---

## Overview

**Naming:** `stream-golang-{platform}-{purpose}`

**Examples:** `stream-golang-kafka-logs`, `stream-golang-nats-events`, `stream-golang-kafka-analytics`

**Golang** provides excellent performance for stream processing with minimal resource usage and native concurrency.

---

## Technology Stack

- **Go 1.21+** - Programming language
- **Kafka** or **NATS** - Message broker
- **sarama** or **confluent-kafka-go** - Kafka client
- **nats.go** - NATS client
- **Prometheus** - Metrics
- **Zap/Zerolog** - Structured logging

---

## Features

✅ **High performance** - Native concurrency  
✅ **Low resource usage** - Small memory footprint  
✅ **Simple deployment** - Single binary  
✅ **Built-in concurrency** - Goroutines and channels  
✅ **Type safety** - Compile-time checks  
✅ **Fast startup** - Minimal overhead

---

## Ideal Use Cases

✅ **High-throughput processing** - Handle millions of messages  
✅ **Real-time analytics** - Process streams in real-time  
✅ **Log aggregation** - Collect and process logs  
✅ **Event-driven microservices** - React to events  
✅ **Data pipelines** - ETL with streaming data

---

## Kafka Implementation

### Project Structure
```
stream-golang-kafka-logs/
├── cmd/
│   └── consumer/
│       └── main.go
├── internal/
│   ├── config/
│   │   └── config.go
│   ├── consumer/
│   │   └── consumer.go
│   ├── processor/
│   │   └── processor.go
│   └── metrics/
│       └── metrics.go
├── go.mod
├── go.sum
├── Dockerfile
└── docker-compose.yml
```

### Kafka Consumer

```go
// internal/consumer/consumer.go
package consumer

import (
    "context"
    "log"
    
    "github.com/IBM/sarama"
)

type Consumer struct {
    client    sarama.ConsumerGroup
    processor Processor
    topics    []string
}

type Processor interface {
    Process(ctx context.Context, message *sarama.ConsumerMessage) error
}

func New(brokers []string, groupID string, topics []string, processor Processor) (*Consumer, error) {
    config := sarama.NewConfig()
    config.Consumer.Group.Rebalance.Strategy = sarama.BalanceStrategyRoundRobin
    config.Consumer.Offsets.Initial = sarama.OffsetNewest
    
    client, err := sarama.NewConsumerGroup(brokers, groupID, config)
    if err != nil {
        return nil, err
    }
    
    return &Consumer{
        client:    client,
        processor: processor,
        topics:    topics,
    }, nil
}

func (c *Consumer) Start(ctx context.Context) error {
    handler := &consumerGroupHandler{processor: c.processor}
    
    for {
        if err := c.client.Consume(ctx, c.topics, handler); err != nil {
            log.Printf("Error from consumer: %v", err)
        }
        
        if ctx.Err() != nil {
            return ctx.Err()
        }
    }
}

func (c *Consumer) Close() error {
    return c.client.Close()
}

type consumerGroupHandler struct {
    processor Processor
}

func (h *consumerGroupHandler) Setup(_ sarama.ConsumerGroupSession) error { return nil }
func (h *consumerGroupHandler) Cleanup(_ sarama.ConsumerGroupSession) error { return nil }

func (h *consumerGroupHandler) ConsumeClaim(session sarama.ConsumerGroupSession, claim sarama.ConsumerGroupClaim) error {
    for message := range claim.Messages() {
        if err := h.processor.Process(session.Context(), message); err != nil {
            log.Printf("Error processing message: %v", err)
            continue
        }
        session.MarkMessage(message, "")
    }
    return nil
}
```

### Message Processor

```go
// internal/processor/processor.go
package processor

import (
    "context"
    "encoding/json"
    "log"
    
    "github.com/IBM/sarama"
)

type LogEvent struct {
    Timestamp string `json:"timestamp"`
    Level     string `json:"level"`
    Service   string `json:"service"`
    Message   string `json:"message"`
}

type LogProcessor struct {
    // Dependencies
}

func (p *LogProcessor) Process(ctx context.Context, msg *sarama.ConsumerMessage) error {
    var event LogEvent
    if err := json.Unmarshal(msg.Value, &event); err != nil {
        return err
    }
    
    // Process the event
    log.Printf("Processing log: service=%s level=%s message=%s", 
        event.Service, event.Level, event.Message)
    
    // Store, forward, or analyze
    // ...
    
    return nil
}
```

### Main Application

```go
// cmd/consumer/main.go
package main

import (
    "context"
    "log"
    "os"
    "os/signal"
    "syscall"
    
    "your-module/internal/consumer"
    "your-module/internal/processor"
)

func main() {
    brokers := []string{"kafka:9092"}
    groupID := "log-processor-group"
    topics := []string{"logs"}
    
    processor := &processor.LogProcessor{}
    
    consumer, err := consumer.New(brokers, groupID, topics, processor)
    if err != nil {
        log.Fatalf("Failed to create consumer: %v", err)
    }
    defer consumer.Close()
    
    ctx, cancel := context.WithCancel(context.Background())
    defer cancel()
    
    // Handle shutdown
    sigterm := make(chan os.Signal, 1)
    signal.Notify(sigterm, syscall.SIGINT, syscall.SIGTERM)
    
    go func() {
        <-sigterm
        log.Println("Shutting down...")
        cancel()
    }()
    
    log.Println("Starting consumer...")
    if err := consumer.Start(ctx); err != nil {
        log.Fatalf("Consumer error: %v", err)
    }
}
```

---

## NATS Implementation

### NATS Consumer

```go
package main

import (
    "log"
    "os"
    "os/signal"
    "syscall"
    
    "github.com/nats-io/nats.go"
)

func main() {
    nc, err := nats.Connect("nats://nats:4222")
    if err != nil {
        log.Fatal(err)
    }
    defer nc.Close()
    
    js, err := nc.JetStream()
    if err != nil {
        log.Fatal(err)
    }
    
    // Create durable consumer
    sub, err := js.Subscribe("events.>", func(msg *nats.Msg) {
        log.Printf("Received: %s", string(msg.Data))
        msg.Ack()
    }, nats.Durable("event-processor"))
    
    if err != nil {
        log.Fatal(err)
    }
    defer sub.Unsubscribe()
    
    // Wait for interrupt
    sigterm := make(chan os.Signal, 1)
    signal.Notify(sigterm, syscall.SIGINT, syscall.SIGTERM)
    <-sigterm
}
```

### NATS with Consumer Group

```go
package main

import (
    "context"
    "log"
    "sync"
    
    "github.com/nats-io/nats.go"
)

type EventProcessor struct {
    nc *nats.Conn
    js nats.JetStreamContext
    wg sync.WaitGroup
}

func NewEventProcessor(url string) (*EventProcessor, error) {
    nc, err := nats.Connect(url)
    if err != nil {
        return nil, err
    }
    
    js, err := nc.JetStream()
    if err != nil {
        nc.Close()
        return nil, err
    }
    
    return &EventProcessor{nc: nc, js: js}, nil
}

func (ep *EventProcessor) Start(ctx context.Context, workers int) error {
    for i := 0; i < workers; i++ {
        ep.wg.Add(1)
        go ep.worker(ctx, i)
    }
    
    ep.wg.Wait()
    return nil
}

func (ep *EventProcessor) worker(ctx context.Context, id int) {
    defer ep.wg.Done()
    
    sub, err := ep.js.Subscribe("events.*", func(msg *nats.Msg) {
        log.Printf("Worker %d processing: %s", id, string(msg.Data))
        
        // Process message
        if err := processEvent(msg.Data); err != nil {
            log.Printf("Error processing: %v", err)
            msg.Nak()
            return
        }
        
        msg.Ack()
    }, nats.Durable("workers"), nats.ManualAck())
    
    if err != nil {
        log.Printf("Worker %d error: %v", id, err)
        return
    }
    defer sub.Unsubscribe()
    
    <-ctx.Done()
}

func processEvent(data []byte) error {
    // Processing logic
    return nil
}
```

---

## Deployment

### Dockerfile

```dockerfile
FROM golang:1.21-alpine AS builder

WORKDIR /app
COPY go.* ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 go build -o consumer ./cmd/consumer

FROM alpine:latest
RUN apk --no-cache add ca-certificates

WORKDIR /app
COPY --from=builder /app/consumer .

CMD ["./consumer"]
```

### Docker Compose (Kafka)

```yaml
version: '3.8'

services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.5.0
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181

  kafka:
    image: confluentinc/cp-kafka:7.5.0
    depends_on:
      - zookeeper
    ports:
      - "9092:9092"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1

  stream-consumer:
    build: .
    depends_on:
      - kafka
    environment:
      KAFKA_BROKERS: kafka:9092
      KAFKA_GROUP_ID: log-processor
      KAFKA_TOPICS: logs
```

### Docker Compose (NATS)

```yaml
version: '3.8'

services:
  nats:
    image: nats:alpine
    ports:
      - "4222:4222"
    command: ["-js"]

  stream-consumer:
    build: .
    depends_on:
      - nats
    environment:
      NATS_URL: nats://nats:4222
```

---

## Monitoring

### Prometheus Metrics

```go
package metrics

import (
    "github.com/prometheus/client_golang/prometheus"
    "github.com/prometheus/client_golang/prometheus/promauto"
)

var (
    MessagesProcessed = promauto.NewCounterVec(
        prometheus.CounterOpts{
            Name: "messages_processed_total",
            Help: "Total messages processed",
        },
        []string{"topic", "status"},
    )
    
    ProcessingDuration = promauto.NewHistogramVec(
        prometheus.HistogramOpts{
            Name: "message_processing_duration_seconds",
            Help: "Message processing duration",
        },
        []string{"topic"},
    )
)
```

---

## Best Practices

✅ Use consumer groups for load balancing  
✅ Implement idempotent processing  
✅ Handle errors gracefully  
✅ Monitor consumer lag  
✅ Use structured logging  
✅ Set up health checks  
✅ Implement graceful shutdown  
✅ Test with integration tests

---

## Related Documentation

- [Streams Overview](./README.md)
- [Worker Technologies](../workers/README.md)
- [Naming Conventions](../NAMING-CONVENTIONS.md)

---

**Reference Files:**
- Instruction: `service-stream-golang-kafka.instructions.md`
- Scaffold: `scaffold-stream-golang-service.md`
