# Scaffold Celery Worker Service

**Objective:** Create a production-ready Celery worker service with complete directory structure, Docker configuration, monitoring, and comprehensive documentation.

---

## 📋 Service Specification

Before running this prompt, gather the following information:

1. **Service Name:** `[e.g., worker-celery-etl, worker-celery-analytics]`
2. **Service Purpose:** `[Brief description of what tasks this worker processes]`
3. **Task Types:** `[List of task types to implement]`
4. **Redis/RabbitMQ:** `[Redis or RabbitMQ as broker]`
5. **Result Backend:** `[Redis, Database, None]`
6. **Scheduling Required:** `[Yes/No - for periodic tasks via Celery Beat]`
7. **External APIs:** `[List any external services to integrate]`
8. **Monitoring:** `[Flower dashboard - Yes/No]`

---

## 🏗️ Architecture Overview

```
┌────────────────────────────────────────────────────────────────────┐
│                   Celery Worker Architecture                       │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                    Management Layer                          │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │   Celery   │  │   Flower   │  │   Celery   │            │ │
│  │  │    App     │  │(Monitoring)│  │    Beat    │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
│            ↓                ↓                ↓                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                      Worker Layer                            │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │   Task     │  │   Task     │  │   Error    │            │ │
│  │  │ Processors │  │ Validators │  │  Handlers  │            │ │
│  │  └──────┬─────┘  └────────────┘  └────────────┘            │ │
│  └─────────┼────────────────────────────────────────────────────┘ │
│            │                                                      │
│            ↓                                                      │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                    Service Layer                             │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │  Business  │  │  External  │  │  Database  │            │ │
│  │  │   Logic    │  │API Clients │  │   Access   │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
└────────────┼────────────────┼────────────────┼────────────────────┘
             │                │                │
             ↓                ↓                ↓
    ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
    │    Redis    │  │External APIs│  │  Database   │
    │   /RMQ      │  │             │  │  (Optional) │
    │  (Broker)   │  │             │  │             │
    └─────────────┘  └─────────────┘  └─────────────┘
```

---

## 📁 Directory Structure

Create the following complete directory structure:

```
services/[service-name]/
├── app/
│   ├── __init__.py
│   ├── celery_app.py            ← Celery application instance
│   ├── config.py                ← Configuration settings
│   │
│   ├── tasks/                   ← Task definitions
│   │   ├── __init__.py
│   │   ├── email_tasks.py       ← Example: Email tasks
│   │   ├── report_tasks.py      ← Example: Report tasks
│   │   └── etl_tasks.py         ← Example: ETL tasks
│   │
│   ├── services/                ← Business logic
│   │   ├── __init__.py
│   │   ├── email_service.py
│   │   ├── report_service.py
│   │   └── data_service.py
│   │
│   ├── utils/                   ← Utility functions
│   │   ├── __init__.py
│   │   ├── logger.py            ← Logging configuration
│   │   ├── retry.py             ← Retry decorators
│   │   └── metrics.py           ← Metrics collection
│   │
│   └── schemas/                 ← Pydantic schemas
│       ├── __init__.py
│       └── task_schemas.py      ← Task input/output schemas
│
├── tests/                       ← Tests
│   ├── __init__.py
│   ├── conftest.py              ← Pytest fixtures
│   ├── unit/
│   │   ├── test_tasks.py
│   │   └── test_services.py
│   └── integration/
│       └── test_celery.py
│
├── scripts/                     ← Utility scripts
│   ├── start_worker.sh          ← Start Celery worker
│   ├── start_beat.sh            ← Start Celery beat
│   └── start_flower.sh          ← Start Flower monitoring
│
├── requirements.txt             ← Production dependencies
├── requirements-dev.txt         ← Development dependencies
├── Dockerfile                   ← Production Docker image
├── .dockerignore                ← Docker ignore patterns
├── pytest.ini                   ← Pytest configuration
├── .flake8                      ← Flake8 linting config
├── README.md                    ← Service documentation
└── .env.example                 ← Environment variables template
```

