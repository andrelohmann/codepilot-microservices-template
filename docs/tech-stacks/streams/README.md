# Streams - Real-Time Event Processing

Process continuous streams of data with event-driven architectures using Kafka, NATS, or other streaming platforms.

---

## Overview

Stream services handle real-time data processing, event-driven architectures, and message streaming at scale.

---

## Available Technologies

### 1. [Golang with Kafka/NATS](./golang.md) ⭐ **RECOMMENDED**
**Type:** High-performance stream processor  
**Best For:** Real-time event processing, log aggregation, data pipelines

---

## What is Stream Processing?

Stream processing handles continuous flows of data in real-time, unlike batch processing which works on static datasets.

### Key Concepts

**Event Stream:** Continuous sequence of events  
**Producer:** Publishes events to stream  
**Consumer:** Subscribes and processes events  
**Topic/Subject:** Named channel for events  
**Partition:** Parallel processing unit  
**Consumer Group:** Load-balanced consumers

---

## Architecture Patterns

### Simple Event Stream
```
Producer → Stream → Consumer
```

### Fan-Out (Multiple Consumers)
```
Producer → Stream → Consumer 1 (Analytics)
                 → Consumer 2 (Storage)
                 → Consumer 3 (Notifications)
```

### Event Sourcing
```
Service → Event Stream → Event Store
                      → Projection Builder
                      → Read Model
```

### CQRS with Streams
```
Command → Write Model → Events → Stream → Read Model
```

### Real-Time Pipeline
```
Logs → Collector → Stream → Processor → Analytics DB
                                      → Alerting
```

---

## Common Use Cases

### 1. Log Aggregation
Collect and process logs from multiple services:
```
[Service A Logs] → Stream → Log Processor → Elasticsearch
[Service B Logs] →        → Metrics       → Prometheus
[Service C Logs] →        → Alerts        → PagerDuty
```

### 2. Event-Driven Microservices
Decouple services with events:
```
Order Service → OrderCreated → [Inventory, Billing, Notification]
```

### 3. Real-Time Analytics
Process data streams for analytics:
```
User Activity → Stream → Aggregator → Dashboard
```

### 4. Data Pipelines
ETL with streaming data:
```
Source DB → Change Stream → Transformer → Data Warehouse
```

### 5. IoT Data Processing
Handle sensor data streams:
```
IoT Devices → MQTT → Stream Processor → Time-Series DB
```

---

## Technology Comparison

### Kafka
**Best For:** Large-scale event streaming, log aggregation  
**Pros:**
- High throughput
- Durable storage
- Strong ordering guarantees
- Mature ecosystem

**Cons:**
- Complex setup
- Resource-intensive
- Requires ZooKeeper (older versions)

### NATS
**Best For:** Lightweight messaging, microservices  
**Pros:**
- Simple deployment
- Low latency
- Easy to operate
- Kubernetes-friendly

**Cons:**
- Less mature than Kafka
- Smaller ecosystem
- Limited durability (JetStream adds this)

### Redis Streams
**Best For:** Simple event streams, caching + streaming  
**Pros:**
- Simple to use
- Fast
- Minimal setup

**Cons:**
- Limited durability
- Not for large-scale streaming
- In-memory limitations

---

## Naming Convention

```
stream-{tech}-{purpose}
```

### Examples
```
stream-golang-kafka-logs      # Kafka log processor
stream-golang-nats-events     # NATS event processor
stream-golang-kafka-analytics # Kafka analytics pipeline
stream-golang-nats-iot        # NATS IoT data processor
```

---

## When to Use Stream Processing

### Use Streams When:
✅ Processing **real-time data**  
✅ Building **event-driven** systems  
✅ Need **high throughput**  
✅ Handling **log aggregation**  
✅ Implementing **CQRS/Event Sourcing**  
✅ Building **data pipelines**

### Use Alternative When:
❌ Batch processing is sufficient  
❌ Simple request/response (use HTTP)  
❌ Low-volume data  
❌ Strong transactional requirements

---

## Key Patterns

### At-Least-Once Delivery
Consumer might process same message multiple times:
- Idempotent processing required
- Simpler to implement
- Common default

### Exactly-Once Delivery
Message processed exactly once:
- Transactional processing
- More complex
- Use when idempotency is hard

### Consumer Groups
Load balance across consumers:
- Horizontal scaling
- Each message to one consumer in group
- Automatic failover

### Dead Letter Queue
Handle failed messages:
- Retry logic
- Manual inspection
- Prevent blocking

---

## Monitoring

### Key Metrics
- **Throughput:** Messages/second
- **Lag:** Consumer behind producer
- **Processing time:** Time to process message
- **Error rate:** Failed messages
- **Partition distribution:** Load balance

### Tools
- **Kafka:** Kafka Manager, Confluent Control Center
- **NATS:** NATS monitoring API
- **Prometheus:** Metrics collection
- **Grafana:** Visualization

---

## Best Practices

✅ **Design idempotent consumers** - Handle duplicates  
✅ **Monitor consumer lag** - Detect processing delays  
✅ **Use consumer groups** - Scale horizontally  
✅ **Implement retry logic** - Handle transient failures  
✅ **Set up dead letter queues** - Handle poison messages  
✅ **Partition strategically** - Balance load  
✅ **Version event schemas** - Enable evolution  
✅ **Log processing errors** - Debug issues

---

## Testing Strategy

### Unit Tests
Test message processing logic:
```go
func TestProcessEvent(t *testing.T) {
    event := Event{ID: "123", Type: "OrderCreated"}
    result := ProcessEvent(event)
    assert.Equal(t, "processed", result.Status)
}
```

### Integration Tests
Test with real message broker:
```go
func TestKafkaConsumer(t *testing.T) {
    // Start test Kafka
    producer := NewTestProducer()
    consumer := NewConsumer()
    
    producer.Send(testMessage)
    received := consumer.Receive()
    
    assert.Equal(t, testMessage, received)
}
```

### Load Tests
Test throughput and lag:
```bash
# Generate load
kafka-producer-perf-test --topic test --num-records 100000 --throughput 1000
```

---

## Security

✅ **Encrypt in transit** - TLS for connections  
✅ **Authenticate** - SASL for Kafka, JWT for NATS  
✅ **Authorize** - ACLs for topics  
✅ **Audit logs** - Track access  
✅ **Network segmentation** - Isolate brokers

---

## Related Documentation

- [Golang Stream Processing](./golang.md)
- [Worker Technologies](../workers/README.md)
- [API Technologies](../apis/README.md)

---

## Next Steps

1. **Choose platform**: Kafka (large-scale) or NATS (lightweight)
2. **Review implementation**: Check [Golang documentation](./golang.md)
3. **Design events**: Define event schema
4. **Implement consumers**: Build processing logic
5. **Setup monitoring**: Track lag and throughput
6. **Test thoroughly**: Unit, integration, and load tests
7. **Deploy**: Container or VM deployment
