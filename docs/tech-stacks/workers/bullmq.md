# BullMQ Worker - Node.js Background Job Processing

Modern, Redis-based queue for Node.js applications with TypeScript support and excellent monitoring capabilities.

---

## Overview

**Naming Convention:** `worker-bullmq-{purpose}`

**Examples:**
- `worker-bullmq-email` - Email sending worker
- `worker-bullmq-reports` - Report generation worker  
- `worker-bullmq-notifications` - Push notification worker

**BullMQ** is a modern, high-performance queue for Node.js applications, backed by Redis. It provides job prioritization, delays, retries, rate limiting, and a beautiful monitoring UI (Bull Board).

---

## Technology Stack

```
┌─────────────────────────────────────────────┐
│ Runtime & Language                          │
│  - Node.js 20+                              │
│  - TypeScript 5+                            │
│  - ES Modules (ESM)                         │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Queue & Messaging                           │
│  - BullMQ 4+ (job queue)                    │
│  - Redis 7+ (message broker + storage)      │
│  - ioredis (Redis client)                   │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Monitoring & Management                     │
│  - Bull Board (web UI)                      │
│  - @bull-board/api                          │
│  - @bull-board/express (integration)        │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Development & Testing                       │
│  - Jest (testing framework)                 │
│  - ts-node (TypeScript execution)           │
│  - nodemon (hot reload)                     │
│  - dotenv (environment variables)           │
└─────────────────────────────────────────────┘
```

---

## Features

### Core Queue Features
- ✅ **Redis-backed** - Reliable, persistent job storage
- ✅ **Job priorities** - Process important jobs first
- ✅ **Delayed jobs** - Schedule jobs for future execution
- ✅ **Job retries** - Automatic retry with exponential backoff
- ✅ **Rate limiting** - Throttle job processing
- ✅ **Job events** - Track progress, completion, failure
- ✅ **Repeatable jobs** - Cron-like scheduled tasks
- ✅ **Job dependencies** - Wait for other jobs to complete
- ✅ **Concurrency control** - Limit parallel workers

### Developer Experience
- ✅ **TypeScript support** - Full type safety
- ✅ **Bull Board UI** - Beautiful monitoring dashboard
- ✅ **Job progress tracking** - Real-time progress updates
- ✅ **Automatic reconnection** - Resilient to Redis failures
- ✅ **Graceful shutdown** - Finish jobs before exit
- ✅ **Metrics export** - Prometheus-compatible metrics

---

## Ideal Use Cases

### ✅ Perfect For:
1. **Email Sending**
   - Transactional emails (welcome, password reset)
   - Newsletter campaigns
   - Bulk email delivery

2. **Report Generation**
   - PDF/Excel reports
   - Data exports
   - Analytics reports

3. **Image/Video Processing**
   - Thumbnail generation
   - Image optimization
   - Video transcoding

4. **Notification Systems**
   - Push notifications
   - SMS sending
   - Webhook delivery

5. **Data Processing**
   - CSV/JSON imports
   - Data transformation
   - Batch updates

6. **Integration Tasks**
   - Third-party API calls
   - Data synchronization
   - Webhook processing

### ❌ Not Ideal For:
- ❌ Extremely high throughput (> 10K jobs/sec) → Use Go worker
- ❌ Python data science tasks → Use Celery
- ❌ Real-time stream processing → Use stream processor

---

## Project Structure

```
worker-bullmq-{purpose}/
├── src/
│   ├── index.ts              # Entry point (start worker)
│   ├── queue.ts              # Queue setup and configuration
│   ├── worker.ts             # Worker definition
│   ├── processors/           # Job processors
│   │   ├── email.processor.ts
│   │   ├── report.processor.ts
│   │   └── index.ts
│   ├── jobs/                 # Job types and schemas
│   │   ├── email.job.ts
│   │   └── report.job.ts
│   ├── config/
│   │   ├── redis.ts          # Redis connection
│   │   └── bull-board.ts     # Monitoring UI setup
│   └── utils/
│       ├── logger.ts
│       └── metrics.ts
├── tests/
│   ├── processors/
│   │   └── email.processor.test.ts
│   └── integration/
│       └── queue.test.ts
├── .env.example
├── .env
├── package.json
├── tsconfig.json
├── jest.config.js
├── Dockerfile
└── README.md
```