---

## 📦 Step 1: `requirements.txt` - Production Dependencies

```txt
celery[redis]==5.3.4
redis==5.0.1
flower==2.0.1
pydantic==2.5.2
pydantic-settings==2.1.0
python-dotenv==1.0.0
requests==2.31.0
psycopg2-binary==2.9.9
sqlalchemy==2.0.23
```

---

## 📦 Step 2: `requirements-dev.txt` - Development Dependencies

```txt
-r requirements.txt
pytest==7.4.3
pytest-celery==0.0.0
pytest-cov==4.1.0
pytest-mock==3.12.0
black==23.12.1
flake8==6.1.0
mypy==1.7.1
```

---

## 🔧 Step 3: `app/celery_app.py` - Celery Application

```python
"""Celery application instance and configuration."""
from celery import Celery
from celery.schedules import crontab
from app.config import settings

# Create Celery app
celery_app = Celery(
    "worker",
    broker=settings.CELERY_BROKER_URL,
    backend=settings.CELERY_RESULT_BACKEND,
    include=[
        "app.tasks.email_tasks",
        "app.tasks.report_tasks",
        "app.tasks.etl_tasks",
    ],
)

# Celery configuration
celery_app.conf.update(
    task_serializer="json",
    accept_content=["json"],
    result_serializer="json",
    timezone=settings.TIMEZONE,
    enable_utc=True,
    task_track_started=True,
    task_time_limit=settings.TASK_TIME_LIMIT,
    task_soft_time_limit=settings.TASK_SOFT_TIME_LIMIT,
    worker_prefetch_multiplier=settings.WORKER_PREFETCH_MULTIPLIER,
    worker_max_tasks_per_child=settings.WORKER_MAX_TASKS_PER_CHILD,
    task_acks_late=True,
    task_reject_on_worker_lost=True,
    task_default_retry_delay=60,  # 60 seconds
    task_max_retries=3,
    broker_connection_retry_on_startup=True,
)

# Celery Beat schedule for periodic tasks
celery_app.conf.beat_schedule = {
    "daily-report": {
        "task": "app.tasks.report_tasks.generate_daily_report",
        "schedule": crontab(hour=8, minute=0),  # Every day at 8 AM
    },
    "cleanup-old-data": {
        "task": "app.tasks.etl_tasks.cleanup_old_data",
        "schedule": crontab(hour=2, minute=0),  # Every day at 2 AM
    },
    "health-check": {
        "task": "app.tasks.email_tasks.send_health_check",
        "schedule": 300.0,  # Every 5 minutes
    },
}

# Task routes (optional - for multiple queues)
celery_app.conf.task_routes = {
    "app.tasks.email_tasks.*": {"queue": "email"},
    "app.tasks.report_tasks.*": {"queue": "reports"},
    "app.tasks.etl_tasks.*": {"queue": "etl"},
}

# Error handling
@celery_app.task(bind=True)
def debug_task(self):
    """Debug task for testing Celery configuration."""
    print(f"Request: {self.request!r}")
```

---

## 🔧 Step 4: `app/config.py` - Configuration Settings

```python
"""Application configuration using Pydantic settings."""
from pydantic_settings import BaseSettings, SettingsConfigDict
from typing import Optional


class Settings(BaseSettings):
    """Application settings."""

    model_config = SettingsConfigDict(
        env_file=".env",
        env_file_encoding="utf-8",
        case_sensitive=True,
    )

    # Application
    APP_NAME: str = "celery-worker"
    ENVIRONMENT: str = "production"
    DEBUG: bool = False
    TIMEZONE: str = "UTC"

    # Celery
    CELERY_BROKER_URL: str = "redis://redis:6379/0"
    CELERY_RESULT_BACKEND: str = "redis://redis:6379/0"
    
    # Worker settings
    WORKER_CONCURRENCY: int = 4
    WORKER_PREFETCH_MULTIPLIER: int = 4
    WORKER_MAX_TASKS_PER_CHILD: int = 1000
    
    # Task settings
    TASK_TIME_LIMIT: int = 3600  # 1 hour
    TASK_SOFT_TIME_LIMIT: int = 3300  # 55 minutes
    
    # Flower monitoring
    FLOWER_PORT: int = 5555
    FLOWER_BASIC_AUTH: Optional[str] = None  # Format: "user:password"
    
    # Database (if needed)
    DATABASE_URL: Optional[str] = None
    
    # External services
    SMTP_HOST: Optional[str] = None
    SMTP_PORT: int = 587
    SMTP_USER: Optional[str] = None
    SMTP_PASSWORD: Optional[str] = None
    
    # Logging
    LOG_LEVEL: str = "INFO"


settings = Settings()
```

