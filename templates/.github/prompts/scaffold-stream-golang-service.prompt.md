---
description: "Scaffold a Golang stream processing service"
mode: agent
tools: [file, workspace, terminal]
---

# Scaffold Golang Stream Processing Service

Create a production-ready `${input:serviceName}` stream processor using Go with Kafka/NATS for real-time data processing.

## Reference Documentation

- **[Golang Stream Tech-Stack Guide](../../../../docs/tech-stacks/streams/golang.md)** - Complete implementation details
- **[Golang Stream Instructions](../instructions/service-stream-golang.instructions.md)** - Copilot guidance

## Service Configuration

Ask the user for:
- **Service Name**: `${input:serviceName}` (e.g., `stream-golang-events`, `stream-golang-analytics`)
- **Purpose**: `${input:servicePurpose}`
- **Stream**: `${input:stream:Kafka}` (Kafka, NATS, Redis Streams)
- **Workers**: `${input:workers:4}`

## Project Structure

```
services/${input:serviceName}/
├── cmd/processor/main.go
├── internal/
│   ├── config/
│   ├── consumers/
│   ├── processors/
│   ├── producers/
│   └── state/
└── go.mod
```

## Implementation Steps

1. Initialize Go module
2. Create main.go with consumer setup
3. Implement message processor
4. Configure worker pool
5. Set up state management (Redis)
6. Implement windowing (if needed)
7. Add graceful shutdown
8. Write tests
9. Create Dockerfile
10. Update docker-compose.yml

Refer to [tech-stack guide](../../../../docs/tech-stacks/streams/golang.md) for detailed templates.

## Completion Checklist

- [ ] Consumer configured
- [ ] Processor implemented
- [ ] Worker pool working
- [ ] State management set up
- [ ] Graceful shutdown working
- [ ] Tests passing
- [ ] Docker builds successfully

---

**Note**: Refer to [complete documentation](../../../../docs/tech-stacks/streams/golang.md) for implementation details.
