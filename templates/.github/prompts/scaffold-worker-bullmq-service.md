# Scaffold BullMQ Worker Service

**Objective:** Create a production-ready BullMQ worker service with complete directory structure, Docker configuration, monitoring, and comprehensive documentation.

---

## 📋 Service Specification

Before running this prompt, gather the following information:

1. **Service Name:** `[e.g., worker-bullmq-email, worker-bullmq-reports]`
2. **Service Purpose:** `[Brief description of what jobs this worker processes]`
3. **Queue Names:** `[List of queues to process, e.g., email, notifications, reports]`
4. **Job Types:** `[List of job types per queue]`
5. **Redis Required:** `[Yes - required for BullMQ]`
6. **Scheduling Required:** `[Yes/No - for repeatable jobs]`
7. **External APIs:** `[List any external services to integrate]`
8. **Port:** `[Default: 3000 for Bull Board, or specify custom]`

---

## 🏗️ Architecture Overview

```
┌────────────────────────────────────────────────────────────────────┐
│                   BullMQ Worker Architecture                       │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                    Queue Manager Layer                       │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │   Queue    │  │ Scheduler  │  │Bull Board  │            │ │
│  │  │  Registry  │  │(Repeatable)│  │   (UI)     │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
│            ↓                ↓                ↓                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                      Worker Layer                            │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │  Job       │  │   Job      │  │   Error    │            │ │
│  │  │ Processors │  │ Validators │  │  Handlers  │            │ │
│  │  └──────┬─────┘  └────────────┘  └────────────┘            │ │
│  └─────────┼────────────────────────────────────────────────────┘ │
│            │                                                      │
│            ↓                                                      │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                    Service Layer                             │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │  Business  │  │  External  │  │  Storage   │            │ │
│  │  │   Logic    │  │API Clients │  │  Services  │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
└────────────┼────────────────┼────────────────┼────────────────────┘
             │                │                │
             ↓                ↓                ↓
    ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
    │    Redis    │  │External APIs│  │  Database   │
    │  (Backing   │  │             │  │  (Optional) │
    │   Service)  │  │             │  │             │
    └─────────────┘  └─────────────┘  └─────────────┘
```

---

## 📁 Directory Structure

Create the following complete directory structure:

```
services/[service-name]/
├── src/
│   ├── index.ts                   ← Application entry point
│   ├── config/
│   │   ├── redis.config.ts        ← Redis configuration
│   │   ├── queue.config.ts        ← Queue configuration
│   │   └── bull-board.config.ts   ← Bull Board UI configuration
│   │
│   ├── queues/                    ← Queue definitions
│   │   ├── index.ts               ← Queue registry
│   │   ├── email.queue.ts         ← Example: Email queue
│   │   └── report.queue.ts        ← Example: Report queue
│   │
│   ├── workers/                   ← Worker processors
│   │   ├── index.ts               ← Worker registry
│   │   ├── email.worker.ts        ← Example: Email worker
│   │   └── report.worker.ts       ← Example: Report worker
│   │
│   ├── jobs/                      ← Job type definitions
│   │   ├── email/
│   │   │   ├── send-email.job.ts
│   │   │   └── send-bulk-email.job.ts
│   │   └── report/
│   │       └── generate-report.job.ts
│   │
│   ├── services/                  ← Business logic services
│   │   ├── email.service.ts
│   │   └── report.service.ts
│   │
│   ├── utils/                     ← Utility functions
│   │   ├── logger.ts              ← Winston logger
│   │   ├── retry.ts               ← Retry utilities
│   │   └── metrics.ts             ← Metrics collection
│   │
│   └── types/                     ← TypeScript types
│       ├── jobs.types.ts          ← Job data types
│       └── queue.types.ts         ← Queue types
│
├── test/                          ← Tests
│   ├── unit/
│   │   ├── workers/
│   │   └── services/
│   └── integration/
│       └── queues/
│
├── .eslintrc.js                   ← ESLint config
├── .prettierrc                    ← Prettier config
├── tsconfig.json                  ← TypeScript config
├── package.json                   ← Dependencies
├── Dockerfile                     ← Production Docker image
├── .dockerignore                  ← Docker ignore patterns
├── README.md                      ← Service documentation
└── .env.example                   ← Environment variables template
```