---

## 🔧 Step 5: `app/tasks/email_tasks.py` - Example Email Tasks

```python
"""Email processing tasks."""
import logging
from celery import Task
from app.celery_app import celery_app
from app.services.email_service import email_service
from app.schemas.task_schemas import EmailTaskInput
from app.utils.retry import exponential_backoff

logger = logging.getLogger(__name__)


class CallbackTask(Task):
    """Base task with callbacks for lifecycle events."""

    def on_success(self, retval, task_id, args, kwargs):
        """Called when task succeeds."""
        logger.info(f"Task {task_id} succeeded with result: {retval}")

    def on_failure(self, exc, task_id, args, kwargs, einfo):
        """Called when task fails."""
        logger.error(f"Task {task_id} failed with error: {exc}")

    def on_retry(self, exc, task_id, args, kwargs, einfo):
        """Called when task is retried."""
        logger.warning(f"Task {task_id} retrying due to: {exc}")


@celery_app.task(
    name="app.tasks.email_tasks.send_email",
    base=CallbackTask,
    bind=True,
    max_retries=3,
    default_retry_delay=60,
)
def send_email(self, email_data: dict):
    """
    Send a single email.
    
    Args:
        email_data: Dictionary containing email details (to, subject, body)
    
    Returns:
        dict: Result with success status
    """
    try:
        # Validate input
        validated_data = EmailTaskInput(**email_data)
        
        logger.info(f"Sending email to {validated_data.to}")
        
        # Send email
        result = email_service.send_email(
            to=validated_data.to,
            subject=validated_data.subject,
            body=validated_data.body,
            html=validated_data.html,
        )
        
        return {
            "success": True,
            "message_id": result.get("message_id"),
            "recipient": validated_data.to,
        }
    
    except Exception as exc:
        logger.error(f"Error sending email: {exc}")
        # Retry with exponential backoff
        raise self.retry(exc=exc, countdown=exponential_backoff(self.request.retries))


@celery_app.task(
    name="app.tasks.email_tasks.send_bulk_email",
    bind=True,
)
def send_bulk_email(self, recipients: list[str], subject: str, body: str):
    """
    Send bulk emails using Celery groups.
    
    Args:
        recipients: List of email addresses
        subject: Email subject
        body: Email body
    
    Returns:
        dict: Result with count of sent emails
    """
    from celery import group
    
    logger.info(f"Sending bulk email to {len(recipients)} recipients")
    
    # Create a group of email tasks
    job = group(
        send_email.s({"to": email, "subject": subject, "body": body})
        for email in recipients
    )
    
    result = job.apply_async()
    
    return {
        "success": True,
        "total_emails": len(recipients),
        "group_id": result.id,
    }


@celery_app.task(name="app.tasks.email_tasks.send_health_check")
def send_health_check():
    """Periodic task to verify worker is functioning."""
    logger.info("Health check task executed")
    return {"status": "healthy", "timestamp": str(celery_app.now())}
```

---

## 🔧 Step 6: `app/tasks/report_tasks.py` - Example Report Tasks

