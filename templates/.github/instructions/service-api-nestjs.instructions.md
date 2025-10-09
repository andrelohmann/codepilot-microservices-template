---
applyTo: "**/api-nestjs-*/**"
description: NestJS API service with TypeScript, TypeORM, and dependency injection
---

# API NestJS - Enterprise Node.js API

Build RESTful APIs with NestJS framework using TypeScript and modular architecture.

## Naming Convention

`api-nestjs-{purpose}` (e.g., `api-nestjs-billing`, `api-nestjs-payments`)

## Technology Stack

- NestJS 10+ (framework)
- TypeScript 5+ (language)
- TypeORM (database ORM)
- class-validator (validation)
- class-transformer (serialization)
- Passport.js (authentication)
- Swagger/OpenAPI (documentation)
- Jest (testing)

## Directory Structure

```
services/api-nestjs-{purpose}/
├── src/
│   ├── main.ts
│   ├── app.module.ts
│   ├── config/
│   │   └── configuration.ts
│   ├── modules/
│   │   ├── users/
│   │   │   ├── users.module.ts
│   │   │   ├── users.controller.ts
│   │   │   ├── users.service.ts
│   │   │   ├── entities/user.entity.ts
│   │   │   └── dto/create-user.dto.ts
│   │   └── auth/
│   ├── common/
│   │   ├── filters/
│   │   ├── guards/
│   │   ├── interceptors/
│   │   └── decorators/
│   └── database/
│       └── migrations/
├── test/
│   ├── unit/
│   └── e2e/
├── Dockerfile
├── package.json
├── tsconfig.json
└── nest-cli.json
```

## Module Pattern

Each feature = one module:
```typescript
@Module({
  imports: [TypeOrmModule.forFeature([User])],
  controllers: [UsersController],
  providers: [UsersService],
  exports: [UsersService],
})
export class UsersModule {}
```

## Dependency Injection

Use constructor injection:
```typescript
@Injectable()
export class UsersService {
  constructor(
    @InjectRepository(User)
    private usersRepository: Repository<User>,
    private configService: ConfigService,
  ) {}
}
```

## Validation

Use class-validator DTOs:
```typescript
export class CreateUserDto {
  @IsEmail()
  email: string;

  @IsString()
  @MinLength(8)
  password: string;
}
```

## Configuration

Use @nestjs/config:
```typescript
@Module({
  imports: [
    ConfigModule.forRoot({
      isGlobal: true,
      validationSchema: Joi.object({
        DATABASE_URL: Joi.string().required(),
        JWT_SECRET: Joi.string().min(32).required(),
      }),
    }),
  ],
})
```

## Database

TypeORM with migrations:
```typescript
@Entity()
export class User {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ unique: true })
  email: string;

  @CreateDateColumn()
  createdAt: Date;
}
```

## Testing

- Jest for unit tests
- Supertest for E2E tests
- Test coverage >80%

```typescript
describe('UsersService', () => {
  let service: UsersService;

  beforeEach(async () => {
    const module = await Test.createTestingModule({
      providers: [UsersService, mockRepository],
    }).compile();

    service = module.get<UsersService>(UsersService);
  });
});
```

## API Documentation

Swagger auto-generated:
```typescript
@ApiTags('users')
@Controller('users')
export class UsersController {
  @ApiOperation({ summary: 'Create user' })
  @ApiResponse({ status: 201, type: User })
  @Post()
  create(@Body() dto: CreateUserDto) {}
}
```

## Health Checks

```typescript
@Controller('health')
export class HealthController {
  constructor(
    private health: HealthCheckService,
    private db: TypeOrmHealthIndicator,
  ) {}

  @Get()
  check() {
    return this.health.check([
      () => this.db.pingCheck('database'),
    ]);
  }
}
```

## Anti-Patterns

- ❌ Business logic in controllers
- ❌ Direct repository access in controllers
- ❌ No validation on DTOs
- ❌ Circular dependencies between modules
- ❌ Not using dependency injection
