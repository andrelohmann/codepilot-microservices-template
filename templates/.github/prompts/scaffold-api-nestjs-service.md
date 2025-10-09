# Scaffold NestJS API Service

**Objective:** Create a production-ready NestJS API service with complete directory structure, Docker configuration, testing setup, and comprehensive documentation.

---

## 📋 Service Specification

Before running this prompt, gather the following information:

1. **Service Name:** `[e.g., api-nestjs-auth, api-nestjs-payments]`
2. **Service Purpose:** `[Brief description of what this service does]`
3. **Database Required:** `[Yes/No - PostgreSQL]`
4. **Cache Required:** `[Yes/No - Redis]`
5. **Message Queue Required:** `[Yes/No - RabbitMQ/Bull]`
6. **External APIs:** `[List any external services to integrate]`
7. **Authentication:** `[JWT, OAuth, Passport, None]`
8. **Port:** `[Default: 3000, or specify custom]`

---

## 🏗️ Architecture Overview

```
┌────────────────────────────────────────────────────────────────────┐
│                      NestJS Service Architecture                   │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                     Controller Layer                         │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │ Controllers│  │   Guards   │  │Interceptors│            │ │
│  │  │ (Routes)   │  │  (Auth)    │  │ (Logging)  │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
│            ↓                ↓                ↓                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                      Service Layer                           │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │  Business  │  │   DTOs     │  │  External  │            │ │
│  │  │   Logic    │  │(Validation)│  │   Clients  │            │ │
│  │  └──────┬─────┘  └────────────┘  └────────────┘            │ │
│  └─────────┼────────────────────────────────────────────────────┘ │
│            │                                                      │
│            ↓                                                      │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                    Repository Layer                          │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │  TypeORM   │  │   Cache    │  │Bull Queue  │            │ │
│  │  │Repositories│  │  (Redis)   │  │  Service   │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
└────────────┼────────────────┼────────────────┼────────────────────┘
             │                │                │
             ↓                ↓                ↓
    ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
    │ PostgreSQL  │  │    Redis    │  │  RabbitMQ   │
    │  (Backing   │  │  (Backing   │  │  (Backing   │
    │   Service)  │  │   Service)  │  │   Service)  │
    └─────────────┘  └─────────────┘  └─────────────┘
```

---

## 📁 Directory Structure

Create the following complete directory structure:

