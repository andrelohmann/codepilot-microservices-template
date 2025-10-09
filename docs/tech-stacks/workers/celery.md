# Celery Worker - Python Background Job Processing

Distributed task queue for Python applications with robust scheduling, monitoring, and workflow capabilities.

---

## Overview

**Naming Convention:** `worker-celery-{purpose}`

**Examples:**
- `worker-celery-etl` - ETL pipeline worker
- `worker-celery-analytics` - Analytics processing worker
- `worker-celery-ml` - ML model training worker

**Celery** is a mature, distributed task queue for Python applications, supporting multiple message brokers (Redis, RabbitMQ). It excels at ETL pipelines, data processing, ML workflows, and complex task orchestration.

---

## Technology Stack

```
┌─────────────────────────────────────────────┐
│ Runtime & Language                          │
│  - Python 3.11+                             │
│  - Type hints (mypy)                        │
│  - asyncio support                          │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Queue & Messaging                           │
│  - Celery 5+                                │
│  - Redis / RabbitMQ (message broker)        │
│  - celery[redis] or celery[amqp]           │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Scheduling & Orchestration                  │
│  - Celery Beat (cron-like scheduler)        │
│  - Canvas primitives (chain, group, chord)  │
│  - Task routing                             │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Monitoring & Management                     │
│  - Flower (web UI)                          │
│  - celery events (real-time monitoring)     │
│  - Prometheus integration                   │
└─────────────────────────────────────────────┘
┌─────────────────────────────────────────────┐
│ Development & Testing                       │
│  - pytest                                   │
│  - celery[test] (testing utilities)         │
│  - pytest-celery                            │
└─────────────────────────────────────────────┘
```

---

## Features

### Core Queue Features
- ✅ **Multiple brokers** - Redis, RabbitMQ, SQS, etc.
- ✅ **Task routing** - Route tasks to specific queues/workers
- ✅ **Scheduled tasks** - Cron-like scheduling with Celery Beat
- ✅ **Task chains** - Sequential task execution
- ✅ **Task groups** - Parallel task execution
- ✅ **Chords** - Group with callback
- ✅ **Task retries** - Automatic retry with exponential backoff
- ✅ **Result backends** - Store task results (Redis, database)
- ✅ **Priority queues** - Process important tasks first
- ✅ **Task expiration** - Discard old tasks

### Canvas Primitives (Workflows)
- ✅ **chain** - Execute tasks sequentially (A → B → C)
- ✅ **group** - Execute tasks in parallel ([A, B, C])
- ✅ **chord** - Group with callback (group([A, B]) → C)
- ✅ **map** - Apply task to list of arguments
- ✅ **starmap** - Like map, but with multiple arguments
- ✅ **chunks** - Split task into chunks

### Developer Experience
- ✅ **Flower UI** - Web-based monitoring dashboard
- ✅ **Task events** - Real-time task monitoring
- ✅ **Result tracking** - Query task status and results
- ✅ **Django integration** - First-class Django support
- ✅ **Flask integration** - Flask extension available
- ✅ **Rich ecosystem** - Many extensions and plugins

---

## Ideal Use Cases

### ✅ Perfect For:
1. **ETL Pipelines**
   - Data extraction from APIs/databases
   - Data transformation and cleaning
   - Loading into data warehouses

2. **Data Processing**
   - Batch data processing
   - Data aggregation and analytics
   - Report generation

3. **ML Workflows**
   - Model training (long-running)
   - Batch inference
   - Feature engineering
   - Hyperparameter tuning

4. **Scheduled Jobs**
   - Daily/hourly reports
   - Data synchronization
   - Backup and cleanup tasks
   - Recurring analytics

5. **Complex Workflows**
   - Multi-step data pipelines
   - Dependent task chains
   - Parallel processing with aggregation

6. **Python Ecosystem Integration**
   - pandas, NumPy, scikit-learn
   - TensorFlow, PyTorch
   - SQLAlchemy, Django ORM

### ❌ Not Ideal For:
- ❌ Real-time stream processing → Use stream processor
- ❌ Extremely high throughput (> 10K jobs/sec) → Use Go worker
- ❌ Node.js applications → Use BullMQ
- ❌ Simple async tasks → Use asyncio or threading

---

## Project Structure

