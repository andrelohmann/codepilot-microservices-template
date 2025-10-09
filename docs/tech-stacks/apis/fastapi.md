# FastAPI - Python API Services

Python FastAPI framework for building high-performance async APIs with automatic documentation.

## Overview

FastAPI is a modern, fast (high-performance) web framework for building APIs with Python 3.11+ based on standard Python type hints.

**Perfect for:**
- Data-intensive APIs
- ML model serving
- Async I/O operations
- Python-centric ecosystems
- Rapid API development

---

## Naming Convention

```
api-fastapi-{purpose}
```

### Examples

```
api-fastapi-auth          # Authentication service
api-fastapi-billing       # Billing/payment processing
api-fastapi-users         # User management
api-fastapi-ml-inference  # ML model serving
api-fastapi-data          # Data transformation API
```

---

## Technology Stack

### Core Technologies

- **Python** 3.11+
- **FastAPI** 0.104+
- **Pydantic** 2.0+ (data validation)
- **SQLAlchemy** 2.0 (async ORM)
- **Alembic** (database migrations)
- **uvicorn** (ASGI server)

### Key Dependencies

**Production:**
- `fastapi` - Web framework
- `uvicorn[standard]` - ASGI server
- `pydantic` - Data validation
- `sqlalchemy` - ORM
- `alembic` - Migrations
- `python-jose` - JWT tokens
- `passlib` - Password hashing
- `python-multipart` - File uploads

**Development:**
- `pytest` - Testing framework
- `pytest-asyncio` - Async test support
- `httpx` - Async HTTP client for tests
- `black` - Code formatter
- `ruff` - Linter
- `mypy` - Type checker

---

## Key Features

### 1. Async/Await Support

```python
from fastapi import FastAPI
from sqlalchemy.ext.asyncio import AsyncSession

app = FastAPI()

@app.get("/users/{user_id}")
async def get_user(user_id: int, db: AsyncSession = Depends(get_db)):
    user = await db.get(User, user_id)
    return user
```

### 2. Auto-Generated OpenAPI Documentation

- **Swagger UI:** `/docs`
- **ReDoc:** `/redoc`
- **OpenAPI JSON:** `/openapi.json`

Automatically generated from your code and type hints.

### 3. Type Hints and Validation

```python
from pydantic import BaseModel, EmailStr, Field

class UserCreate(BaseModel):
    email: EmailStr
    password: str = Field(min_length=8)
    age: int = Field(ge=18, le=120)

@app.post("/users/")
async def create_user(user: UserCreate):
    # Automatically validated!
    return user
```

### 4. Dependency Injection

```python
from fastapi import Depends, HTTPException

async def get_current_user(token: str = Depends(oauth2_scheme)):
    user = await verify_token(token)
    if not user:
        raise HTTPException(status_code=401)
    return user

@app.get("/me")
async def read_users_me(current_user: User = Depends(get_current_user)):
    return current_user
```

### 5. JWT Authentication

```python
from jose import JWTError, jwt
from passlib.context import CryptContext

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def create_access_token(data: dict):
    return jwt.encode(data, SECRET_KEY, algorithm=ALGORITHM)
```

---

## Ideal Use Cases

### 1. Data-Intensive APIs ⭐

**Why FastAPI:**
- Python's rich data science ecosystem
- Async support for concurrent requests
- Efficient data serialization with Pydantic
- Easy integration with pandas, numpy

**Example:**
```python
@app.post("/transform")
async def transform_data(data: List[DataPoint]):
    df = pd.DataFrame([d.dict() for d in data])
    result = await process_dataframe(df)
    return {"result": result.to_dict()}
```

### 2. ML Model Serving ⭐⭐⭐

**Why FastAPI:**
- Python ML libraries (TensorFlow, PyTorch, scikit-learn)
- Async model inference
- Request validation
- Batch processing support

**Example:**
```python
model = load_model("model.h5")

@app.post("/predict")
async def predict(features: Features):
    prediction = await model.predict_async(features.values)
    return {"prediction": prediction}
```

### 3. Async I/O Operations ⭐⭐

**Why FastAPI:**
- Native async/await
- Handles many concurrent connections
- Non-blocking database queries
- Efficient API composition

**Example:**
```python
@app.get("/aggregated")
async def get_aggregated_data():
    user_task = fetch_users()
    order_task = fetch_orders()
    payment_task = fetch_payments()
    
    users, orders, payments = await asyncio.gather(
        user_task, order_task, payment_task
    )
    return {"users": users, "orders": orders, "payments": payments}
```

### 4. Microservices with Python Ecosystem ⭐

**Why FastAPI:**
- Lightweight and fast
- Easy service-to-service communication
- Good for Python-centric stacks
- Built-in background tasks

---

## Project Structure

```
services/api-fastapi-{purpose}/
├── src/
│   ├── main.py                    # Application entry point
│   ├── api/
│   │   ├── __init__.py
│   │   ├── deps.py               # Dependencies
│   │   └── v1/
│   │       ├── __init__.py
│   │       ├── auth.py           # Auth endpoints
│   │       └── users.py          # User endpoints
│   ├── core/
│   │   ├── __init__.py
│   │   ├── config.py             # Settings
│   │   ├── security.py           # Auth logic
│   │   └── db.py                 # Database connection
│   ├── models/
│   │   ├── __init__.py
│   │   └── user.py               # SQLAlchemy models
│   ├── schemas/
│   │   ├── __init__.py
│   │   └── user.py               # Pydantic schemas
│   └── services/
│       ├── __init__.py
│       └── user_service.py       # Business logic
├── tests/
│   ├── __init__.py
│   ├── conftest.py               # Test fixtures
│   ├── test_auth.py
│   └── test_users.py
├── alembic/
│   ├── versions/                 # Migrations
│   └── env.py
├── Dockerfile
├── requirements.txt
├── .env.example
├── alembic.ini
├── pyproject.toml
└── README.md
```