```
services/[service-name]/
├── src/
│   ├── main.ts                    ← Application entry point
│   ├── app.module.ts              ← Root module
│   ├── app.controller.ts          ← Root controller
│   ├── app.service.ts             ← Root service
│   │
│   ├── config/                    ← Configuration
│   │   ├── configuration.ts       ← App configuration
│   │   ├── database.config.ts     ← Database config
│   │   ├── redis.config.ts        ← Redis config
│   │   └── validation.schema.ts   ← Environment validation
│   │
│   ├── common/                    ← Shared utilities
│   │   ├── decorators/            ← Custom decorators
│   │   │   ├── public.decorator.ts
│   │   │   └── current-user.decorator.ts
│   │   ├── filters/               ← Exception filters
│   │   │   ├── http-exception.filter.ts
│   │   │   └── all-exceptions.filter.ts
│   │   ├── guards/                ← Guards
│   │   │   ├── auth.guard.ts
│   │   │   └── roles.guard.ts
│   │   ├── interceptors/          ← Interceptors
│   │   │   ├── logging.interceptor.ts
│   │   │   └── transform.interceptor.ts
│   │   ├── pipes/                 ← Validation pipes
│   │   │   └── validation.pipe.ts
│   │   └── dto/                   ← Base DTOs
│   │       ├── pagination.dto.ts
│   │       └── response.dto.ts
│   │
│   ├── database/                  ← Database layer
│   │   ├── database.module.ts
│   │   ├── database.service.ts
│   │   ├── entities/              ← TypeORM entities
│   │   │   ├── base.entity.ts
│   │   │   └── user.entity.ts     ← Example entity
│   │   └── migrations/            ← TypeORM migrations
│   │       └── .gitkeep
│   │
│   ├── modules/                   ← Feature modules
│   │   ├── health/                ← Health check module
│   │   │   ├── health.module.ts
│   │   │   └── health.controller.ts
│   │   │
│   │   ├── auth/                  ← Authentication module
│   │   │   ├── auth.module.ts
│   │   │   ├── auth.controller.ts
│   │   │   ├── auth.service.ts
│   │   │   ├── strategies/
│   │   │   │   ├── jwt.strategy.ts
│   │   │   │   └── local.strategy.ts
│   │   │   └── dto/
│   │   │       ├── login.dto.ts
│   │   │       └── register.dto.ts
│   │   │
│   │   └── users/                 ← Example: Users module
│   │       ├── users.module.ts
│   │       ├── users.controller.ts
│   │       ├── users.service.ts
│   │       ├── users.repository.ts
│   │       └── dto/
│   │           ├── create-user.dto.ts
│   │           ├── update-user.dto.ts
│   │           └── user-response.dto.ts
│   │
│   └── utils/                     ← Utility functions
│       ├── logger.ts              ← Custom logger
│       ├── hash.ts                ← Password hashing
│       └── date.ts                ← Date utilities
│
├── test/                          ← E2E tests
│   ├── app.e2e-spec.ts
│   ├── jest-e2e.json
│   └── setup.ts
│
├── .eslintrc.js                   ← ESLint config
├── .prettierrc                    ← Prettier config
├── nest-cli.json                  ← Nest CLI config
├── tsconfig.json                  ← TypeScript config
├── tsconfig.build.json            ← Build config
├── package.json                   ← Dependencies
├── Dockerfile                     ← Production Docker image
├── .dockerignore                  ← Docker ignore patterns
├── README.md                      ← Service documentation
└── .env.example                   ← Environment variables template
```

---

## 📦 Step 1: `package.json`

```json
{
  "name": "[service-name]",
  "version": "1.0.0",
  "description": "[Service Purpose]",
  "author": "",
  "private": true,
  "license": "MIT",
  "scripts": {
    "build": "nest build",
    "format": "prettier --write \"src/**/*.ts\" \"test/**/*.ts\"",
    "start": "nest start",
    "start:dev": "nest start --watch",
    "start:debug": "nest start --debug --watch",
    "start:prod": "node dist/main",
    "lint": "eslint \"{src,apps,libs,test}/**/*.ts\" --fix",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:cov": "jest --coverage",
    "test:debug": "node --inspect-brk -r tsconfig-paths/register -r ts-node/register node_modules/.bin/jest --runInBand",
    "test:e2e": "jest --config ./test/jest-e2e.json",
    "typeorm": "typeorm-ts-node-commonjs",
    "migration:generate": "npm run typeorm -- migration:generate -d src/database/data-source.ts",
    "migration:run": "npm run typeorm -- migration:run -d src/database/data-source.ts",
    "migration:revert": "npm run typeorm -- migration:revert -d src/database/data-source.ts"
  },
  "dependencies": {
    "@nestjs/common": "^10.0.0",
    "@nestjs/core": "^10.0.0",
    "@nestjs/platform-express": "^10.0.0",
    "@nestjs/config": "^3.1.1",
    "@nestjs/typeorm": "^10.0.1",
    "@nestjs/swagger": "^7.1.16",
    "@nestjs/passport": "^10.0.3",
    "@nestjs/jwt": "^10.2.0",
    "@nestjs/throttler": "^5.0.1",
    "@nestjs/terminus": "^10.2.0",
    "typeorm": "^0.3.17",
    "pg": "^8.11.3",
    "redis": "^4.6.11",
    "passport": "^0.7.0",
    "passport-jwt": "^4.0.1",
    "passport-local": "^1.0.0",
    "bcrypt": "^5.1.1",
    "class-validator": "^0.14.0",
    "class-transformer": "^0.5.1",
    "helmet": "^7.1.0",
    "compression": "^1.7.4",
    "winston": "^3.11.0",
    "winston-daily-rotate-file": "^4.7.1",
    "reflect-metadata": "^0.1.13",
    "rxjs": "^7.8.1"
  },
  "devDependencies": {
    "@nestjs/cli": "^10.0.0",
    "@nestjs/schematics": "^10.0.0",
    "@nestjs/testing": "^10.0.0",
    "@types/express": "^4.17.17",
    "@types/jest": "^29.5.2",
    "@types/node": "^20.3.1",
    "@types/supertest": "^2.0.12",
    "@types/bcrypt": "^5.0.2",
    "@types/passport-jwt": "^4.0.0",
    "@types/passport-local": "^1.0.38",
    "@typescript-eslint/eslint-plugin": "^6.0.0",
    "@typescript-eslint/parser": "^6.0.0",
    "eslint": "^8.42.0",
    "eslint-config-prettier": "^9.0.0",
    "eslint-plugin-prettier": "^5.0.0",
    "jest": "^29.5.0",
    "prettier": "^3.0.0",
    "source-map-support": "^0.5.21",
    "supertest": "^6.3.3",
    "ts-jest": "^29.1.0",
    "ts-loader": "^9.4.3",
    "ts-node": "^10.9.1",
    "tsconfig-paths": "^4.2.0",
    "typescript": "^5.1.3"
  },
  "jest": {
    "moduleFileExtensions": [
      "js",
      "json",
      "ts"
    ],
    "rootDir": "src",
    "testRegex": ".*\\.spec\\.ts$",
    "transform": {
      "^.+\\.(t|j)s$": "ts-jest"
    },
    "collectCoverageFrom": [
      "**/*.(t|j)s"
    ],
    "coverageDirectory": "../coverage",
    "testEnvironment": "node"
  }
}
```

