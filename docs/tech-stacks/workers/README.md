# Workers - Background Job Processing

Asynchronous task execution, job queuing, scheduled tasks, and background processing.

---

## Overview

Worker services handle background jobs, asynchronous tasks, scheduled processes, and long-running operations that should not block API requests or user interfaces. They consume jobs from message queues (Redis, RabbitMQ, NATS) and process them independently.

---

## Available Technologies

### 1. [BullMQ (Node.js)](./bullmq.md) ⭐
**Queue:** Redis  
**Language:** TypeScript/JavaScript  
**Best For:** Modern Node.js applications, email sending, report generation, image processing

### 2. [Celery (Python)](./celery.md) ⭐
**Queue:** Redis / RabbitMQ  
**Language:** Python  
**Best For:** ETL pipelines, data processing, ML model training, scheduled analytics

### 3. [Go Native Worker](./golang.md)
**Queue:** Redis / NATS  
**Language:** Go  
**Best For:** High-throughput processing, real-time pipelines, system workers

---

## Technology Comparison

| Feature | BullMQ | Celery | Go Native |
|---------|--------|--------|-----------|
| **Language** | Node.js / TypeScript | Python | Go |
| **Primary Broker** | Redis | Redis / RabbitMQ | Redis / NATS |
| **Ease of Use** | ⭐⭐⭐⭐⭐ Very Easy | ⭐⭐⭐⭐ Easy | ⭐⭐⭐ Moderate |
| **Performance** | ⭐⭐⭐⭐ Fast | ⭐⭐⭐ Good | ⭐⭐⭐⭐⭐ Fastest |
| **Scalability** | ⭐⭐⭐⭐ Horizontal | ⭐⭐⭐⭐⭐ Excellent | ⭐⭐⭐⭐⭐ Excellent |
| **Job Scheduling** | ✅ Built-in | ✅ Celery Beat | ⚠️ Manual (cron) |
| **Retry Mechanisms** | ✅ Advanced | ✅ Advanced | ⚠️ Manual |
| **Monitoring** | ✅ Bull Board | ✅ Flower | ⚠️ Custom (Prometheus) |
| **Task Chains** | ✅ Flows | ✅ Canvas | ⚠️ Manual orchestration |
| **Community** | Large (Node.js) | Very Large (Python) | Moderate |
| **Ecosystem** | npm packages | Python libraries | Go standard library |

---

## Performance Comparison

### Throughput (Jobs/Second)

| Scenario | BullMQ | Celery | Go Native |
|----------|--------|--------|-----------|
| **Simple Tasks** | 5,000 | 3,000 | 15,000 |
| **I/O-Bound Tasks** | 8,000 | 4,000 | 20,000 |
| **CPU-Bound Tasks** | 2,000 | 2,500 | 10,000 |
| **Large Payloads** | 1,500 | 1,000 | 5,000 |

*Benchmarks with single worker instance, Redis broker, 4 CPU cores*

### Resource Usage (per worker)

| Worker | Memory (Idle) | Memory (Active) | CPU (Idle) | CPU (Active) |
|--------|---------------|-----------------|------------|--------------|
| **BullMQ** | 50 MB | 150 MB | 5% | 40% |
| **Celery** | 80 MB | 200 MB | 8% | 50% |
| **Go Native** | 15 MB | 50 MB | 2% | 30% |

---

## When to Use Each Technology

### Choose **BullMQ** When:
✅ Working in a **Node.js/TypeScript ecosystem**  
✅ Need **easy integration** with existing NestJS/Express APIs  
✅ Want **modern UI** for monitoring (Bull Board)  
✅ Processing **moderate volumes** (< 10K jobs/sec)  
✅ Need **advanced scheduling** (cron, delayed jobs)  
✅ Building **email services**, **notification systems**, **report generators**  
✅ Team prefers **JavaScript/TypeScript**

**Ideal Use Cases:**
- Email sending (transactional, newsletters)
- Report generation (PDF, Excel)
- Image/video processing
- Data export/import
- Notification delivery
- Webhook processing

### Choose **Celery** When:
✅ Working in a **Python ecosystem**  
✅ Need to integrate with **Python data science libraries** (pandas, NumPy, scikit-learn)  
✅ Building **ETL pipelines** or **data processing workflows**  
✅ Running **ML model training** or **inference**  
✅ Need **task chains**, **groups**, and **chords** (complex workflows)  
✅ Want **mature ecosystem** with lots of plugins  
✅ Processing **data-intensive tasks**

**Ideal Use Cases:**
- ETL pipelines
- Data analytics and aggregation
- ML model training/inference
- Scheduled reports and analytics
- Data synchronization
- Batch processing

### Choose **Go Native Worker** When:
✅ Need **maximum throughput** (> 10K jobs/sec)  
✅ **Low resource usage** is critical  
✅ Building **high-performance real-time pipelines**  
✅ Want **no external dependencies** (just Go standard library)  
✅ Need **fine-grained control** over concurrency  
✅ Processing **simple, high-volume tasks**  
✅ Team has **Go expertise**

