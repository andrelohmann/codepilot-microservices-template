---
applyTo: "**/test_*.py,**/test_*.ts,**/*.test.ts,**/*.spec.ts,**/tests/**,**/pytest.ini,**/jest.config.js"
description: Testing standards and patterns
---

# Testing Standards

Test pyramid: 70% unit, 20% integration, 10% e2e. Coverage >80%, critical paths 100%.

## Test Structure

```
tests/
├── conftest.py / jest.setup.ts  # Fixtures
├── .env.test                     # Test environment
├── unit/                         # 70% - Fast, isolated
├── integration/                  # 20% - DB, cache, queue
└── e2e/                          # 10% - Full workflows
```

## Naming Convention

`test_<function>_<scenario>_<expected>`

Examples:
- `test_create_user_with_valid_data_returns_201`
- `test_login_with_invalid_password_returns_401`

## AAA Pattern

```python
def test_example():
    # Arrange: Setup
    user = UserFactory(email="test@example.com")
    
    # Act: Execute
    result = authenticate_user(user.email, "password123")
    
    # Assert: Verify
    assert result.is_authenticated is True
```

## Python (pytest)

Config (`pytest.ini`):
```ini
[pytest]
testpaths = tests
addopts = --verbose --cov=app --cov-fail-under=80
markers =
    unit: Unit tests
    integration: Integration tests
    e2e: End-to-end tests
```

Fixtures (`conftest.py`):
- `db_session` - Database with rollback
- `client` - TestClient with dependency overrides
- `redis_client` - Redis with cleanup
- Use testcontainers for integration tests

## TypeScript (Jest)

Config (`jest.config.js`):
```javascript
module.exports = {
  preset: 'ts-jest',
  testMatch: ['**/?(*.)+(spec|test).ts'],
  coverageThreshold: {
    global: { lines: 80, functions: 80, branches: 80 }
  }
};
```

## Factories

Python (factory-boy):
```python
class UserFactory(factory.alchemy.SQLAlchemyModelFactory):
    class Meta:
        model = User
    email = Faker("email")
    hashed_password = "default_hash"
```

## CI/CD

Run in pipeline:
```yaml
- pytest tests/unit -v --cov=app
- pytest tests/integration -v
- Upload coverage to Codecov
```

## Anti-Patterns

- ❌ Tests depend on each other
- ❌ No AAA structure
- ❌ Mocking everything (test mocks, not code)
- ❌ Ignoring flaky tests
- ❌ No factories for test data