---

## 🔧 Step 2: `src/main.ts` - Application Entry Point

```typescript
import { NestFactory } from '@nestjs/core';
import { ValidationPipe, Logger } from '@nestjs/common';
import { SwaggerModule, DocumentBuilder } from '@nestjs/swagger';
import { ConfigService } from '@nestjs/config';
import * as compression from 'compression';
import helmet from 'helmet';
import { AppModule } from './app.module';
import { HttpExceptionFilter } from './common/filters/http-exception.filter';
import { LoggingInterceptor } from './common/interceptors/logging.interceptor';

async function bootstrap() {
  const app = await NestFactory.create(AppModule, {
    logger: ['error', 'warn', 'log', 'debug', 'verbose'],
  });

  const configService = app.get(ConfigService);
  const port = configService.get<number>('port', 3000);
  const logger = new Logger('Bootstrap');

  // Security
  app.use(helmet());
  app.enableCors({
    origin: configService.get<string>('cors.origin', '*'),
    credentials: true,
  });

  // Compression
  app.use(compression());

  // Global prefix
  app.setGlobalPrefix('api/v1');

  // Validation
  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,
      forbidNonWhitelisted: true,
      transform: true,
      transformOptions: {
        enableImplicitConversion: true,
      },
    }),
  );

  // Global filters
  app.useGlobalFilters(new HttpExceptionFilter());

  // Global interceptors
  app.useGlobalInterceptors(new LoggingInterceptor());

  // Swagger documentation
  const config = new DocumentBuilder()
    .setTitle('[Service Name] API')
    .setDescription('[Service Purpose]')
    .setVersion('1.0')
    .addBearerAuth()
    .addTag('[service-name]')
    .build();
  const document = SwaggerModule.createDocument(app, config);
  SwaggerModule.setup('api/docs', app, document);

  await app.listen(port);
  logger.log(`Application is running on: http://localhost:${port}`);
  logger.log(`Swagger documentation: http://localhost:${port}/api/docs`);
}