**Ideal Use Cases:**
- High-throughput indexing
- Real-time data synchronization
- Log processing and aggregation
- Metrics collection
- Event streaming consumers
- System-level workers

---

## Quick Decision Guide

```
┌─────────────────────────────────────┐
│ What kind of tasks?                 │
└─────────────────────────────────────┘
           │
           ├─ Data Science / ML / ETL
           │  → Use Celery (Python ecosystem)
           │
           ├─ Web-related (emails, notifications, reports)
           │  → Use BullMQ (Node.js ecosystem)
           │
           └─ High-throughput / Real-time / System tasks
              → Use Go Native Worker

┌─────────────────────────────────────┐
│ What's your API tech stack?         │
└─────────────────────────────────────┘
           │
           ├─ NestJS / Node.js API
           │  → Use BullMQ (same ecosystem)
           │
           ├─ FastAPI / Django / Flask
           │  → Use Celery (same ecosystem)
           │
           └─ Fiber / Go API
              → Use Go Native Worker (same ecosystem)

┌─────────────────────────────────────┐
│ What's your volume?                 │
└─────────────────────────────────────┘
           │
           ├─ < 5K jobs/sec
           │  → BullMQ or Celery (both handle well)
           │
           ├─ 5K-10K jobs/sec
           │  → BullMQ or Go Native Worker
           │
           └─ > 10K jobs/sec
              → Go Native Worker (best performance)
```

---

## Architecture Patterns

### Simple Queue Pattern
```
API → Queue → Worker → Database
```
- API pushes job to queue
- Worker consumes and processes
- Results stored in database

**Best For:** Basic async tasks (emails, notifications)

### Pipeline Pattern
```
API → Queue1 → Worker1 → Queue2 → Worker2 → Database
```
- Chained processing stages
- Each worker specializes in one task

**Best For:** Multi-stage processing (ETL, image processing)

### Fan-Out Pattern
```
API → Queue → [Worker1, Worker2, Worker3...] → Database
```
- Multiple workers consume same queue
- Horizontal scaling for throughput

**Best For:** High-volume processing

### Scheduled Pattern
```
Scheduler → Queue → Worker → Database
```
- Cron-like scheduling (Celery Beat, BullMQ repeatable jobs)
- Automated recurring tasks

**Best For:** Reports, analytics, backups, cleanup jobs

---

## Naming Convention

```
worker-{tech}-{purpose}
```

### Examples
```
worker-bullmq-email      # BullMQ email sending worker
worker-bullmq-reports    # BullMQ report generation
worker-celery-etl        # Celery ETL pipeline
worker-celery-ml         # Celery ML training
worker-golang-indexer    # Go indexing worker
worker-golang-sync       # Go data synchronization
```

---

## Common Features

### Job Retry Logic
All worker technologies support automatic retries with exponential backoff:
- BullMQ: `attempts: 3, backoff: { type: 'exponential', delay: 2000 }`
- Celery: `@task(autoretry_for=(Exception,), retry_backoff=True, max_retries=3)`
- Go: Manual implementation with `time.Sleep` and retry counters

### Job Prioritization
Assign priority levels to jobs:
- BullMQ: `{ priority: 1 }` (lower number = higher priority)
- Celery: Task routing with priority queues
- Go: Multiple channels with priority selection

### Job Scheduling
Schedule jobs for future execution:
- BullMQ: `{ delay: 60000 }` (delay in ms)
- Celery: `task.apply_async(eta=datetime.now() + timedelta(hours=1))`
- Go: Redis ZADD with timestamp, poll and execute

### Monitoring & Observability
- BullMQ: Bull Board (web UI)
- Celery: Flower (web UI)
- Go: Custom Prometheus metrics + Grafana

---

## Testing Strategy

### Unit Tests
Test individual job handlers:
```javascript
// BullMQ example
test('processes email job', async () => {
  const result = await emailProcessor({ to: 'test@example.com', subject: 'Test' });
  expect(result.sent).toBe(true);
});
```

### Integration Tests
Test queue interaction:
```python
# Celery example
def test_celery_task():
    result = process_data.apply(args=[test_data])
    assert result.get(timeout=10) == expected_output
```

### Load Tests
Measure throughput:
```bash
# Push 10K jobs and measure processing time
for i in {1..10000}; do
  redis-cli LPUSH myqueue "job-$i"
done
time redis-cli LLEN myqueue  # Wait until empty
```

---

## Related Documentation

- [API Technologies](../apis/README.md) - Worker job producers
- [Naming Conventions](../NAMING-CONVENTIONS.md)
- [Decision Tree](../DECISION-TREE.md)
- [Anti-Patterns](../ANTI-PATTERNS.md)

---

## Next Steps

1. **Choose a technology** based on your use case and ecosystem
2. **Review the detailed docs** for your chosen worker stack:
   - [BullMQ Documentation](./bullmq.md)
   - [Celery Documentation](./celery.md)
   - [Go Worker Documentation](./golang.md)
3. **Follow naming conventions** for your worker service
4. **Set up monitoring** (Bull Board, Flower, or Prometheus)
5. **Implement retry logic** and error handling
6. **Load test** to verify throughput meets requirements