```python
"""Report generation tasks."""
import logging
from datetime import datetime
from celery import chain
from app.celery_app import celery_app
from app.services.report_service import report_service

logger = logging.getLogger(__name__)


@celery_app.task(name="app.tasks.report_tasks.generate_daily_report")
def generate_daily_report():
    """Generate and email daily report (scheduled task)."""
    logger.info("Generating daily report")
    
    report_data = report_service.generate_report(
        report_type="daily",
        date=datetime.now(),
    )
    
    # Chain tasks: generate report -> send email
    from app.tasks.email_tasks import send_email
    
    chain(
        generate_daily_report.s(),
        send_email.s({
            "to": "admin@example.com",
            "subject": f"Daily Report - {datetime.now().date()}",
            "body": report_data,
        })
    ).apply_async()
    
    return {"success": True, "report_type": "daily"}


@celery_app.task(
    name="app.tasks.report_tasks.generate_custom_report",
    bind=True,
    time_limit=1800,  # 30 minutes
)
def generate_custom_report(self, report_type: str, parameters: dict):
    """
    Generate custom report based on parameters.
    
    Args:
        report_type: Type of report to generate
        parameters: Report parameters
    
    Returns:
        dict: Report generation result
    """
    logger.info(f"Generating {report_type} report with parameters: {parameters}")
    
    try:
        # Update task state
        self.update_state(
            state="PROGRESS",
            meta={"current": 0, "total": 100, "status": "Starting..."}
        )
        
        result = report_service.generate_report(
            report_type=report_type,
            parameters=parameters,
            progress_callback=lambda current, total, status: self.update_state(
                state="PROGRESS",
                meta={"current": current, "total": total, "status": status}
            )
        )
        
        return {
            "success": True,
            "report_url": result.get("url"),
            "file_size": result.get("size"),
        }
    
    except Exception as exc:
        logger.error(f"Error generating report: {exc}")
        raise
```

---

## 🔧 Step 7: `app/services/email_service.py` - Email Service

```python
"""Email service for sending emails."""
import logging
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from app.config import settings

logger = logging.getLogger(__name__)


class EmailService:
    """Service for sending emails."""

    def send_email(
        self,
        to: str,
        subject: str,
        body: str,
        html: str = None,
    ) -> dict:
        """
        Send an email.
        
        Args:
            to: Recipient email address
            subject: Email subject
            body: Plain text body
            html: HTML body (optional)
        
        Returns:
            dict: Result with message_id
        """
        logger.info(f"Sending email to {to}")
        
        # Create message
        msg = MIMEMultipart("alternative")
        msg["Subject"] = subject
        msg["From"] = settings.SMTP_USER
        msg["To"] = to
        
        # Add plain text
        msg.attach(MIMEText(body, "plain"))
        
        # Add HTML if provided
        if html:
            msg.attach(MIMEText(html, "html"))
        
        # Send email
        try:
            with smtplib.SMTP(settings.SMTP_HOST, settings.SMTP_PORT) as server:
                server.starttls()
                server.login(settings.SMTP_USER, settings.SMTP_PASSWORD)
                server.send_message(msg)
            
            logger.info(f"Email sent successfully to {to}")
            return {"success": True, "message_id": msg["Message-ID"]}
        
        except Exception as e:
            logger.error(f"Failed to send email: {e}")
            raise


email_service = EmailService()
```

---

## 🔧 Step 8: `app/utils/logger.py` - Logging Configuration

```python
"""Logging configuration."""
import logging
import sys
from app.config import settings

def setup_logging():
    """Configure logging for the application."""
    logging.basicConfig(
        level=getattr(logging, settings.LOG_LEVEL),
        format="%(asctime)s - %(name)s - %(levelname)s - %(message)s",
        handlers=[
            logging.StreamHandler(sys.stdout),
        ],
    )

setup_logging()
```

---

## 🔧 Step 9: `app/utils/retry.py` - Retry Utilities

