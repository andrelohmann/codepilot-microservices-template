---
description: "Scaffold a Golang worker service"
mode: agent
tools: [file, workspace, terminal]
---

# Scaffold Golang Worker Service

Create a production-ready `${input:serviceName}` worker service using Go with goroutines and channels for background job processing.

## Reference Documentation

- **[Golang Worker Tech-Stack Guide](../../../../docs/tech-stacks/workers/golang.md)** - Complete implementation details
- **[Golang Worker Instructions](../instructions/service-worker-golang.instructions.md)** - Copilot guidance

## Service Configuration

Ask the user for:
- **Service Name**: `${input:serviceName}` (e.g., `worker-golang-processor`, `worker-golang-sync`)
- **Purpose**: `${input:servicePurpose}`
- **Worker Count**: `${input:workers:4}` (default: NumCPU)
- **Queue**: `${input:queue:Redis}` (Redis, In-memory, Kafka)

## Project Structure

```
services/${input:serviceName}/
├── cmd/worker/main.go
├── internal/
│   ├── config/
│   ├── jobs/
│   ├── handlers/
│   ├── queue/
│   └── services/
├── go.mod
├── Dockerfile
└── README.md
```

## Implementation Steps

1. Initialize Go module
2. Create `cmd/worker/main.go` with worker pool
3. Implement job interface and processor
4. Set up Redis/Kafka queue consumer
5. Configure graceful shutdown with context
6. Add panic recovery in workers
7. Implement exponential backoff retries
8. Write tests with mocked queue
9. Create Dockerfile
10. Update docker-compose.yml

Refer to [tech-stack guide](../../../../docs/tech-stacks/workers/golang.md) for detailed templates.

## Completion Checklist

- [ ] Worker pool implemented
- [ ] Job processing with retry
- [ ] Graceful shutdown working
- [ ] Tests passing
- [ ] Docker builds successfully
- [ ] Worker starts without errors

---

**Note**: Refer to [complete documentation](../../../../docs/tech-stacks/workers/golang.md) for implementation details.
