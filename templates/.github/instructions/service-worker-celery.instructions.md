---
applyTo: "**/worker-celery-*/**"
description: Celery background worker with Python and Redis/RabbitMQ
---

# Worker Celery - Python Background Jobs

Process asynchronous tasks with Celery distributed task queue.

## Naming Convention

`worker-celery-{purpose}` (e.g., `worker-celery-etl`, `worker-celery-analytics`)

## Technology Stack

- Python 3.11+ (language)
- Celery 5+ (task queue)
- Redis or RabbitMQ (message broker)
- Flower (monitoring)
- Pydantic (validation)
- pytest (testing)

## Directory Structure

```
services/worker-celery-{purpose}/
├── app/
│   ├── __init__.py
│   ├── celery_app.py
│   ├── config.py
│   ├── tasks/
│   │   ├── __init__.py
│   │   ├── email.py
│   │   └── reports.py
│   ├── processors/
│   │   ├── __init__.py
│   │   ├── email_processor.py
│   │   └── report_processor.py
│   └── utils/
│       └── logger.py
├── tests/
│   └── test_tasks.py
├── Dockerfile
├── requirements.txt
└── celeryconfig.py
```

## Celery Setup

```python
# app/celery_app.py
from celery import Celery

app = Celery(
    'worker',
    broker=f'redis://{REDIS_HOST}:{REDIS_PORT}/0',
    backend=f'redis://{REDIS_HOST}:{REDIS_PORT}/1',
    include=['app.tasks.email', 'app.tasks.reports']
)

app.config_from_object('celeryconfig')
```

## Configuration

```python
# celeryconfig.py
broker_connection_retry_on_startup = True
task_serializer = 'json'
result_serializer = 'json'
accept_content = ['json']
timezone = 'UTC'
enable_utc = True

task_routes = {
    'app.tasks.email.*': {'queue': 'email'},
    'app.tasks.reports.*': {'queue': 'reports'},
}

task_annotations = {
    '*': {'rate_limit': '100/m'}
}
```

## Task Definition

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

## Periodic Tasks

```python
# celeryconfig.py
from celery.schedules import crontab

beat_schedule = {
    'daily-report': {
        'task': 'app.tasks.reports.generate_daily_report',
        'schedule': crontab(hour=9, minute=0),
    },
    'cleanup-old-data': {
        'task': 'app.tasks.maintenance.cleanup',
        'schedule': crontab(hour=2, minute=0, day_of_week=0),
    },
}
```

## Task Execution

```python
# Asynchronous
result = send_email.delay('user@example.com', 'Subject', 'Body')

# With ETA
from datetime import datetime, timedelta
eta = datetime.utcnow() + timedelta(hours=1)
send_email.apply_async(args=[...], eta=eta)

# Task chaining
from celery import chain
workflow = chain(
    task1.s(arg1),
    task2.s(),
    task3.s()
)
workflow.apply_async()
```

## Monitoring with Flower

```python
# Start Flower
# celery -A app.celery_app flower --port=5555

# Or in code
from flower.command import FlowerCommand
FlowerCommand().execute_from_commandline(['flower'])
```

## Error Handling

```python
@app.task(bind=True)
def process_data(self, data_id: int):
    try:
        process(data_id)
    except SoftTimeLimitExceeded:
        logger.error(f'Task timeout for data {data_id}')
        cleanup(data_id)
    except Exception as exc:
        logger.error(f'Task failed: {exc}')
        send_alert(f'Task failed for {data_id}')
        raise self.retry(exc=exc, countdown=60)
```

## Testing

```python
# tests/test_tasks.py
from app.tasks.email import send_email

def test_send_email():
    result = send_email.apply(args=['test@example.com', 'Test', 'Body'])
    assert result.successful()
    assert result.result['sent'] is True
```

## Running Workers

```bash
# Single worker
celery -A app.celery_app worker --loglevel=info

# Multiple queues
celery -A app.celery_app worker -Q email,reports --concurrency=4

# Beat scheduler
celery -A app.celery_app beat --loglevel=info
```

## Health Checks

```python
from celery import Celery

def health_check():
    inspector = app.control.inspect()
    stats = inspector.stats()
    active = inspector.active()
    
    return {
        'workers': len(stats) if stats else 0,
        'active_tasks': sum(len(tasks) for tasks in active.values()) if active else 0
    }
```

## Anti-Patterns

- ❌ Long-running tasks without timeout
- ❌ No retry configuration
- ❌ Storing large objects in task args
- ❌ Not using task queues for organization
- ❌ Missing error logging and monitoring