```python
"""Retry utility functions."""

def exponential_backoff(retry_count: int, base_delay: int = 60) -> int:
    """
    Calculate exponential backoff delay.
    
    Args:
        retry_count: Current retry attempt
        base_delay: Base delay in seconds
    
    Returns:
        int: Delay in seconds
    """
    return base_delay * (2 ** retry_count)
```

---

## 🔧 Step 10: `app/schemas/task_schemas.py` - Task Schemas

```python
"""Pydantic schemas for task inputs."""
from pydantic import BaseModel, EmailStr
from typing import Optional


class EmailTaskInput(BaseModel):
    """Schema for email task input."""
    to: EmailStr
    subject: str
    body: str
    html: Optional[str] = None


class ReportTaskInput(BaseModel):
    """Schema for report task input."""
    report_type: str
    parameters: dict
    format: str = "pdf"
```

---

## 🔧 Step 11: `scripts/start_worker.sh` - Start Worker Script

```bash
#!/bin/bash
set -e

echo "Starting Celery worker..."

celery -A app.celery_app worker \
    --loglevel=INFO \
    --concurrency=${WORKER_CONCURRENCY:-4} \
    --max-tasks-per-child=${WORKER_MAX_TASKS_PER_CHILD:-1000} \
    --queues=celery,email,reports,etl
```

---

## 🔧 Step 12: `scripts/start_beat.sh` - Start Beat Script

```bash
#!/bin/bash
set -e

echo "Starting Celery Beat..."

celery -A app.celery_app beat \
    --loglevel=INFO \
    --pidfile=/tmp/celerybeat.pid
```

---

## 🔧 Step 13: `scripts/start_flower.sh` - Start Flower Script

```bash
#!/bin/bash
set -e

echo "Starting Flower monitoring..."

celery -A app.celery_app flower \
    --port=${FLOWER_PORT:-5555} \
    --basic_auth=${FLOWER_BASIC_AUTH}
```

---

## 🐳 Step 14: `Dockerfile` - Production Docker Image

```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Install system dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        gcc \
        postgresql-client && \
    rm -rf /var/lib/apt/lists/*

# Copy requirements
COPY requirements.txt .

# Install Python dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Make scripts executable
RUN chmod +x scripts/*.sh

# Create non-root user
RUN useradd -m -u 1001 celery && \
    chown -R celery:celery /app

USER celery

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
  CMD celery -A app.celery_app inspect ping || exit 1

# Default command (worker)
CMD ["./scripts/start_worker.sh"]
```

---

## 🔧 Step 15: `.env.example` - Environment Variables Template

```bash
# Application
APP_NAME=celery-worker
ENVIRONMENT=production
DEBUG=False
TIMEZONE=UTC

# Celery
CELERY_BROKER_URL=redis://redis:6379/0
CELERY_RESULT_BACKEND=redis://redis:6379/0

# Worker settings
WORKER_CONCURRENCY=4
WORKER_PREFETCH_MULTIPLIER=4
WORKER_MAX_TASKS_PER_CHILD=1000

# Task settings
TASK_TIME_LIMIT=3600
TASK_SOFT_TIME_LIMIT=3300

# Flower
FLOWER_PORT=5555
FLOWER_BASIC_AUTH=admin:password

# SMTP
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASSWORD=your-password

# Logging
LOG_LEVEL=INFO
```

---

## 🐳 Step 16: Docker Compose Integration

Add to `docker-compose.yml`:

```yaml
  [service-name]:
    build:
      context: ./services/[service-name]
      dockerfile: Dockerfile
    container_name: [service-name]
    restart: unless-stopped
    command: ./scripts/start_worker.sh
    environment:
      - CELERY_BROKER_URL=redis://redis:6379/0
      - CELERY_RESULT_BACKEND=redis://redis:6379/0
    env_file:
      - ./config/.env
    depends_on:
      redis:
        condition: service_healthy
    networks:
      - microservices
    healthcheck:
      test: ["CMD", "celery", "-A", "app.celery_app", "inspect", "ping"]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 10s

  [service-name]-beat:
    build:
      context: ./services/[service-name]
      dockerfile: Dockerfile
    container_name: [service-name]-beat
    restart: unless-stopped
    command: ./scripts/start_beat.sh
    env_file:
      - ./config/.env
    depends_on:
      - redis
      - [service-name]
    networks:
      - microservices

  [service-name]-flower:
    build:
      context: ./services/[service-name]
      dockerfile: Dockerfile
    container_name: [service-name]-flower
    restart: unless-stopped
    command: ./scripts/start_flower.sh
    ports:
      - "5555:5555"
    env_file:
      - ./config/.env
    depends_on:
      - redis
      - [service-name]
    networks:
      - microservices
```