bootstrap();
```

---

## 🔧 Step 3: `src/app.module.ts` - Root Module

```typescript
import { Module } from '@nestjs/common';
import { ConfigModule, ConfigService } from '@nestjs/config';
import { TypeOrmModule } from '@nestjs/typeorm';
import { ThrottlerModule } from '@nestjs/throttler';
import { TerminusModule } from '@nestjs/terminus';
import configuration from './config/configuration';
import { DatabaseModule } from './database/database.module';
import { HealthModule } from './modules/health/health.module';
import { AuthModule } from './modules/auth/auth.module';
import { UsersModule } from './modules/users/users.module';
import { AppController } from './app.controller';
import { AppService } from './app.service';

@Module({
  imports: [
    // Configuration
    ConfigModule.forRoot({
      isGlobal: true,
      load: [configuration],
      envFilePath: ['.env.local', '.env'],
    }),

    // Rate limiting
    ThrottlerModule.forRoot([
      {
        ttl: 60000,
        limit: 10,
      },
    ]),

    // Database
    DatabaseModule,

    // Health checks
    TerminusModule,
    HealthModule,

    // Feature modules
    AuthModule,
    UsersModule,
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

---

## 🔧 Step 4: `src/config/configuration.ts` - Configuration

```typescript
export default () => ({
  port: parseInt(process.env.PORT, 10) || 3000,
  environment: process.env.NODE_ENV || 'development',
  
  database: {
    host: process.env.DB_HOST || 'localhost',
    port: parseInt(process.env.DB_PORT, 10) || 5432,
    username: process.env.DB_USER || 'postgres',
    password: process.env.DB_PASSWORD || 'postgres',
    database: process.env.DB_NAME || '[service-name]',
    synchronize: process.env.DB_SYNCHRONIZE === 'true',
    logging: process.env.DB_LOGGING === 'true',
  },

  redis: {
    host: process.env.REDIS_HOST || 'localhost',
    port: parseInt(process.env.REDIS_PORT, 10) || 6379,
    password: process.env.REDIS_PASSWORD || '',
    db: parseInt(process.env.REDIS_DB, 10) || 0,
  },

  jwt: {
    secret: process.env.JWT_SECRET || 'your-secret-key',
    expiresIn: process.env.JWT_EXPIRES_IN || '1h',
  },

  cors: {
    origin: process.env.CORS_ORIGIN || '*',
  },
});
```

---

## 🔧 Step 5: `src/database/database.module.ts` - Database Module

```typescript
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { ConfigService } from '@nestjs/config';
import { User } from './entities/user.entity';

@Module({
  imports: [
    TypeOrmModule.forRootAsync({
      inject: [ConfigService],
      useFactory: (configService: ConfigService) => ({
        type: 'postgres',
        host: configService.get('database.host'),
        port: configService.get('database.port'),
        username: configService.get('database.username'),
        password: configService.get('database.password'),
        database: configService.get('database.database'),
        entities: [User],
        synchronize: configService.get('database.synchronize'),
        logging: configService.get('database.logging'),
      }),
    }),
  ],
})
export class DatabaseModule {}
```

---

## 🔧 Step 6: `src/database/entities/user.entity.ts` - Example Entity

```typescript
import {
  Entity,
  PrimaryGeneratedColumn,
  Column,
  CreateDateColumn,
  UpdateDateColumn,
} from 'typeorm';

@Entity('users')
export class User {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ unique: true })
  email: string;

  @Column()
  password: string;

  @Column({ nullable: true })
  firstName: string;

  @Column({ nullable: true })
  lastName: string;

  @Column({ default: true })
  isActive: boolean;

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;
}
```

---

## 🔧 Step 7: `src/modules/health/health.controller.ts` - Health Check

```typescript
import { Controller, Get } from '@nestjs/common';
import { ApiTags, ApiOperation } from '@nestjs/swagger';
import {
  HealthCheck,
  HealthCheckService,
  TypeOrmHealthIndicator,
  MemoryHealthIndicator,
} from '@nestjs/terminus';

@ApiTags('health')
@Controller('health')
export class HealthController {
  constructor(
    private health: HealthCheckService,
    private db: TypeOrmHealthIndicator,
    private memory: MemoryHealthIndicator,
  ) {}

  @Get()
  @HealthCheck()
  @ApiOperation({ summary: 'Health check endpoint' })
  check() {
    return this.health.check([
      () => this.db.pingCheck('database'),
      () => this.memory.checkHeap('memory_heap', 150 * 1024 * 1024),
    ]);
  }
}
```

---

## 🔧 Step 8: `src/common/filters/http-exception.filter.ts` - Exception Filter

```typescript
import {
  ExceptionFilter,
  Catch,
  ArgumentsHost,
  HttpException,
  HttpStatus,
  Logger,
} from '@nestjs/common';
import { Request, Response } from 'express';

@Catch()
export class HttpExceptionFilter implements ExceptionFilter {
  private readonly logger = new Logger(HttpExceptionFilter.name);

  catch(exception: unknown, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();

    const status =
      exception instanceof HttpException
        ? exception.getStatus()
        : HttpStatus.INTERNAL_SERVER_ERROR;

    const message =
      exception instanceof HttpException
        ? exception.getResponse()
        : 'Internal server error';

    this.logger.error(
      `${request.method} ${request.url}`,
      exception instanceof Error ? exception.stack : exception,
    );

    response.status(status).json({
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: request.url,
      message,
    });
  }
}
```

---

## 🔧 Step 9: `src/common/interceptors/logging.interceptor.ts` - Logging

```typescript
import {
  Injectable,
  NestInterceptor,
  ExecutionContext,
  CallHandler,
  Logger,
} from '@nestjs/common';
import { Observable } from 'rxjs';
import { tap } from 'rxjs/operators';

@Injectable()
export class LoggingInterceptor implements NestInterceptor {
  private readonly logger = new Logger(LoggingInterceptor.name);

  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    const request = context.switchToHttp().getRequest();
    const { method, url } = request;
    const now = Date.now();

    return next.handle().pipe(
      tap(() => {
        const response = context.switchToHttp().getResponse();
        const { statusCode } = response;
        const responseTime = Date.now() - now;

        this.logger.log(
          `${method} ${url} ${statusCode} - ${responseTime}ms`,
        );
      }),
    );
  }
}
```

---

## 🔧 Step 10: `tsconfig.json` - TypeScript Configuration

```json
{
  "compilerOptions": {
    "module": "commonjs",
    "declaration": true,
    "removeComments": true,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "allowSyntheticDefaultImports": true,
    "target": "ES2021",
    "sourceMap": true,
    "outDir": "./dist",
    "baseUrl": "./",
    "incremental": true,
    "skipLibCheck": true,
    "strictNullChecks": false,
    "noImplicitAny": false,
    "strictBindCallApply": false,
    "forceConsistentCasingInFileNames": false,
    "noFallthroughCasesInSwitch": false,
    "paths": {
      "@/*": ["src/*"]
    }
  }
}
```

---

## 🐳 Step 11: `Dockerfile` - Production Docker Image

```dockerfile
# Build stage
FROM node:20-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production && \
    npm cache clean --force

# Copy source code
COPY . .

# Build application
RUN npm run build

# Production stage
FROM node:20-alpine

WORKDIR /app

# Copy built application
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package*.json ./

# Create non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nestjs -u 1001

USER nestjs

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3000/api/v1/health', (res) => { process.exit(res.statusCode === 200 ? 0 : 1); })"

EXPOSE 3000

CMD ["node", "dist/main"]
```

---

## 🔧 Step 12: `.env.example` - Environment Variables Template

```bash
# Application
NODE_ENV=production
PORT=3000

# Database
DB_HOST=postgres
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=postgres
DB_NAME=[service-name]
DB_SYNCHRONIZE=false
DB_LOGGING=false

# Redis
REDIS_HOST=redis
REDIS_PORT=6379
REDIS_PASSWORD=
REDIS_DB=0

# JWT
JWT_SECRET=your-secret-key-change-in-production
JWT_EXPIRES_IN=1h

# CORS
CORS_ORIGIN=*
```

---

## 🐳 Step 13: Docker Compose Integration

Add to `docker-compose.yml`:

```yaml
  [service-name]:
    build:
      context: ./services/[service-name]
      dockerfile: Dockerfile
    container_name: [service-name]
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DB_HOST=postgres
      - REDIS_HOST=redis
    env_file:
      - ./config/.env
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    networks:
      - microservices
    healthcheck:
      test: ["CMD", "node", "-e", "require('http').get('http://localhost:3000/api/v1/health', (res) => { process.exit(res.statusCode === 200 ? 0 : 1); })"]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 40s
```

---

## 🔧 Step 14: `.devcontainer/[service-name]-container/devcontainer.json`

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
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode",
        "firsttris.vscode-jest-runner",
        "ms-vscode.vscode-typescript-next"
      ],
      "settings": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
          "source.fixAll.eslint": "explicit"
        }
      }
    }
  },
  "forwardPorts": [3000],
  "postCreateCommand": "npm install",
  "remoteUser": "node"
}
```

---

## 📝 Step 15: `README.md` - Service Documentation

```markdown
# [Service Name]

