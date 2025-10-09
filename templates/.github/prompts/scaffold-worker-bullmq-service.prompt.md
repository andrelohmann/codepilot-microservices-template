---
description: "Scaffold a BullMQ worker service"
mode: agent
tools: [file, workspace, terminal]
---

# Scaffold BullMQ Worker Service

Create a production-ready `${input:serviceName}` worker service using BullMQ with TypeScript for processing background jobs with Redis.

## Reference Documentation

- **[BullMQ Tech-Stack Guide](../../../../docs/tech-stacks/workers/bullmq.md)** - Complete implementation details and examples
- **[BullMQ Instructions](../instructions/service-worker-bullmq.instructions.md)** - Copilot coding guidance

## Service Configuration

Ask the user for:
- **Service Name**: `${input:serviceName}` (e.g., `worker-bullmq-email`, `worker-bullmq-reports`)
- **Purpose**: `${input:servicePurpose}` (brief description)
- **Queue Name**: `${input:queueName:default}` (default: default)
- **Concurrency**: `${input:concurrency:5}` (default: 5)
- **Redis URL**: From environment (default: redis://redis:6379)

## Project Structure

```
services/${input:serviceName}/
├── src/
│   ├── index.ts             # Worker entry point
│   ├── config.ts            # Configuration
│   ├── queues/              # Queue definitions
│   │   └── ${input:queueName}.queue.ts
│   ├── workers/             # Worker implementations
│   │   └── ${input:queueName}.worker.ts
│   ├── jobs/                # Job definitions
│   │   └── *.job.ts
│   └── processors/          # Business logic
│       └── *.processor.ts
├── tests/
├── Dockerfile
├── package.json
├── tsconfig.json
└── README.md
```

## Implementation Steps

1. **Initialize TypeScript Project**
   - Create directory in `services/`
   - Initialize `package.json` and `tsconfig.json`
   - Install dependencies: `bullmq`, `ioredis`, `@types/node`

2. **Core Application Files**
   - `src/index.ts` - Worker bootstrap with graceful shutdown
   - `src/config.ts` - Configuration from environment
   - Refer to [tech-stack guide](../../../../docs/tech-stacks/workers/bullmq.md#quick-start) for templates

3. **Queue Setup**
   - `src/queues/${input:queueName}.queue.ts` - Queue instance with Redis connection
   - Configure retry strategy and job options
   - Export queue for job producers

4. **Worker Implementation**
   - `src/workers/${input:queueName}.worker.ts` - Worker with processor
   - Set concurrency: `${input:concurrency}`
   - Event handlers: completed, failed, progress
   - Graceful shutdown handling

5. **Job Definitions**
   - `src/jobs/*.job.ts` - Typed job data interfaces
   - Job name constants
   - Job options (priority, retry, delay)

6. **Processors (Business Logic)**
   - `src/processors/*.processor.ts` - Actual job processing logic
   - Separate concerns: worker handles orchestration, processor handles logic
   - Proper error handling and logging

7. **Scheduled Jobs**
   - Use BullMQ repeatable jobs
   - Configure cron expressions
   - Job scheduling in queue setup

8. **Monitoring Dashboard** (Optional)
   - Bull Board integration
   - Express server for UI
   - Dashboard route configuration

9. **Testing Setup**
   - `tests/*.test.ts` - Job processing tests
   - Mock Redis with `ioredis-mock`
   - Test job completion and failures

10. **Docker Configuration**
    - `Dockerfile` - Node.js with TypeScript build
    - Health check (worker status endpoint)
    - `.dockerignore`

11. **Dependencies**
    - `package.json` - BullMQ, ioredis, TypeScript, testing libraries
    - Lock file for reproducibility

12. **Docker Compose Integration**
    - Update `docker-compose.yml` in project root
    - Add worker service with Redis dependency
    - Environment variables for Redis connection

13. **Development Configuration**
    - `.devcontainer/` - VS Code devcontainer
    - `.env.example` - Environment template
    - Hot reload with `ts-node-dev` (optional)

14. **Verify Service**
    - Start service: `docker compose up ${input:serviceName}`
    - Check worker logs for "Worker started" message
    - Test by adding jobs to queue
    - Monitor with Bull Board (if configured)

## Code Templates

All code templates are available in the [BullMQ Tech-Stack Documentation](../../../../docs/tech-stacks/workers/bullmq.md):
- Queue setup with Redis
- Worker with concurrency
- Job definitions with retry
- Scheduled jobs
- Event handlers
- Bull Board dashboard
- Testing patterns

## Completion Checklist

- [ ] Directory structure created
- [ ] TypeScript configured
- [ ] Queue instance created
- [ ] Worker implemented with processor
- [ ] Job definitions typed
- [ ] Business logic in processors
- [ ] Event handlers configured
- [ ] Scheduled jobs set up (if required)
- [ ] Tests written and passing
- [ ] Dockerfile builds successfully
- [ ] docker-compose.yml updated
- [ ] README.md documentation complete
- [ ] Worker starts without errors
- [ ] Jobs process successfully

## Post-Scaffolding

After scaffolding completes:
1. Start worker: `docker compose up ${input:serviceName}`
2. Test job processing: Add jobs to queue and verify completion
3. Monitor with Bull Board (if enabled)
4. Refer to [instructions file](../instructions/service-worker-bullmq.instructions.md) for Copilot coding guidance

---

**Note**: This prompt creates the scaffolding only. Refer to the [complete tech-stack documentation](../../../../docs/tech-stacks/workers/bullmq.md) for detailed implementation patterns, best practices, and code examples.