---

## Code Examples

### Queue Configuration

```typescript
// src/config/redis.ts
import { ConnectionOptions } from 'bullmq';

export const redisConnection: ConnectionOptions = {
  host: process.env.REDIS_HOST || 'localhost',
  port: parseInt(process.env.REDIS_PORT || '6379'),
  password: process.env.REDIS_PASSWORD,
  maxRetriesPerRequest: null,
};
```

### Job Type Definition

```typescript
// src/jobs/email.job.ts
import { z } from 'zod';

export const EmailJobSchema = z.object({
  to: z.string().email(),
  subject: z.string().min(1),
  body: z.string(),
  template: z.string().optional(),
  data: z.record(z.any()).optional(),
});

export type EmailJob = z.infer<typeof EmailJobSchema>;
```

### Queue Setup

```typescript
// src/queue.ts
import { Queue } from 'bullmq';
import { redisConnection } from './config/redis';
import { EmailJob } from './jobs/email.job';

export const emailQueue = new Queue<EmailJob>('email-queue', {
  connection: redisConnection,
  defaultJobOptions: {
    attempts: 3,
    backoff: {
      type: 'exponential',
      delay: 2000,
    },
    removeOnComplete: {
      age: 3600, // Keep completed jobs for 1 hour
      count: 1000,
    },
    removeOnFail: {
      age: 86400, // Keep failed jobs for 24 hours
    },
  },
});

// Add job to queue
export async function sendEmail(job: EmailJob) {
  return emailQueue.add('send-email', job, {
    priority: job.template === 'password-reset' ? 1 : 5,
  });
}
```

### Job Processor

```typescript
// src/processors/email.processor.ts
import { Job } from 'bullmq';
import { EmailJob } from '../jobs/email.job';
import nodemailer from 'nodemailer';

const transporter = nodemailer.createTransport({
  host: process.env.SMTP_HOST,
  port: parseInt(process.env.SMTP_PORT || '587'),
  secure: false,
  auth: {
    user: process.env.SMTP_USER,
    pass: process.env.SMTP_PASS,
  },
});

export async function processEmail(job: Job<EmailJob>) {
  const { to, subject, body, template, data } = job.data;

  // Update progress
  await job.updateProgress(10);

  // Render template if provided
  let htmlBody = body;
  if (template && data) {
    htmlBody = await renderTemplate(template, data);
  }

  await job.updateProgress(50);

  // Send email
  const info = await transporter.sendMail({
    from: process.env.SMTP_FROM,
    to,
    subject,
    html: htmlBody,
  });

  await job.updateProgress(100);

  return { messageId: info.messageId, sent: true };
}

async function renderTemplate(template: string, data: any): Promise<string> {
  // Template rendering logic (Handlebars, EJS, etc.)
  return `<html><body>...</body></html>`;
}
```

### Worker Setup

```typescript
// src/worker.ts
import { Worker, Job } from 'bullmq';
import { redisConnection } from './config/redis';
import { processEmail } from './processors/email.processor';
import { EmailJob } from './jobs/email.job';

export const emailWorker = new Worker<EmailJob>(
  'email-queue',
  async (job: Job<EmailJob>) => {
    console.log(`Processing job ${job.id}: ${job.name}`);
    return processEmail(job);
  },
  {
    connection: redisConnection,
    concurrency: 10, // Process 10 jobs in parallel
    limiter: {
      max: 100, // Max 100 jobs
      duration: 60000, // per 60 seconds
    },
  }
);

// Event listeners
emailWorker.on('completed', (job) => {
  console.log(`Job ${job.id} completed`);
});

emailWorker.on('failed', (job, err) => {
  console.error(`Job ${job?.id} failed:`, err);
});

emailWorker.on('progress', (job, progress) => {
  console.log(`Job ${job.id} progress: ${progress}%`);
});

// Graceful shutdown
process.on('SIGTERM', async () => {
  await emailWorker.close();
  process.exit(0);
});
```