---

## Testing

### Unit Tests

```python
import pytest
from httpx import AsyncClient
from src.main import app

@pytest.mark.asyncio
async def test_create_user():
    async with AsyncClient(app=app, base_url="http://test") as ac:
        response = await ac.post("/users/", json={
            "email": "test@example.com",
            "password": "securepassword123"
        })
    assert response.status_code == 201
    assert response.json()["email"] == "test@example.com"
```

### Test Frameworks

- **pytest** - Testing framework
- **pytest-asyncio** - Async test support
- **httpx** - Async HTTP client
- **pytest-cov** - Coverage reports

### Run Tests

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=src --cov-report=html

# Run specific test file
pytest tests/test_users.py

# Run with verbose output
pytest -v
```

---

## Deployment

### Dockerfile

```dockerfile
# Multi-stage build
FROM python:3.11-slim as builder

WORKDIR /app
COPY requirements.txt .
RUN pip install --user --no-cache-dir -r requirements.txt

FROM python:3.11-slim

WORKDIR /app
COPY --from=builder /root/.local /root/.local
COPY src/ ./src/
COPY alembic/ ./alembic/
COPY alembic.ini .

ENV PATH=/root/.local/bin:$PATH
ENV PYTHONUNBUFFERED=1

EXPOSE 8000

# Non-root user
RUN useradd -m -u 1000 apiuser && chown -R apiuser:apiuser /app
USER apiuser

CMD ["uvicorn", "src.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Environment Variables

```bash
# .env.example
DATABASE_URL=postgresql+asyncpg://user:pass@localhost/db
SECRET_KEY=your-secret-key-here
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

# Redis (optional)
REDIS_URL=redis://localhost:6379

# CORS (optional)
BACKEND_CORS_ORIGINS=["http://localhost:3000"]
```

---

## Performance Tips

### 1. Use Async Database Queries

```python
# ❌ Blocking (slow)
user = db.query(User).filter(User.id == user_id).first()

# ✅ Async (fast)
user = await db.execute(select(User).where(User.id == user_id))
user = user.scalar_one_or_none()
```

### 2. Enable Response Caching

```python
from fastapi_cache import FastAPICache
from fastapi_cache.backends.redis import RedisBackend
from fastapi_cache.decorator import cache

@app.get("/users/{user_id}")
@cache(expire=60)
async def get_user(user_id: int):
    return await fetch_user(user_id)
```

### 3. Use Background Tasks

```python
from fastapi import BackgroundTasks

def send_email_background(email: str, message: str):
    # Send email (runs in background)
    send_email(email, message)

@app.post("/register")
async def register(user: UserCreate, background_tasks: BackgroundTasks):
    # Create user immediately
    new_user = await create_user(user)
    
    # Send welcome email in background
    background_tasks.add_task(send_email_background, user.email, "Welcome!")
    
    return new_user
```

---

## Best Practices

### 1. Use Pydantic Models for Validation

```python
from pydantic import BaseModel, validator

class UserCreate(BaseModel):
    email: EmailStr
    password: str
    
    @validator('password')
    def password_strength(cls, v):
        if len(v) < 8:
            raise ValueError('Password must be at least 8 characters')
        return v
```

### 2. Separate Schemas from Models

```python
# models/user.py (SQLAlchemy)
class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True)
    email = Column(String, unique=True)

# schemas/user.py (Pydantic)
class UserBase(BaseModel):
    email: EmailStr

class UserCreate(UserBase):
    password: str

class UserResponse(UserBase):
    id: int
    class Config:
        from_attributes = True
```

### 3. Use Dependency Injection

```python
async def get_db() -> AsyncGenerator:
    async with AsyncSessionLocal() as session:
        yield session

@app.get("/users")
async def list_users(db: AsyncSession = Depends(get_db)):
    return await db.execute(select(User)).scalars().all()
```

---

## Common Pitfalls

### ❌ Blocking I/O in Async Endpoints

```python
# Wrong
@app.get("/data")
async def get_data():
    time.sleep(5)  # Blocks event loop!
    return {"data": "result"}

# Correct
@app.get("/data")
async def get_data():
    await asyncio.sleep(5)  # Non-blocking
    return {"data": "result"}
```

### ❌ Not Using Async Database Queries

```python
# Wrong
@app.get("/users")
async def list_users():
    users = db.query(User).all()  # Blocking!
    return users

# Correct
@app.get("/users")
async def list_users(db: AsyncSession = Depends(get_db)):
    result = await db.execute(select(User))
    return result.scalars().all()
```

---

## Related Files

- **Instruction File:** `templates/.github/instructions/service-api-fastapi.instructions.md`
- **Scaffold Prompt:** `templates/.github/prompts/scaffold-api-fastapi-service.md`

---

## See Also

- **[API Services Overview](./README.md)** - Compare with other API technologies
- **[NestJS](./nestjs.md)** - Node.js alternative
- **[Fiber](./fiber.md)** - Go alternative
- **[Naming Conventions](../NAMING-CONVENTIONS.md)** - Service naming rules

---

**Last Updated:** 2025-10-09  
**Template Version:** 1.0.0