[Service Purpose]

## Technology Stack

- **Framework:** NestJS 10
- **Language:** TypeScript 5
- **Database:** PostgreSQL (via TypeORM)
- **Cache:** Redis
- **Authentication:** JWT/Passport
- **API Documentation:** Swagger/OpenAPI
- **Testing:** Jest, Supertest

## Features

- ✅ RESTful API with OpenAPI/Swagger documentation
- ✅ TypeORM for database operations
- ✅ JWT authentication with Passport
- ✅ Request validation with class-validator
- ✅ Health checks
- ✅ Structured logging
- ✅ Rate limiting
- ✅ CORS configuration
- ✅ Security headers (Helmet)
- ✅ Compression
- ✅ Unit and E2E testing

## Getting Started

### Development

```bash
# Install dependencies
npm install

# Run development server
npm run start:dev

# Run tests
npm run test

# Run E2E tests
npm run test:e2e
```

### API Documentation

Access Swagger documentation at: `http://localhost:3000/api/docs`

## Environment Variables

See `.env.example` for required environment variables.

## Database Migrations

```bash
# Generate migration
npm run migration:generate -- src/database/migrations/MigrationName

# Run migrations
npm run migration:run

# Revert migration
npm run migration:revert
```

## Testing

```bash
# Unit tests
npm run test

# E2E tests
npm run test:e2e

# Test coverage
npm run test:cov
```