### Bull Board Monitoring UI

```typescript
// src/config/bull-board.ts
import { createBullBoard } from '@bull-board/api';
import { BullMQAdapter } from '@bull-board/api/bullMQAdapter';
import { ExpressAdapter } from '@bull-board/express';
import express from 'express';
import { emailQueue } from '../queue';

const serverAdapter = new ExpressAdapter();
serverAdapter.setBasePath('/admin/queues');

createBullBoard({
  queues: [new BullMQAdapter(emailQueue)],
  serverAdapter,
});

const app = express();
app.use('/admin/queues', serverAdapter.getRouter());

app.listen(3000, () => {
  console.log('Bull Board available at http://localhost:3000/admin/queues');
});
```

### Entry Point

```typescript
// src/index.ts
import { emailWorker } from './worker';
import './config/bull-board'; // Start monitoring UI

console.log('🚀 Worker started');
console.log('📊 Bull Board: http://localhost:3000/admin/queues');

// Keep process alive
process.on('SIGINT', async () => {
  console.log('Shutting down gracefully...');
  await emailWorker.close();
  process.exit(0);
});
```

---

## Advanced Features

### Repeatable Jobs (Cron)

```typescript
// Schedule daily report at 9 AM
await emailQueue.add(
  'daily-report',
  { to: 'admin@example.com', subject: 'Daily Report', body: '...' },
  {
    repeat: {
      pattern: '0 9 * * *', // Cron expression
      tz: 'America/New_York',
    },
  }
);
```

### Job Dependencies

```typescript
// Process job after parent completes
const parentJob = await emailQueue.add('parent', data);

await emailQueue.add('child', childData, {
  parent: {
    id: parentJob.id,
    queue: 'email-queue',
  },
});
```

### Job Priority

```typescript
// Higher priority (lower number = higher priority)
await emailQueue.add('urgent-email', data, { priority: 1 });

// Lower priority
await emailQueue.add('newsletter', data, { priority: 10 });
```

### Delayed Jobs

```typescript
// Send email in 1 hour
await emailQueue.add('reminder', data, {
  delay: 60 * 60 * 1000, // 1 hour in milliseconds
});
```

---

## Testing

### Unit Test (Processor)

```typescript
// tests/processors/email.processor.test.ts
import { processEmail } from '../../src/processors/email.processor';
import { Job } from 'bullmq';

describe('Email Processor', () => {
  it('should send email successfully', async () => {
    const mockJob = {
      id: '1',
      data: {
        to: 'test@example.com',
        subject: 'Test',
        body: 'Hello',
      },
      updateProgress: jest.fn(),
    } as unknown as Job;

    const result = await processEmail(mockJob);

    expect(result.sent).toBe(true);
    expect(mockJob.updateProgress).toHaveBeenCalledWith(100);
  });
});
```

### Integration Test

```typescript
// tests/integration/queue.test.ts
import { emailQueue } from '../../src/queue';
import { emailWorker } from '../../src/worker';

describe('Email Queue Integration', () => {
  afterAll(async () => {
    await emailQueue.close();
    await emailWorker.close();
  });

  it('should process email job', async () => {
    const job = await emailQueue.add('test-email', {
      to: 'test@example.com',
      subject: 'Test',
      body: 'Hello',
    });

    const result = await job.waitUntilFinished();
    expect(result.sent).toBe(true);
  });
});
```

---

## Deployment

### Docker

```dockerfile
# Dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

CMD ["node", "dist/index.js"]
```

### Environment Variables

