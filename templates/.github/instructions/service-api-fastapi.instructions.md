---
applyTo: "**/api-fastapi-*/**"
description: FastAPI service with Python, Pydantic, SQLAlchemy, and async support
---

# API FastAPI - Modern Python API

Build high-performance RESTful APIs with FastAPI using async Python and Pydantic validation.

## Naming Convention

`api-fastapi-{purpose}` (e.g., `api-fastapi-auth`, `api-fastapi-catalog`)

## Technology Stack

- FastAPI 0.104+ (framework)
- Python 3.11+ (language)
- Pydantic 2+ (validation/serialization)
- SQLAlchemy 2+ (ORM with async support)
- Alembic (migrations)
- python-jose (JWT)
- passlib (password hashing)
- pytest + httpx (testing)

## Project Structure

```
services/api-fastapi-{purpose}/
├── src/
│   ├── main.py
│   ├── config.py
│   ├── database.py
│   ├── models/
│   │   ├── __init__.py
│   │   └── user.py
│   ├── schemas/
│   │   ├── __init__.py
│   │   └── user.py
│   ├── routers/
│   │   ├── __init__.py
│   │   ├── users.py
│   │   └── auth.py
│   ├── services/
│   │   └── user_service.py
│   ├── dependencies.py
│   └── middleware/
├── tests/
│   ├── __init__.py
│   ├── conftest.py
│   └── test_users.py
├── alembic/
│   └── versions/
├── Dockerfile
├── requirements.txt
└── pyproject.toml
```

## Core Patterns

### Router Structure
Each domain = one router module:
```python
from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.ext.asyncio import AsyncSession

router = APIRouter(prefix="/users", tags=["users"])

@router.post("/", response_model=UserResponse)
async def create_user(
    user: UserCreate,
    db: AsyncSession = Depends(get_db),
):
    # Implementation
    pass
```

### Pydantic Schemas
Use Pydantic v2 models for validation:
```python
from pydantic import BaseModel, EmailStr, Field

class UserCreate(BaseModel):
    email: EmailStr
    password: str = Field(min_length=8)
    
class UserResponse(BaseModel):
    id: int
    email: str
    
    model_config = ConfigDict(from_attributes=True)
```

### SQLAlchemy Models
Use SQLAlchemy 2.0 style with async:
```python
from sqlalchemy.orm import Mapped, mapped_column
from sqlalchemy.ext.asyncio import AsyncAttrs

class User(AsyncAttrs, Base):
    __tablename__ = "users"
    
    id: Mapped[int] = mapped_column(primary_key=True)
    email: Mapped[str] = mapped_column(unique=True)
    hashed_password: Mapped[str]
```

### Async Database Sessions
Use dependency injection for sessions:
```python
async def get_db() -> AsyncGenerator[AsyncSession, None]:
    async with async_session_maker() as session:
        try:
            yield session
            await session.commit()
        except Exception:
            await session.rollback()
            raise
```

## Configuration

Use Pydantic Settings:
```python
from pydantic_settings import BaseSettings, SettingsConfigDict

class Settings(BaseSettings):
    database_url: str
    jwt_secret: str = Field(min_length=32)
    
    model_config = SettingsConfigDict(
        env_file=".env",
        case_sensitive=False
    )
```

## Middleware & Dependencies

### CORS Configuration
```python
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # Restrict in production
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

### Authentication Dependency
```python
async def get_current_user(
    token: str = Depends(oauth2_scheme),
    db: AsyncSession = Depends(get_db),
) -> User:
    # Verify JWT and return user
    pass
```

## Testing Standards

### Test Structure
- Unit tests: Test individual functions
- Integration tests: Test with database
- Coverage target: >80%

### Test Fixtures
```python
@pytest.fixture
async def db_session():
    async with async_session_maker() as session:
        yield session
        await session.rollback()

@pytest.fixture
def client(db_session):
    app.dependency_overrides[get_db] = lambda: db_session
    return TestClient(app)
```

### Async Testing
```python
@pytest.mark.asyncio
async def test_create_user(client):
    response = client.post("/users/", json={
        "email": "test@example.com",
        "password": "secure123"
    })
    assert response.status_code == 201
```

## Anti-Patterns

❌ **Avoid:**
- Synchronous database operations (use async)
- Missing Pydantic validation on endpoints
- Hardcoded secrets in code
- Blocking I/O operations (file, network)
- Missing error handling
- No request/response models
- Missing database migrations
- Insufficient test coverage (<60%)

✅ **Do:**
- Use async/await for all I/O operations
- Define Pydantic models for all endpoints
- Use environment variables for configuration
- Implement proper exception handlers
- Use dependency injection
- Version your API routes (/v1/)
- Write comprehensive tests
- Document endpoints with OpenAPI

## Dependencies

### Core
```
fastapi==0.104.1
uvicorn[standard]==0.24.0
pydantic==2.5.0
pydantic-settings==2.1.0
```

### Database
```
sqlalchemy[asyncio]==2.0.23
alembic==1.13.0
asyncpg==0.29.0
```

### Auth
```
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
python-multipart==0.0.6
```

### Testing
```
pytest==7.4.3
pytest-asyncio==0.21.1
httpx==0.25.2
pytest-cov==4.1.0
```