---

## 🔧 Step 17: `.devcontainer/[service-name]-container/devcontainer.json`

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
        "ms-python.python",
        "ms-python.vscode-pylance",
        "ms-python.black-formatter",
        "ms-python.flake8"
      ],
      "settings": {
        "python.defaultInterpreterPath": "/usr/local/bin/python",
        "python.linting.enabled": true,
        "python.linting.flake8Enabled": true,
        "python.formatting.provider": "black",
        "editor.formatOnSave": true
      }
    }
  },
  "forwardPorts": [5555],
  "postCreateCommand": "pip install -r requirements-dev.txt",
  "remoteUser": "celery"
}
```

---

## 📝 Step 18: `README.md` - Service Documentation

```markdown
# [Service Name]

[Service Purpose]

## Technology Stack

- **Task Queue:** Celery 5
- **Language:** Python 3.11
- **Broker:** Redis/RabbitMQ
- **Monitoring:** Flower
- **Scheduler:** Celery Beat

## Features

- ✅ Distributed task processing
- ✅ Periodic task scheduling (Celery Beat)
- ✅ Task prioritization and routing
- ✅ Automatic retries with exponential backoff
- ✅ Flower monitoring dashboard
- ✅ Task result persistence
- ✅ Graceful shutdown
- ✅ Type-safe task definitions with Pydantic

## Getting Started

### Development

```bash
# Install dependencies
pip install -r requirements-dev.txt

# Start worker
celery -A app.celery_app worker --loglevel=INFO

# Start beat scheduler
celery -A app.celery_app beat --loglevel=INFO

# Start Flower monitoring
celery -A app.celery_app flower
```

### Flower Dashboard

Access task monitoring at: `http://localhost:5555`

## Environment Variables

See `.env.example` for required environment variables.

## Adding New Tasks

1. Create task in `app/tasks/`
2. Define schema in `app/schemas/task_schemas.py`
3. Implement business logic in `app/services/`
4. Add task to Celery app includes in `app/celery_app.py`

## Monitoring

- **Flower Dashboard:** `http://localhost:5555`
- **Task Inspection:** `celery -A app.celery_app inspect active`
- **Stats:** `celery -A app.celery_app inspect stats`

## Testing

```bash
# Run tests
pytest

# Run with coverage
pytest --cov=app --cov-report=html
```
```

---

## ✅ Completion Checklist

- [ ] All directories created
- [ ] `requirements.txt` configured
- [ ] `app/celery_app.py` with Celery instance and Beat schedule
- [ ] `app/config.py` with Pydantic settings
- [ ] Example tasks created (email, report, ETL)
- [ ] Services implemented
- [ ] Utility functions (logger, retry) created
- [ ] Pydantic schemas defined
- [ ] Start scripts created and executable
- [ ] `Dockerfile` created
- [ ] `.env.example` documented
- [ ] Docker Compose integration added (worker, beat, flower)
- [ ] `.devcontainer/[service-name]-container/devcontainer.json` created at project root
- [ ] `README.md` completed
- [ ] Tests configured
- [ ] Service builds successfully
- [ ] Worker processing tasks
- [ ] Beat scheduling works
- [ ] Flower dashboard accessible

---

**Note:** Replace all `[service-name]` placeholders with your actual service name. Configure tasks, queues, and schedules based on your specific requirements.