---

## 📦 Step 1: `package.json`

```json
{
  "name": "[service-name]",
  "version": "1.0.0",
  "description": "[Service Purpose]",
  "main": "dist/index.js",
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "dev": "ts-node-dev --respawn --transpile-only src/index.ts",
    "lint": "eslint 'src/**/*.ts' --fix",
    "format": "prettier --write 'src/**/*.ts'",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:cov": "jest --coverage"
  },
  "dependencies": {
    "bullmq": "^5.0.0",
    "ioredis": "^5.3.2",
    "@bull-board/api": "^5.11.0",
    "@bull-board/express": "^5.11.0",
    "express": "^4.18.2",
    "dotenv": "^16.3.1",
    "winston": "^3.11.0",
    "winston-daily-rotate-file": "^4.7.1",
    "axios": "^1.6.2",
    "zod": "^3.22.4"
  },
  "devDependencies": {
    "@types/express": "^4.17.21",
    "@types/node": "^20.10.5",
    "@typescript-eslint/eslint-plugin": "^6.15.0",
    "@typescript-eslint/parser": "^6.15.0",
    "eslint": "^8.56.0",
    "prettier": "^3.1.1",
    "ts-node-dev": "^2.0.0",
    "typescript": "^5.3.3",
    "jest": "^29.7.0",
    "ts-jest": "^29.1.1",
    "@types/jest": "^29.5.11"
  }
}
```

---

## 🔧 Step 2: `src/index.ts` - Application Entry Point

```typescript
import express from 'express';
import { createBullBoard } from '@bull-board/api';
import { BullMQAdapter } from '@bull-board/api/bullMQAdapter';
import { ExpressAdapter } from '@bull-board/express';
import { queues } from './queues';
import { workers } from './workers';
import { logger } from './utils/logger';
import config from './config/queue.config';

const app = express();
const port = process.env.BULL_BOARD_PORT || 3000;

// Bull Board UI setup
const serverAdapter = new ExpressAdapter();
serverAdapter.setBasePath('/admin/queues');

createBullBoard({
  queues: Object.values(queues).map(queue => new BullMQAdapter(queue)),
  serverAdapter: serverAdapter,
});

app.use('/admin/queues', serverAdapter.getRouter());

// Health check endpoint
app.get('/health', (req, res) => {
  res.json({
    status: 'healthy',
    timestamp: new Date().toISOString(),
    queues: Object.keys(queues),
    workers: Object.keys(workers).length,
  });
});

// Start server
app.listen(port, () => {
  logger.info(`Bull Board running at http://localhost:${port}/admin/queues`);
  logger.info(`Health check at http://localhost:${port}/health`);
});

// Start all workers
Object.entries(workers).forEach(([name, worker]) => {
  logger.info(`Starting worker: ${name}`);
  worker.run();
});

// Graceful shutdown
process.on('SIGTERM', async () => {
  logger.info('SIGTERM received, closing workers...');
  
  await Promise.all(
    Object.values(workers).map(worker => worker.close())
  );
  
  await Promise.all(
    Object.values(queues).map(queue => queue.close())
  );
  
  logger.info('All workers closed');
  process.exit(0);
});
```

---

## 🔧 Step 3: `src/config/redis.config.ts` - Redis Configuration

```typescript
import { ConnectionOptions } from 'bullmq';

export const redisConnection: ConnectionOptions = {
  host: process.env.REDIS_HOST || 'localhost',
  port: parseInt(process.env.REDIS_PORT || '6379'),
  password: process.env.REDIS_PASSWORD || undefined,
  db: parseInt(process.env.REDIS_DB || '0'),
  maxRetriesPerRequest: null,
  enableReadyCheck: false,
};
```

---

## 🔧 Step 4: `src/config/queue.config.ts` - Queue Configuration

```typescript
import { QueueOptions, WorkerOptions } from 'bullmq';
import { redisConnection } from './redis.config';