```env
# .env
NODE_ENV=production
REDIS_HOST=redis
REDIS_PORT=6379
REDIS_PASSWORD=secret

SMTP_HOST=smtp.example.com
SMTP_PORT=587
SMTP_USER=noreply@example.com
SMTP_PASS=password
SMTP_FROM=noreply@example.com

WORKER_CONCURRENCY=10
```

### Docker Compose

```yaml
version: '3.8'
services:
  worker:
    build: .
    environment:
      - REDIS_HOST=redis
      - REDIS_PORT=6379
    depends_on:
      - redis
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    volumes:
      - redis-data:/data
    restart: unless-stopped

volumes:
  redis-data:
```

---

## Monitoring & Metrics

### Bull Board Dashboard
Access at `http://localhost:3000/admin/queues`

Features:
- View active/completed/failed jobs
- Retry failed jobs
- Clean old jobs
- View job details and logs
- Monitor queue health

### Prometheus Metrics

```typescript
// src/utils/metrics.ts
import { register, Counter, Gauge } from 'prom-client';

export const jobsProcessed = new Counter({
  name: 'bullmq_jobs_processed_total',
  help: 'Total number of processed jobs',
  labelNames: ['queue', 'status'],
});

export const jobsActive = new Gauge({
  name: 'bullmq_jobs_active',
  help: 'Number of active jobs',
  labelNames: ['queue'],
});

// Expose metrics endpoint
app.get('/metrics', async (req, res) => {
  res.set('Content-Type', register.contentType);
  res.end(await register.metrics());
});
```

---

## Best Practices

### ✅ Do:
- Use **TypeScript** for type safety
- Implement **retry logic** with exponential backoff
- Set **appropriate timeouts** for jobs
- Use **Bull Board** for monitoring
- Implement **graceful shutdown**
- Log job **start/completion/failure**
- Use **job priorities** for important tasks
- Set **TTL for completed jobs** to avoid memory bloat
- Use **concurrency limits** to avoid overload
- Implement **circuit breakers** for external API calls

### ❌ Don't:
- Don't store **large payloads** in job data (use references)
- Don't use **blocking operations** in processors
- Don't **ignore failed jobs** (investigate and retry/fix)
- Don't run **CPU-intensive tasks** (use separate worker or different tech)
- Don't forget to **clean up old jobs**
- Don't use **synchronous APIs** (use async/await)

---

## Common Pitfalls

### 1. Large Job Payloads
**Problem:** Storing large data in job → Redis memory issues

**Solution:**
```typescript
// ❌ Bad: Store entire file in job
await queue.add('process-file', { file: largeBuffer });

// ✅ Good: Store file reference
await queue.add('process-file', { fileId: '123', bucket: 's3-bucket' });
```

### 2. Blocking Operations
**Problem:** Synchronous operations block event loop

**Solution:**
```typescript
// ❌ Bad: Blocking file read
const data = fs.readFileSync('/path/to/file');

// ✅ Good: Async file read
const data = await fs.promises.readFile('/path/to/file');
```

### 3. Memory Leaks
**Problem:** Not cleaning up completed jobs

**Solution:**
```typescript
// ✅ Configure automatic cleanup
const queue = new Queue('my-queue', {
  defaultJobOptions: {
    removeOnComplete: { age: 3600, count: 1000 },
    removeOnFail: { age: 86400 },
  },
});
```

---

## Related Documentation

- [Workers Overview](./README.md)
- [Celery Worker](./celery.md) - Python alternative
- [Go Worker](./golang.md) - High-performance alternative
- [Naming Conventions](../NAMING-CONVENTIONS.md)

---

## Reference Files

- **Instruction File:** `service-worker-bullmq.instructions.md`
- **Scaffold Prompt:** `scaffold-worker-bullmq-service.md`

---

## Next Steps

1. **Set up Redis** connection and verify connectivity
2. **Define job types** with Zod or TypeScript interfaces
3. **Implement processors** for each job type
4. **Configure Bull Board** for monitoring
5. **Write tests** for processors and queue integration
6. **Deploy worker** with Docker
7. **Monitor performance** and adjust concurrency/rate limits
