---
applyTo: "**/worker-bullmq-*/**"
description: BullMQ background job worker with TypeScript and Redis
---

# BullMQ Worker Service - Copilot Guidance

Quick reference for GitHub Copilot when working with BullMQ worker services.

## Reference Documentation

- **[Complete BullMQ Tech-Stack Guide](../../../docs/tech-stacks/workers/bullmq.md)** - Full implementation details, examples, and best practices
- **[Scaffold Prompt](../prompts/scaffold-worker-bullmq-service.prompt.md)** - Generate new BullMQ services

## Naming Convention

`worker-bullmq-{purpose}` (e.g., `worker-bullmq-email`, `worker-bullmq-reports`)

## Project Structure

```
services/worker-bullmq-{purpose}/
├── src/
│   ├── index.ts              # Worker initialization
│   ├── config.ts             # Configuration
│   ├── workers/              # Worker definitions
│   │   └── {job}.worker.ts
│   ├── jobs/                 # Job type definitions
│   │   └── {job}.job.ts
│   ├── processors/           # Job processing logic
│   │   └── {job}.processor.ts
│   └── utils/
│       └── logger.ts
├── test/
├── Dockerfile
├── package.json
└── tsconfig.json
```

## Copilot Coding Guidance

### Use These Patterns

1. **Separate Concerns** - Queue definition, Worker, Processor in separate files
2. **Type Safety** - Define TypeScript interfaces for all job data
3. **Error Handling** - Use worker event listeners (`completed`, `failed`)
4. **Retry Strategy** - Configure attempts and exponential backoff
5. **Job Options** - Set removeOnComplete, removeOnFail limits
6. **Monitoring** - Integrate Bull Board for queue visualization

### Quick Patterns Reference

**Queue Setup**:
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

**Worker with Concurrency**:
```typescript
import { Worker, Job } from 'bullmq';

const worker = new Worker('email', async (job: Job) => {
  const { to, subject, body } = job.data;
  await sendEmail(to, subject, body);
  return { sent: true, timestamp: Date.now() };
}, {
  connection,
  concurrency: 5,
  limiter: { max: 100, duration: 60000 },
});

worker.on('completed', (job) => logger.info(`Job ${job.id} completed`));
worker.on('failed', (job, err) => logger.error(`Job ${job.id} failed:`, err));
```

**Job Definition with Retry**:
```typescript
export interface EmailJob {
  to: string;
  subject: string;
  body: string;
}

export async function addEmailJob(data: EmailJob) {
  return emailQueue.add('send-email', data, {
    attempts: 3,
    backoff: { type: 'exponential', delay: 1000 },
    removeOnComplete: 100,
    removeOnFail: 50,
  });
}
```

**Scheduled Jobs**:
```typescript
await queue.add('daily-report', data, {
  repeat: { pattern: '0 9 * * *' }, // Cron: 9 AM daily
});
```

### Code Style

- **Naming**: camelCase for variables/functions, PascalCase for interfaces
- **Job Names**: Use kebab-case (`'send-email'`, not `'sendEmail'`)
- **Event Handlers**: Always attach `completed` and `failed` listeners
- **Cleanup**: Configure `removeOnComplete` and `removeOnFail`
- **Logging**: Use structured logging with job context

### Anti-Patterns to Avoid

❌ Not setting job retry limits  
❌ Missing error handling in processors  
❌ Blocking/synchronous operations in workers  
❌ No rate limiting configured  
❌ Not cleaning up completed jobs  
❌ Forgetting to handle `failed` events  
❌ Using same Redis instance for multiple purposes  

For detailed examples, Bull Board setup, deployment guides, testing strategies, and advanced patterns, refer to the [BullMQ Tech-Stack Documentation](../../../docs/tech-stacks/workers/bullmq.md).