export const defaultQueueOptions: QueueOptions = {
  connection: redisConnection,
  defaultJobOptions: {
    attempts: 3,
    backoff: {
      type: 'exponential',
      delay: 1000,
    },
    removeOnComplete: {
      age: 24 * 3600, // 24 hours
      count: 1000,
    },
    removeOnFail: {
      age: 7 * 24 * 3600, // 7 days
    },
  },
};

export const defaultWorkerOptions: WorkerOptions = {
  connection: redisConnection,
  concurrency: parseInt(process.env.WORKER_CONCURRENCY || '5'),
  limiter: {
    max: parseInt(process.env.RATE_LIMIT_MAX || '10'),
    duration: parseInt(process.env.RATE_LIMIT_DURATION || '1000'),
  },
};

export default {
  defaultQueueOptions,
  defaultWorkerOptions,
};
```

---

## 🔧 Step 5: `src/queues/email.queue.ts` - Example Email Queue

```typescript
import { Queue } from 'bullmq';
import { defaultQueueOptions } from '../config/queue.config';
import { EmailJobData } from '../types/jobs.types';

export const emailQueue = new Queue<EmailJobData>('email', defaultQueueOptions);

// Add job helper methods
export const addEmailJob = async (
  jobType: string,
  data: EmailJobData,
  options = {}
) => {
  return await emailQueue.add(jobType, data, {
    ...options,
    removeOnComplete: true,
    removeOnFail: false,
  });
};

// Schedule repeatable job
export const scheduleEmailJob = async (
  jobType: string,
  data: EmailJobData,
  cronExpression: string
) => {
  return await emailQueue.add(jobType, data, {
    repeat: {
      pattern: cronExpression,
    },
  });
};
```

---

## 🔧 Step 6: `src/queues/index.ts` - Queue Registry

```typescript
import { emailQueue } from './email.queue';
import { reportQueue } from './report.queue';

export const queues = {
  email: emailQueue,
  report: reportQueue,
};

export { emailQueue, reportQueue };
```

---

## 🔧 Step 7: `src/workers/email.worker.ts` - Example Email Worker

```typescript
import { Worker, Job } from 'bullmq';
import { defaultWorkerOptions } from '../config/queue.config';
import { EmailJobData } from '../types/jobs.types';
import { emailService } from '../services/email.service';
import { logger } from '../utils/logger';

const QUEUE_NAME = 'email';

const processEmailJob = async (job: Job<EmailJobData>) => {
  logger.info(`Processing email job ${job.id}`, {
    jobId: job.id,
    jobType: job.name,
    attempt: job.attemptsMade,
  });

  try {
    switch (job.name) {
      case 'send-email':
        await emailService.sendEmail(job.data);
        break;
      
      case 'send-bulk-email':
        await emailService.sendBulkEmail(job.data);
        break;
      
      default:
        throw new Error(`Unknown job type: ${job.name}`);
    }

    logger.info(`Email job ${job.id} completed successfully`);
    
    return { success: true, timestamp: new Date().toISOString() };
  } catch (error) {
    logger.error(`Email job ${job.id} failed`, {
      error: error.message,
      stack: error.stack,
    });
    throw error;
  }
};

export const emailWorker = new Worker<EmailJobData>(
  QUEUE_NAME,
  processEmailJob,
  {
    ...defaultWorkerOptions,
    concurrency: 10, // Override for this worker
  }
);

// Event listeners
emailWorker.on('completed', (job) => {
  logger.info(`Job ${job.id} completed`, {
    jobId: job.id,
    queue: QUEUE_NAME,
    duration: job.finishedOn - job.processedOn,
  });
});

emailWorker.on('failed', (job, err) => {
  logger.error(`Job ${job.id} failed`, {
    jobId: job.id,
    queue: QUEUE_NAME,
    error: err.message,
    attempts: job.attemptsMade,
  });
});

emailWorker.on('error', (err) => {
  logger.error(`Worker error in ${QUEUE_NAME}`, { error: err.message });
});
```

---

## 🔧 Step 8: `src/workers/index.ts` - Worker Registry

```typescript
import { emailWorker } from './email.worker';
import { reportWorker } from './report.worker';

