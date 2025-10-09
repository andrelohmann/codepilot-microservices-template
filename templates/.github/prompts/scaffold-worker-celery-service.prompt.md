---
description: "Scaffold a Celery worker service"
mode: agent
tools: [file, workspace, terminal]
---

# Scaffold Celery Worker Service

Create a production-ready `${input:serviceName}` worker service using Celery with Python for processing background jobs with Redis/RabbitMQ.

## Reference Documentation

- **[Celery Tech-Stack Guide](../../../../docs/tech-stacks/workers/celery.md)** - Complete implementation details
- **[Celery Instructions](../instructions/service-worker-celery.instructions.md)** - Copilot guidance

## Service Configuration

Ask the user for:
- **Service Name**: `${input:serviceName}` (e.g., `worker-celery-etl`, `worker-celery-analytics`)
- **Purpose**: `${input:servicePurpose}`
- **Broker**: `${input:broker:Redis}` (Redis or RabbitMQ)
- **Concurrency**: `${input:concurrency:4}`

## Project Structure

```
services/${input:serviceName}/
├── app/
│   ├── celery_app.py        # Celery instance
│   ├── config.py            # Configuration
│   ├── tasks/               # Task definitions
│   └── processors/          # Business logic
├── celeryconfig.py          # Celery configuration
├── requirements.txt
├── Dockerfile
└── README.md
```

## Implementation Steps

1. Initialize Python project with Celery, Redis/RabbitMQ, Pydantic
2. Create `app/celery_app.py` with broker configuration
3. Implement `celeryconfig.py` with retry, queues, schedules
4. Define tasks in `app/tasks/` with `@app.task` decorator
5. Implement business logic in `app/processors/`
6. Configure periodic tasks with `beat_schedule`
7. Set up Flower monitoring (optional)
8. Write tests with mocked Celery
9. Create Dockerfile
10. Update docker-compose.yml

Refer to [tech-stack guide](../../../../docs/tech-stacks/workers/celery.md) for detailed templates.

## Completion Checklist

- [ ] Celery app configured
- [ ] Tasks defined with retry logic
- [ ] Periodic tasks scheduled
- [ ] Tests passing
- [ ] Docker builds successfully
- [ ] Worker starts without errors

---

**Note**: Refer to [complete documentation](../../../../docs/tech-stacks/workers/celery.md) for implementation details.
