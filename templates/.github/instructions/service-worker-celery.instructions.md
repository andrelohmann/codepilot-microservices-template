---
applyTo: "**/worker-celery-*/**"
description: Celery background worker with Python and Redis/RabbitMQ
---

# Worker Celery - Python Background Jobs

Process asynchronous tasks with Celery distributed task queue.

**Reference**: [Complete Celery Tech Stack Documentation](../../../docs/tech-stacks/workers/celery.md)  
**Scaffold**: Use the `scaffold-worker-celery-service.prompt.md` for new services

## Naming Convention

`worker-celery-{purpose}` (e.g., `worker-celery-etl`, `worker-celery-analytics`)

## Project Structure

```
services/worker-celery-{purpose}/
├── app/
│   ├── celery_app.py       # Celery instance
│   ├── config.py           # Configuration
│   ├── tasks/              # Task definitions
│   └── processors/         # Business logic
├── celeryconfig.py         # Celery config
└── requirements.txt
```

## Quick Reference Patterns

### 1. Celery Setup

```python
# app/celery_app.py
from celery import Celery

app = Celery(
    'worker',
    broker=f'redis://{REDIS_HOST}:{REDIS_PORT}/0',
    backend=f'redis://{REDIS_HOST}:{REDIS_PORT}/1',
    include=['app.tasks.email']
)
app.config_from_object('celeryconfig')
```

### 2. Task with Retry

```python
# app/tasks/email.py
from app.celery_app import app
from pydantic import BaseModel, EmailStr

class EmailTask(BaseModel):
    to: EmailStr
    subject: str
    body: str

@app.task(
    bind=True,
    max_retries=3,
    default_retry_delay=60,
    autoretry_for=(Exception,)
)
def send_email(self, to: str, subject: str, body: str):
    try:
        data = EmailTask(to=to, subject=subject, body=body)
        # Send email logic
        return {'sent': True, 'to': data.to}
    except Exception as exc:
        raise self.retry(exc=exc)
```

### 3. Periodic Tasks

```python
# celeryconfig.py
from celery.schedules import crontab

beat_schedule = {
    'daily-report': {
        'task': 'app.tasks.reports.generate_daily_report',
        'schedule': crontab(hour=9, minute=0),
    },
}
```

### 4. Task Chaining

```python
from celery import chain

workflow = chain(
    task1.s(arg1),
    task2.s(),
    task3.s()
)
workflow.apply_async()
```

## Code Style

- Use Pydantic for task parameter validation
- Configure retry strategies (max_retries, delay)
- Organize tasks by domain in separate modules
- Use task queues for priority management
- Always configure timeouts for long tasks

## Anti-Patterns

- ❌ Long-running tasks without timeout
- ❌ No retry configuration
- ❌ Storing large objects in task args
- ❌ Not using task queues for organization
- ❌ Missing error logging and monitoring
