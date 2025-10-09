---
applyTo: "**/worker-bullmq-*/**"
description: BullMQ background job worker with TypeScript and Redis
---

# Worker BullMQ - Node.js Background Jobs

Process asynchronous tasks with BullMQ queue system backed by Redis.

## Naming Convention

`worker-bullmq-{purpose}` (e.g., `worker-bullmq-email`, `worker-bullmq-reports`)

## Technology Stack

- TypeScript 5+ (language)
- BullMQ 5+ (queue system)
- Redis (queue storage)
- ioredis (Redis client)
- Bull Board (monitoring UI)
- Pino (logging)
- Jest (testing)

## Directory Structure

```
services/worker-bullmq-{purpose}/
├── src/
│   ├── index.ts
│   ├── config.ts
│   ├── workers/
│   │   ├── email.worker.ts
│   │   └── report.worker.ts
│   ├── jobs/
│   │   ├── email.job.ts
│   │   └── report.job.ts
│   ├── processors/
│   │   ├── email.processor.ts
│   │   └── report.processor.ts
│   └── utils/
│       └── logger.ts
├── test/
│   └── processors/
├── Dockerfile
├── package.json
└── tsconfig.json
```

## Queue Setup

```typescript
import { Queue } from 'bullmq';
import Redis from 'ioredis';

const connection = new Redis({
  host: process.env.REDIS_HOST,
  port: Number(process.env.REDIS_PORT),
  maxRetriesPerRequest: null,
});

export const emailQueue = new Queue('email', { connection });
```

## Worker Implementation

```typescript
import { Worker, Job } from 'bullmq';

const worker = new Worker('email', async (job: Job) => {
  const { to, subject, body } = job.data;
  
  await sendEmail(to, subject, body);
  
  return { sent: true, timestamp: Date.now() };
}, {
  connection,
  concurrency: 5,
  limiter: {
    max: 100,
    duration: 60000, // 100 jobs per minute
  },
});

worker.on('completed', (job) => {
  logger.info(`Job ${job.id} completed`);
});

worker.on('failed', (job, err) => {
  logger.error(`Job ${job.id} failed:`, err);
});
```

## Job Definition

```typescript
export interface EmailJob {
  to: string;
  subject: string;
  body: string;
  attachments?: string[];
}

export async function addEmailJob(data: EmailJob) {
  return emailQueue.add('send-email', data, {
    attempts: 3,
    backoff: {
      type: 'exponential',
      delay: 1000,
    },
    removeOnComplete: 100,
    removeOnFail: 50,
  });
}
```

## Monitoring Dashboard

Bull Board integration:
```typescript
import { createBullBoard } from '@bull-board/api';
import { BullMQAdapter } from '@bull-board/api/bullMQAdapter';
import { ExpressAdapter } from '@bull-board/express';

const serverAdapter = new ExpressAdapter();
serverAdapter.setBasePath('/admin/queues');

createBullBoard({
  queues: [new BullMQAdapter(emailQueue)],
  serverAdapter,
});

app.use('/admin/queues', serverAdapter.getRouter());
```

## Job Scheduling

```typescript
// Repeat every day at 9 AM
await emailQueue.add('daily-report', data, {
  repeat: {
    pattern: '0 9 * * *',
  },
});
```

## Error Handling

```typescript
worker.on('failed', async (job, err) => {
  if (job.attemptsMade >= job.opts.attempts) {
    // Move to dead letter queue or alert
    await alerting.sendAlert('Job permanently failed', {
      jobId: job.id,
      error: err.message,
    });
  }
});
```

## Testing

```typescript
describe('Email Processor', () => {
  it('sends email successfully', async () => {
    const job = { data: { to: 'test@example.com', subject: 'Test' } };
    const result = await emailProcessor(job);
    expect(result.sent).toBe(true);
  });
});
```

## Health Checks

```typescript
async function healthCheck() {
  const health = await emailQueue.getJobCounts();
  return {
    active: health.active,
    waiting: health.waiting,
    failed: health.failed,
  };
}
```

## Anti-Patterns

- ❌ Not setting job retry limits
- ❌ No error handling in processors
- ❌ Blocking operations in workers
- ❌ Missing rate limiting
- ❌ Not cleaning up completed jobs