## API Endpoints

### Health Check
- `GET /api/v1/health` - Service health status

### Authentication
- `POST /api/v1/auth/login` - Login
- `POST /api/v1/auth/register` - Register

### Users
- `GET /api/v1/users` - List users
- `GET /api/v1/users/:id` - Get user
- `POST /api/v1/users` - Create user
- `PATCH /api/v1/users/:id` - Update user
- `DELETE /api/v1/users/:id` - Delete user
```

---

## ✅ Completion Checklist

- [ ] All directories created
- [ ] `package.json` configured with all dependencies
- [ ] `src/main.ts` entry point created
- [ ] `src/app.module.ts` root module created
- [ ] Configuration files created
- [ ] Database module and entities created
- [ ] Health check module created
- [ ] Auth module scaffolded
- [ ] Example Users module created
- [ ] Guards, filters, and interceptors implemented
- [ ] TypeScript configuration complete
- [ ] `Dockerfile` created with production-ready setup
- [ ] `.dockerignore` configured
- [ ] `.env.example` documented
- [ ] Docker Compose integration added
- [ ] `.devcontainer/[service-name]-container/devcontainer.json` created at project root
- [ ] `README.md` completed
- [ ] ESLint and Prettier configured
- [ ] Jest testing configured
- [ ] Service builds successfully
- [ ] All tests pass
- [ ] Health check endpoint working
- [ ] Swagger documentation accessible

---

**Note:** Replace all `[service-name]` and `[Service Name]` placeholders with your actual service name. Update configurations based on your specific requirements.