```
worker-celery-{purpose}/
├── src/
│   ├── __init__.py
│   ├── celery_app.py         # Celery application setup
│   ├── config.py              # Configuration
│   ├── tasks/                 # Task definitions
│   │   ├── __init__.py
│   │   ├── etl_tasks.py
│   │   ├── ml_tasks.py
│   │   └── report_tasks.py
│   ├── workflows/             # Complex workflows
│   │   ├── data_pipeline.py
│   │   └── ml_pipeline.py
│   └── utils/
│       ├── logger.py
│       └── metrics.py
├── tests/
│   ├── test_tasks.py
│   └── test_workflows.py
├── requirements.txt
├── .env.example
├── .env
├── Dockerfile
└── README.md
```

---

## Code Examples

### Celery Application Setup

```python
# src/celery_app.py
from celery import Celery
from kombu import Exchange, Queue

app = Celery(
    'worker',
    broker='redis://localhost:6379/0',
    backend='redis://localhost:6379/1',
)

app.conf.update(
    task_serializer='json',
    accept_content=['json'],
    result_serializer='json',
    timezone='UTC',
    enable_utc=True,
    
    # Task routing
    task_routes={
        'src.tasks.etl_tasks.*': {'queue': 'etl'},
        'src.tasks.ml_tasks.*': {'queue': 'ml'},
    },
    
    # Task execution
    task_acks_late=True,  # Acknowledge after execution
    task_reject_on_worker_lost=True,
    worker_prefetch_multiplier=1,  # Disable prefetching
    
    # Result expiration
    result_expires=3600,  # 1 hour
    
    # Retry policy
    task_autoretry_for=(Exception,),
    task_retry_backoff=True,
    task_max_retries=3,
)

# Define queues
app.conf.task_queues = (
    Queue('default', Exchange('default'), routing_key='default'),
    Queue('etl', Exchange('etl'), routing_key='etl'),
    Queue('ml', Exchange('ml'), routing_key='ml', priority=0),
)
```

### Simple Task

```python
# src/tasks/etl_tasks.py
from src.celery_app import app
import pandas as pd

@app.task(bind=True, name='extract_data')
def extract_data(self, source_url: str):
    """Extract data from API"""
    try:
        df = pd.read_csv(source_url)
        self.update_state(state='PROGRESS', meta={'rows': len(df)})
        return df.to_dict('records')
    except Exception as exc:
        self.update_state(state='FAILURE', meta={'error': str(exc)})
        raise

@app.task(bind=True, name='transform_data')
def transform_data(self, data: list):
    """Transform data"""
    df = pd.DataFrame(data)
    
    # Data cleaning
    df = df.dropna()
    df['date'] = pd.to_datetime(df['date'])
    
    # Aggregation
    result = df.groupby('category').agg({
        'value': 'sum',
        'count': 'sum'
    }).reset_index()
    
    return result.to_dict('records')

@app.task(bind=True, name='load_data')
def load_data(self, data: list, destination: str):
    """Load data to destination"""
    df = pd.DataFrame(data)
    
    # Save to database or file
    df.to_sql(destination, con=engine, if_exists='append')
    
    return {'rows_loaded': len(df)}
```

### Task Chain (Sequential Execution)

```python
# src/workflows/data_pipeline.py
from celery import chain
from src.tasks.etl_tasks import extract_data, transform_data, load_data

def run_etl_pipeline(source_url: str, destination: str):
    """Execute ETL pipeline: Extract → Transform → Load"""
    pipeline = chain(
        extract_data.s(source_url),
        transform_data.s(),
        load_data.s(destination)
    )
    
    result = pipeline.apply_async()
    return result.id
```

### Task Group (Parallel Execution)

```python
from celery import group

@app.task
def process_chunk(chunk_id: int, data: list):
    """Process a chunk of data"""
    # Process chunk
    return {'chunk_id': chunk_id, 'processed': len(data)}

def process_in_parallel(data_chunks: list):
    """Process multiple chunks in parallel"""
    job = group(
        process_chunk.s(i, chunk) 
        for i, chunk in enumerate(data_chunks)
    )
    
    result = job.apply_async()
    results = result.get(timeout=300)  # Wait max 5 minutes
    return results
```

### Chord (Group + Callback)