export const workers = {
  email: emailWorker,
  report: reportWorker,
};

export { emailWorker, reportWorker };
```

---

## 🔧 Step 9: `src/types/jobs.types.ts` - Job Type Definitions

```typescript
export interface EmailJobData {
  to: string | string[];
  subject: string;
  body: string;
  html?: string;
  attachments?: Array<{
    filename: string;
    content: string | Buffer;
  }>;
}

export interface ReportJobData {
  reportType: string;
  userId: string;
  parameters: Record<string, any>;
  outputFormat: 'pdf' | 'csv' | 'xlsx';
}

export type JobData = EmailJobData | ReportJobData;
```

---

## 🔧 Step 10: `src/services/email.service.ts` - Email Service

```typescript
import { logger } from '../utils/logger';
import { EmailJobData } from '../types/jobs.types';

class EmailService {
  async sendEmail(data: EmailJobData): Promise<void> {
    logger.info('Sending email', { to: data.to, subject: data.subject });
    
    // TODO: Implement actual email sending logic
    // Example: using nodemailer, SendGrid, AWS SES, etc.
    
    await new Promise(resolve => setTimeout(resolve, 1000)); // Simulate delay
    
    logger.info('Email sent successfully', { to: data.to });
  }

  async sendBulkEmail(data: EmailJobData): Promise<void> {
    const recipients = Array.isArray(data.to) ? data.to : [data.to];
    logger.info('Sending bulk email', { count: recipients.length });
    
    // Send emails in batches
    const batchSize = 10;
    for (let i = 0; i < recipients.length; i += batchSize) {
      const batch = recipients.slice(i, i + batchSize);
      await Promise.all(
        batch.map(recipient =>
          this.sendEmail({ ...data, to: recipient })
        )
      );
    }
    
    logger.info('Bulk email sent successfully');
  }
}

export const emailService = new EmailService();
```

---

## 🔧 Step 11: `src/utils/logger.ts` - Winston Logger

```typescript
import winston from 'winston';
import DailyRotateFile from 'winston-daily-rotate-file';

const logFormat = winston.format.combine(
  winston.format.timestamp({ format: 'YYYY-MM-DD HH:mm:ss' }),
  winston.format.errors({ stack: true }),
  winston.format.json()
);

const transports: winston.transport[] = [
  new winston.transports.Console({
    format: winston.format.combine(
      winston.format.colorize(),
      winston.format.simple()
    ),
  }),
];

// Add file rotation in production
if (process.env.NODE_ENV === 'production') {
  transports.push(
    new DailyRotateFile({
      filename: 'logs/worker-%DATE%.log',
      datePattern: 'YYYY-MM-DD',
      maxSize: '20m',
      maxFiles: '14d',
    })
  );
}

