---
description: "Scaffold a NestJS REST API service"
mode: agent
tools: [file, workspace, terminal]
---

# Scaffold NestJS Service

Create a production-ready `${input:serviceName}` service using NestJS with TypeScript, TypeORM, and complete project structure.

## Reference Documentation

- **[NestJS Tech-Stack Guide](../../../../docs/tech-stacks/apis/nestjs.md)** - Complete implementation details and examples
- **[NestJS Instructions](../instructions/service-api-nestjs.instructions.md)** - Copilot coding guidance

## Service Configuration

Ask the user for:
- **Service Name**: `${input:serviceName}` (e.g., `api-nestjs-products`, `api-nestjs-inventory`)
- **Purpose**: `${input:servicePurpose}` (brief description)
- **Port**: `${input:port:3000}` (default: 3000)
- **Database**: `${input:database:PostgreSQL}` (PostgreSQL, MySQL, None)
- **Authentication**: `${input:auth:JWT}` (JWT, API Key, None)

## Project Structure

```
services/${input:serviceName}/
├── src/
│   ├── main.ts              # Application entry
│   ├── app.module.ts        # Root module
│   ├── common/              # Shared utilities
│   │   ├── decorators/
│   │   ├── filters/
│   │   ├── guards/
│   │   └── interceptors/
│   ├── config/              # Configuration
│   │   └── database.config.ts
│   ├── modules/             # Feature modules
│   │   └── users/
│   │       ├── users.module.ts
│   │       ├── users.controller.ts
│   │       ├── users.service.ts
│   │       ├── dto/
│   │       └── entities/
│   └── database/            # Migrations
├── test/
├── Dockerfile
├── package.json
├── tsconfig.json
└── README.md
```

## Implementation Steps

1. **Initialize NestJS Project**
   - Generate base structure in `services/` directory
   - Install dependencies: `@nestjs/core`, `@nestjs/platform-express`, `typeorm`, `@nestjs/typeorm`

2. **Core Application Files**
   - `src/main.ts` - Bootstrap with Swagger, CORS, validation pipe
   - `src/app.module.ts` - Root module with ConfigModule, TypeOrmModule
   - `src/common/filters/http-exception.filter.ts` - Global exception handler
   - Refer to [tech-stack guide](../../../../docs/tech-stacks/apis/nestjs.md#quick-start) for templates

3. **Configuration Module**
   - `src/config/database.config.ts` - TypeORM configuration factory
   - Use `@nestjs/config` for environment variables
   - Validation with class-validator

4. **Database Setup** (if ${input:database} != None)
   - `src/database/migrations/` - Directory for TypeORM migrations
   - `ormconfig.ts` - TypeORM CLI configuration
   - Example entity: `src/modules/users/entities/user.entity.ts`

5. **Feature Modules**
   - Generate module: `nest g module modules/users`
   - Generate controller: `nest g controller modules/users`
   - Generate service: `nest g service modules/users`
   - Create DTOs with class-validator decorators
   - Create entities with TypeORM decorators

6. **DTOs (Data Transfer Objects)**
   - `dto/create-*.dto.ts` - Request validation
   - `dto/update-*.dto.ts` - Partial update validation
   - `dto/*-response.dto.ts` - Response serialization
   - Use `class-validator` and `class-transformer`

7. **Authentication Module** (if ${input:auth} != None)
   - `src/modules/auth/auth.module.ts`
   - `src/modules/auth/auth.service.ts` - JWT generation/validation
   - `src/common/guards/jwt-auth.guard.ts` - Route protection
   - `src/common/decorators/current-user.decorator.ts`
   - Refer to [auth examples](../../../../docs/tech-stacks/apis/nestjs.md#authentication)

8. **API Documentation (Swagger)**
   - Configure Swagger in `main.ts`
   - Add `@ApiTags`, `@ApiOperation`, `@ApiResponse` decorators
   - Document DTOs with `@ApiProperty`

9. **Testing Setup**
   - `test/app.e2e-spec.ts` - E2E tests with supertest
   - Unit test files: `*.spec.ts` alongside services
   - Test module with mock repository

10. **Docker Configuration**
    - `Dockerfile` - Multi-stage Node.js build
    - `.dockerignore` - Exclude node_modules, dist
    - Health check endpoint: `/health`

11. **Dependencies**
    - `package.json` - NestJS, TypeORM, class-validator, Swagger, Jest
    - Lock file for reproducibility

12. **Docker Compose Integration**
    - Update `docker-compose.yml` in project root
    - Add service with ports, environment, database dependency
    - Configure TypeORM connection from environment

13. **Development Configuration**
    - `.devcontainer/` - VS Code devcontainer
    - `.env.example` - Environment template
    - `nest-cli.json` - NestJS CLI configuration

14. **Verify Service**
    - Start service: `docker compose up ${input:serviceName}`
    - Test health endpoint: `curl http://localhost:${input:port}/health`
    - Check Swagger docs: `http://localhost:${input:port}/api`
    - Run migrations: `npm run migration:run`

## Code Templates

All code templates are available in the [NestJS Tech-Stack Documentation](../../../../docs/tech-stacks/apis/nestjs.md):
- Application bootstrap
- Module structure
- Controllers with decorators
- Services with dependency injection
- TypeORM entities and repositories
- DTOs with validation
- Guards and interceptors
- Testing patterns

## Completion Checklist

- [ ] Directory structure created
- [ ] NestJS application configured
- [ ] Database connection and TypeORM set up
- [ ] Feature modules created (users, auth)
- [ ] DTOs with validation defined
- [ ] Controllers with Swagger documentation
- [ ] Services with business logic
- [ ] Authentication implemented (if required)
- [ ] Tests written and passing
- [ ] Dockerfile builds successfully
- [ ] docker-compose.yml updated
- [ ] README.md documentation complete
- [ ] Service accessible at `http://localhost:${input:port}/api`
- [ ] Health endpoint returns 200 OK

## Post-Scaffolding

After scaffolding completes:
1. Run migrations: `docker compose exec ${input:serviceName} npm run migration:run`
2. Run tests: `docker compose exec ${input:serviceName} npm test`
3. Review Swagger documentation at `/api`
4. Refer to [instructions file](../instructions/service-api-nestjs.instructions.md) for Copilot coding guidance

---

**Note**: This prompt creates the scaffolding only. Refer to the [complete tech-stack documentation](../../../../docs/tech-stacks/apis/nestjs.md) for detailed implementation patterns, best practices, and code examples.