```python
from celery import chord

@app.task
def fetch_user_data(user_id: int):
    """Fetch data for one user"""
    # API call or database query
    return {'user_id': user_id, 'data': '...'}

@app.task
def aggregate_results(results: list):
    """Aggregate results from all users"""
    total_users = len(results)
    # Aggregate logic
    return {'total_users': total_users, 'summary': '...'}

def process_all_users(user_ids: list):
    """Fetch all user data in parallel, then aggregate"""
    job = chord(
        (fetch_user_data.s(uid) for uid in user_ids),
        aggregate_results.s()
    )
    
    result = job.apply_async()
    return result.id
```

### Scheduled Tasks (Celery Beat)

```python
# src/celery_app.py (continued)
from celery.schedules import crontab

app.conf.beat_schedule = {
    'daily-report': {
        'task': 'src.tasks.report_tasks.generate_daily_report',
        'schedule': crontab(hour=9, minute=0),  # Every day at 9 AM
    },
    'hourly-sync': {
        'task': 'src.tasks.sync_tasks.sync_data',
        'schedule': crontab(minute=0),  # Every hour
    },
    'weekly-cleanup': {
        'task': 'src.tasks.cleanup_tasks.cleanup_old_data',
        'schedule': crontab(day_of_week=0, hour=2, minute=0),  # Sunday 2 AM
    },
}
```

### ML Training Task

```python
# src/tasks/ml_tasks.py
from src.celery_app import app
import joblib
from sklearn.ensemble import RandomForestClassifier

@app.task(bind=True, name='train_model')
def train_model(self, X_path: str, y_path: str, model_path: str):
    """Train ML model (long-running task)"""
    import pandas as pd
    
    # Load data
    X = pd.read_csv(X_path)
    y = pd.read_csv(y_path)
    
    # Train model
    model = RandomForestClassifier(n_estimators=100)
    
    for i in range(100):
        # Incremental training or cross-validation
        self.update_state(state='PROGRESS', meta={'progress': i + 1, 'total': 100})
        # Training step...
    
    model.fit(X, y)
    
    # Save model
    joblib.dump(model, model_path)
    
    return {'model_path': model_path, 'accuracy': 0.95}
```

---

## Testing

### Unit Test

```python
# tests/test_tasks.py
from src.tasks.etl_tasks import transform_data

def test_transform_data():
    input_data = [
        {'category': 'A', 'value': 10, 'count': 1},
        {'category': 'A', 'value': 20, 'count': 1},
        {'category': 'B', 'value': 15, 'count': 1},
    ]
    
    result = transform_data(input_data)
    
    assert len(result) == 2
    assert result[0]['category'] == 'A'
    assert result[0]['value'] == 30
```

### Integration Test (with Celery)

```python
# tests/test_workflows.py
import pytest
from celery import Celery
from src.workflows.data_pipeline import run_etl_pipeline

@pytest.fixture
def celery_app():
    app = Celery('test', broker='memory://', backend='cache+memory://')
    app.conf.update(task_always_eager=True)  # Execute synchronously
    return app

def test_etl_pipeline(celery_app):
    result_id = run_etl_pipeline('test.csv', 'test_table')
    result = celery_app.AsyncResult(result_id)
    
    assert result.state == 'SUCCESS'
    assert result.result['rows_loaded'] > 0
```

---

## Running the Worker

### Start Worker

```bash
# Default queue
celery -A src.celery_app worker --loglevel=info

# Specific queue
celery -A src.celery_app worker -Q etl --loglevel=info

# Multiple queues
celery -A src.celery_app worker -Q etl,ml --loglevel=info

# With concurrency
celery -A src.celery_app worker --concurrency=4

# With autoscaling
celery -A src.celery_app worker --autoscale=10,3
```

### Start Celery Beat (Scheduler)

```bash
celery -A src.celery_app beat --loglevel=info
```

### Start Flower (Monitoring UI)

```bash
celery -A src.celery_app flower --port=5555
# Access at http://localhost:5555
```

---

## Deployment

### Docker

```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["celery", "-A", "src.celery_app", "worker", "--loglevel=info"]
```

### Docker Compose

