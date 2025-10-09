---
applyTo: "**/api-fastapi-*/**"
description: FastAPI service with Python, Pydantic, SQLAlchemy, and async support
---

# FastAPI API Service - Copilot Guidance

Quick reference for GitHub Copilot when working with FastAPI API services.

## Reference Documentation

- **[Complete FastAPI Tech-Stack Guide](../../../docs/tech-stacks/apis/fastapi.md)** - Full implementation details, examples, and best practices
- **[Scaffold Prompt](../prompts/scaffold-fastapi-service.prompt.md)** - Generate new FastAPI services

## Naming Convention

`api-fastapi-{purpose}` (e.g., `api-fastapi-auth`, `api-fastapi-billing`)

## Project Structure

```
services/api-fastapi-{purpose}/
├── src/
│   ├── main.py              # FastAPI app initialization
│   ├── config.py            # Pydantic Settings
│   ├── database.py          # SQLAlchemy async setup
│   ├── models/              # SQLAlchemy ORM models
│   ├── schemas/             # Pydantic validation models
│   ├── routers/             # API route handlers
│   ├── services/            # Business logic layer
│   ├── dependencies.py      # FastAPI dependencies
│   └── middleware/          # Custom middleware
├── tests/                   # pytest test suite
├── alembic/                 # Database migrations
├── Dockerfile
├── requirements.txt
└── pyproject.toml
```

## Copilot Coding Guidance

### Use These Patterns

1. **Async Everything** - All I/O operations must use `async/await`
2. **Pydantic v2** - Use `BaseModel` with `model_config = ConfigDict(from_attributes=True)`
3. **SQLAlchemy 2.0** - Use `Mapped[type]` and `mapped_column()` syntax
4. **Dependency Injection** - Use `Depends()` for database sessions, auth, etc.
5. **Router Organization** - One router per domain (`users.py`, `auth.py`)
6. **Type Hints** - All functions must have complete type annotations

### Quick Patterns Reference

**Router with Async DB**:
```python
from fastapi import APIRouter, Depends
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

**Pydantic Schema**:
```python
from pydantic import BaseModel, EmailStr

class UserCreate(BaseModel):
    email: EmailStr
    password: str
    
class UserResponse(BaseModel):
    id: int
    email: str
    model_config = ConfigDict(from_attributes=True)
```

**SQLAlchemy Model**:
```python
from sqlalchemy.orm import Mapped, mapped_column

class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)
    email: Mapped[str] = mapped_column(unique=True)
```

### Code Style

- **Imports**: Group by stdlib, third-party, local
- **Naming**: `snake_case` for functions/variables, `PascalCase` for classes
- **Async**: Prefix async functions with `async def`, use `await` for all I/O
- **Error Handling**: Use `HTTPException` with appropriate status codes
- **Testing**: Write async tests with `@pytest.mark.asyncio`

### Anti-Patterns to Avoid

❌ Synchronous database operations  
❌ Missing Pydantic validation  
❌ Hardcoded configuration values  
❌ Blocking I/O in async functions  
❌ Missing type hints  
❌ No error handling  

For detailed examples, deployment guides, testing strategies, and advanced patterns, refer to the [FastAPI Tech-Stack Documentation](../../../docs/tech-stacks/apis/fastapi.md).