export const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: logFormat,
  transports,
});
```

---

## 🔧 Step 12: `tsconfig.json` - TypeScript Configuration

```json
{
  "compilerOptions": {
    "target": "ES2021",
    "module": "commonjs",
    "lib": ["ES2021"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "moduleResolution": "node",
    "types": ["node", "jest"]
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "test"]
}
```

---

## 🐳 Step 13: `Dockerfile` - Production Docker Image

```dockerfile
# Build stage
FROM node:20-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production && \
    npm cache clean --force

# Copy source code
COPY . .

# Build TypeScript
RUN npm run build

# Production stage
FROM node:20-alpine

WORKDIR /app

# Copy built application
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package*.json ./

# Create non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S worker -u 1001 && \
    mkdir -p logs && \
    chown -R worker:nodejs logs

USER worker

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3000/health', (res) => { process.exit(res.statusCode === 200 ? 0 : 1); })"

EXPOSE 3000

CMD ["node", "dist/index.js"]
```

---

## 🔧 Step 14: `.env.example` - Environment Variables Template

```bash
# Application
NODE_ENV=production
BULL_BOARD_PORT=3000
LOG_LEVEL=info

# Redis
REDIS_HOST=redis
REDIS_PORT=6379
REDIS_PASSWORD=
REDIS_DB=0

# Worker Configuration
WORKER_CONCURRENCY=5
RATE_LIMIT_MAX=10
RATE_LIMIT_DURATION=1000

# External Services (if needed)
# SENDGRID_API_KEY=
# AWS_SES_REGION=
# SMTP_HOST=
# SMTP_PORT=
```

---

## 🐳 Step 15: Docker Compose Integration

Add to `docker-compose.yml`:

```yaml
  [service-name]:
    build:
      context: ./services/[service-name]
      dockerfile: Dockerfile
    container_name: [service-name]
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - REDIS_HOST=redis
    env_file:
      - ./config/.env
    depends_on:
      redis:
        condition: service_healthy
    networks:
      - microservices
    healthcheck:
      test: ["CMD", "node", "-e", "require('http').get('http://localhost:3000/health', (res) => { process.exit(res.statusCode === 200 ? 0 : 1); })"]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 10s
```

---

## 🔧 Step 16: `.devcontainer/[service-name]-container/devcontainer.json`

Location: Project root `.devcontainer/[service-name]-container/devcontainer.json`

```json
{
  "name": "[service-name]",
  "dockerComposeFile": ["../../docker-compose.development.yml"],
  "service": "[service-name]",
  "workspaceFolder": "/workspace/services/[service-name]",
  "customizations": {
    "vscode": {
      "extensions": [
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode",
        "firsttris.vscode-jest-runner"
      ],
      "settings": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
          "source.fixAll.eslint": "explicit"
        }
      }
    }
  },
  "forwardPorts": [3000],
  "postCreateCommand": "npm install",
  "remoteUser": "node"
}
```

---

## 📝 Step 17: `README.md` - Service Documentation

```markdown
# [Service Name]

[Service Purpose]

## Technology Stack

- **Queue:** BullMQ 5
- **Runtime:** Node.js 20
- **Language:** TypeScript 5
- **Storage:** Redis
- **Monitoring:** Bull Board

## Features

- ✅ Multiple queue support
- ✅ Job prioritization and scheduling
- ✅ Rate limiting
- ✅ Automatic retries with exponential backoff
- ✅ Bull Board UI for monitoring
- ✅ Structured logging with Winston
- ✅ Health check endpoints
- ✅ Graceful shutdown
- ✅ Type-safe job definitions

## Getting Started

### Development

```bash
# Install dependencies
npm install

# Run development server
npm run dev

# Run tests
npm run test
```

### Bull Board UI

Access queue monitoring at: `http://localhost:3000/admin/queues`

## Environment Variables

See `.env.example` for required environment variables.

## Adding New Jobs

1. Define job data type in `src/types/jobs.types.ts`
2. Create queue in `src/queues/`
3. Create worker in `src/workers/`
4. Register in respective index files

## Monitoring

- **Bull Board:** `http://localhost:3000/admin/queues`
- **Health Check:** `http://localhost:3000/health`

## Queue Configuration

Each queue supports:
- Automatic retries (default: 3 attempts)
- Exponential backoff
- Job prioritization
- Rate limiting
- Scheduled/cron jobs
```

---

## ✅ Completion Checklist

- [ ] All directories created
- [ ] `package.json` configured with BullMQ dependencies
- [ ] `src/index.ts` entry point with Bull Board
- [ ] Redis configuration created
- [ ] Queue configuration created
- [ ] Example queues created (email, report)
- [ ] Example workers created
- [ ] Job type definitions created
- [ ] Business logic services created
- [ ] Logger utility configured
- [ ] TypeScript configuration complete
- [ ] `Dockerfile` created
- [ ] `.env.example` documented
- [ ] Docker Compose integration added
- [ ] `.devcontainer/[service-name]-container/devcontainer.json` created at project root
- [ ] `README.md` completed
- [ ] Service builds successfully
- [ ] Workers processing jobs
- [ ] Bull Board accessible
- [ ] Health check working

---

**Note:** Replace all `[service-name]` placeholders with your actual service name. Configure queues and workers based on your specific job processing requirements.