```yaml
version: '3.8'
services:
  worker:
    build: .
    environment:
      - CELERY_BROKER_URL=redis://redis:6379/0
      - CELERY_RESULT_BACKEND=redis://redis:6379/1
    depends_on:
      - redis
    command: celery -A src.celery_app worker --loglevel=info
    restart: unless-stopped

  beat:
    build: .
    environment:
      - CELERY_BROKER_URL=redis://redis:6379/0
    depends_on:
      - redis
    command: celery -A src.celery_app beat --loglevel=info
    restart: unless-stopped

  flower:
    build: .
    ports:
      - "5555:5555"
    environment:
      - CELERY_BROKER_URL=redis://redis:6379/0
    depends_on:
      - redis
    command: celery -A src.celery_app flower --port=5555
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    volumes:
      - redis-data:/data
    restart: unless-stopped

volumes:
  redis-data:
```

---

## Monitoring & Metrics

### Flower Dashboard
Access at `http://localhost:5555`

Features:
- View active/scheduled/completed tasks
- Monitor worker status
- View task history and details
- Retry failed tasks
- Real-time graphs
- Task execution statistics

### Prometheus Metrics

```python
# src/utils/metrics.py
from prometheus_client import Counter, Histogram

tasks_processed = Counter(
    'celery_tasks_processed_total',
    'Total number of processed tasks',
    ['task_name', 'status']
)

task_duration = Histogram(
    'celery_task_duration_seconds',
    'Task execution time',
    ['task_name']
)

# Instrument tasks
from celery.signals import task_success, task_failure

@task_success.connect
def task_success_handler(sender=None, **kwargs):
    tasks_processed.labels(task_name=sender.name, status='success').inc()

@task_failure.connect
def task_failure_handler(sender=None, **kwargs):
    tasks_processed.labels(task_name=sender.name, status='failure').inc()
```

---

## Best Practices

### ✅ Do:
- Use **task routing** to separate workloads (ETL, ML, reports)
- Implement **idempotent tasks** (safe to retry)
- Use **task.bind=True** to access task context
- Set **task timeouts** to prevent hanging
- Use **result backends** for task status tracking
- Implement **exponential backoff** for retries
- Use **Canvas primitives** for complex workflows
- Monitor with **Flower**
- Use **task_acks_late** for reliable processing
- Implement **circuit breakers** for external APIs

### ❌ Don't:
- Don't pass **large objects** as arguments (use references)
- Don't use **mutable default arguments**
- Don't **ignore task failures** (log and monitor)
- Don't run **tasks synchronously** (use .apply_async())
- Don't forget to **configure result expiration**
- Don't use **pickle** serializer (use json)

---

## Common Pitfalls

### 1. Large Task Arguments
**Problem:** Passing large data as task arguments → broker overload

**Solution:**
```python
# ❌ Bad: Pass entire DataFrame
@app.task
def process_data(df_dict: dict):
    df = pd.DataFrame(df_dict)

# ✅ Good: Pass file reference
@app.task
def process_data(file_path: str):
    df = pd.read_csv(file_path)
```

### 2. Non-Idempotent Tasks
**Problem:** Retrying task causes duplicate actions

**Solution:**
```python
# ❌ Bad: Not idempotent
@app.task
def create_user(email: str):
    user = User(email=email)
    db.add(user)
    db.commit()

# ✅ Good: Idempotent with get_or_create
@app.task
def create_user(email: str):
    user = User.query.filter_by(email=email).first()
    if not user:
        user = User(email=email)
        db.add(user)
        db.commit()
    return user.id
```

### 3. Task Timeout Issues
**Problem:** Long-running tasks without timeout

**Solution:**
```python
# ✅ Set task timeout
@app.task(bind=True, time_limit=300, soft_time_limit=270)
def long_running_task(self):
    try:
        # Task logic
        pass
    except SoftTimeLimitExceeded:
        # Cleanup
        self.request.chain = None
        raise
```

---

## Related Documentation

- [Workers Overview](./README.md)
- [BullMQ Worker](./bullmq.md) - Node.js alternative
- [Go Worker](./golang.md) - High-performance alternative
- [Naming Conventions](../NAMING-CONVENTIONS.md)

---

## Reference Files

- **Instruction File:** `service-worker-celery.instructions.md`
- **Scaffold Prompt:** `scaffold-worker-celery-service.md`

---

## Next Steps

1. **Choose broker** (Redis for simplicity, RabbitMQ for features)
2. **Set up Celery app** with configuration
3. **Define tasks** for your use case
4. **Implement Canvas workflows** for complex pipelines
5. **Configure Celery Beat** for scheduled tasks
6. **Set up Flower** for monitoring
7. **Write tests** for tasks and workflows
8. **Deploy with Docker** and scale workers as needed
