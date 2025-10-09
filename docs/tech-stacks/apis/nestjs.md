# NestJS - Node.js/TypeScript API Services

Enterprise-grade Node.js framework for building scalable, maintainable server-side applications.

## Overview

NestJS is a progressive Node.js framework for building efficient, reliable, and scalable server-side applications with TypeScript.

**Perfect for:**
- Enterprise APIs
- Microservices architecture
- Complex business logic
- GraphQL APIs
- TypeScript-first development

---

## Naming Convention

```
api-nestjs-{purpose}
```

### Examples

```
api-nestjs-payments       # Payment processing
api-nestjs-notifications  # Notification service
api-nestjs-catalog        # Product catalog
api-nestjs-orders         # Order management
api-nestjs-auth           # Authentication service
```

---

## Technology Stack

### Core Technologies

- **Node.js** 20+
- **TypeScript** 5+
- **NestJS** 10+
- **TypeORM** or **Prisma** (ORM)
- **class-validator** (validation)
- **Passport.js** (authentication)

### Key Dependencies

**Production:**
- `@nestjs/core` - Core framework
- `@nestjs/common` - Common utilities
- `@nestjs/platform-express` - Express adapter
- `@nestjs/typeorm` - TypeORM integration
- `@nestjs/passport` - Authentication
- `@nestjs/jwt` - JWT tokens
- `@nestjs/swagger` - API documentation
- `typeorm` - ORM
- `class-validator` - Validation
- `class-transformer` - Transformation

**Development:**
- `@nestjs/cli` - CLI tool
- `@nestjs/testing` - Testing utilities
- `jest` - Testing framework
- `supertest` - HTTP testing
- `@types/*` - TypeScript types

---

## Key Features

### 1. Modular Architecture

```typescript
@Module({
  imports: [TypeOrmModule.forFeature([User])],
  controllers: [UsersController],
  providers: [UsersService],
  exports: [UsersService],
})
export class UsersModule {}
```

### 2. Dependency Injection

```typescript
@Injectable()
export class UsersService {
  constructor(
    @InjectRepository(User)
    private usersRepository: Repository<User>,
    private readonly authService: AuthService,
  ) {}

  async create(createUserDto: CreateUserDto): Promise<User> {
    const user = this.usersRepository.create(createUserDto);
    return this.usersRepository.save(user);
  }
}
```

### 3. Decorators and Metadata

```typescript
@Controller('users')
@UseGuards(JwtAuthGuard)
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Get()
  @ApiOperation({ summary: 'Get all users' })
  @ApiResponse({ status: 200, description: 'List of users', type: [User] })
  async findAll(): Promise<User[]> {
    return this.usersService.findAll();
  }

  @Post()
  @UsePipes(new ValidationPipe())
  async create(@Body() createUserDto: CreateUserDto): Promise<User> {
    return this.usersService.create(createUserDto);
  }
}
```

### 4. GraphQL Support

```typescript
@Resolver(() => User)
export class UsersResolver {
  constructor(private readonly usersService: UsersService) {}

  @Query(() => [User])
  async users(): Promise<User[]> {
    return this.usersService.findAll();
  }

  @Mutation(() => User)
  async createUser(@Args('input') input: CreateUserInput): Promise<User> {
    return this.usersService.create(input);
  }
}
```

### 5. Microservices Patterns

```typescript
@Controller()
export class OrdersController {
  constructor(
    @Inject('PAYMENT_SERVICE')
    private readonly paymentClient: ClientProxy,
  ) {}

  @Post()
  async createOrder(@Body() order: CreateOrderDto) {
    // Send message to payment microservice
    return this.paymentClient.send('create_payment', order).toPromise();
  }
}
```

---

## Ideal Use Cases

### 1. Enterprise APIs ⭐⭐⭐

**Why NestJS:**
- Scalable architecture
- Built-in testing support
- Excellent code organization
- Easy to maintain large codebases

**Example:**
```typescript
// Clean, modular structure
@Module({
  imports: [
    AuthModule,
    UsersModule,
    OrdersModule,
    PaymentsModule,
    NotificationsModule,
  ],
})
export class AppModule {}
```

### 2. Microservices ⭐⭐⭐

**Why NestJS:**
- Built-in microservices support
- Multiple transport layers (TCP, Redis, NATS, RabbitMQ, Kafka)
- Service discovery patterns
- Message-based communication

**Example:**
```typescript
// Microservice setup
const app = await NestFactory.createMicroservice<MicroserviceOptions>(
  AppModule,
  {
    transport: Transport.KAFKA,
    options: {
      client: {
        brokers: ['localhost:9092'],
      },
    },
  },
);
```

### 3. Complex Business Logic ⭐⭐⭐

**Why NestJS:**
- Dependency injection for clean architecture
- Service layers for business logic
- Easy to test
- SOLID principles encouraged

**Example:**
```typescript
@Injectable()
export class OrderProcessingService {
  constructor(
    private readonly ordersService: OrdersService,
    private readonly paymentsService: PaymentsService,
    private readonly inventoryService: InventoryService,
    private readonly notificationsService: NotificationsService,
  ) {}

  async processOrder(order: Order): Promise<ProcessedOrder> {
    // Complex business logic with multiple services
    await this.inventoryService.reserve(order.items);
    const payment = await this.paymentsService.charge(order.total);
    await this.ordersService.confirm(order.id);
    await this.notificationsService.sendConfirmation(order.customerId);
    return { order, payment };
  }
}
```

### 4. GraphQL APIs ⭐⭐⭐

**Why NestJS:**
- First-class GraphQL support
- Code-first or schema-first approaches
- Built-in DataLoader support
- Subscriptions for real-time data

---

## Project Structure

```
services/api-nestjs-{purpose}/
├── src/
│   ├── main.ts                    # Application entry point
│   ├── app.module.ts              # Root module
│   ├── auth/
│   │   ├── auth.module.ts
│   │   ├── auth.controller.ts
│   │   ├── auth.service.ts
│   │   ├── guards/
│   │   │   └── jwt-auth.guard.ts
│   │   └── strategies/
│   │       └── jwt.strategy.ts
│   ├── users/
│   │   ├── users.module.ts
│   │   ├── users.controller.ts
│   │   ├── users.service.ts
│   │   ├── dto/
│   │   │   ├── create-user.dto.ts
│   │   │   └── update-user.dto.ts
│   │   └── entities/
│   │       └── user.entity.ts
│   ├── common/
│   │   ├── filters/
│   │   ├── guards/
│   │   ├── interceptors/
│   │   ├── pipes/
│   │   └── decorators/
│   └── config/
│       └── configuration.ts
├── test/
│   ├── app.e2e-spec.ts
│   └── jest-e2e.json
├── Dockerfile
├── package.json
├── tsconfig.json
├── nest-cli.json
├── .env.example
└── README.md
```

---

## Testing

### Unit Tests

```typescript
describe('UsersService', () => {
  let service: UsersService;
  let repository: Repository<User>;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        UsersService,
        {
          provide: getRepositoryToken(User),
          useClass: Repository,
        },
      ],
    }).compile();

    service = module.get<UsersService>(UsersService);
    repository = module.get<Repository<User>>(getRepositoryToken(User));
  });

  it('should create a user', async () => {
    const dto = { email: 'test@example.com', password: 'password' };
    jest.spyOn(repository, 'create').mockReturnValue(dto as User);
    jest.spyOn(repository, 'save').mockResolvedValue(dto as User);

    expect(await service.create(dto)).toEqual(dto);
  });
});
```

### E2E Tests

```typescript
describe('Users (e2e)', () => {
  let app: INestApplication;

  beforeEach(async () => {
    const moduleFixture: TestingModule = await Test.createTestingModule({
      imports: [AppModule],
    }).compile();

    app = moduleFixture.createNestApplication();
    await app.init();
  });

  it('/users (GET)', () => {
    return request(app.getHttpServer())
      .get('/users')
      .expect(200)
      .expect('Content-Type', /json/);
  });
});
```

### Test Frameworks

- **Jest** - Testing framework
- **supertest** - HTTP testing
- **@nestjs/testing** - NestJS test utilities

### Run Tests

```bash
# Unit tests
npm run test

# E2E tests
npm run test:e2e

# Test coverage
npm run test:cov

# Watch mode
npm run test:watch
```

---

## Deployment

### Dockerfile

```dockerfile
# Multi-stage build
FROM node:20-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY --from=builder /app/dist ./dist

EXPOSE 3000

# Non-root user
RUN addgroup -g 1000 apiuser && adduser -D -u 1000 -G apiuser apiuser
USER apiuser

CMD ["node", "dist/main"]
```

### Environment Variables

```bash
# .env.example
NODE_ENV=production
PORT=3000

# Database
DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_USER=user
DATABASE_PASSWORD=password
DATABASE_NAME=dbname

# JWT
JWT_SECRET=your-secret-key
JWT_EXPIRATION=3600

# Redis (optional)
REDIS_HOST=localhost
REDIS_PORT=6379
```

---

## Best Practices

### 1. Use DTOs for Validation

```typescript
import { IsEmail, IsString, MinLength } from 'class-validator';

export class CreateUserDto {
  @IsEmail()
  email: string;

  @IsString()
  @MinLength(8)
  password: string;
}
```

### 2. Separate Entities and DTOs

```typescript
// Entity (database)
@Entity()
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column({ unique: true })
  email: string;

  @Column()
  password: string;
}

// DTO (API contract)
export class UserResponseDto {
  id: number;
  email: string;
  // No password!
}
```

### 3. Use Guards for Authorization

```typescript
@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.get<string[]>('roles', context.getHandler());
    const { user } = context.switchToHttp().getRequest();
    return requiredRoles.some((role) => user.roles?.includes(role));
  }
}

@Controller('admin')
@UseGuards(RolesGuard)
export class AdminController {
  @Get()
  @Roles('admin')
  getData() {
    return { data: 'sensitive' };
  }
}
```

---

## Common Pitfalls

### ❌ Not Using Module Organization

```typescript
// Wrong - everything in one module
@Module({
  imports: [],
  controllers: [UsersController, OrdersController, PaymentsController],
  providers: [UsersService, OrdersService, PaymentsService],
})
export class AppModule {}

// Correct - modular structure
@Module({
  imports: [UsersModule, OrdersModule, PaymentsModule],
})
export class AppModule {}
```

### ❌ Circular Dependencies

```typescript
// Wrong
// users.service.ts
@Injectable()
export class UsersService {
  constructor(private ordersService: OrdersService) {}
}

// orders.service.ts
@Injectable()
export class OrdersService {
  constructor(private usersService: UsersService) {}
}

// Correct - use forwardRef or refactor
@Injectable()
export class UsersService {
  constructor(
    @Inject(forwardRef(() => OrdersService))
    private ordersService: OrdersService,
  ) {}
}
```

---

## Related Files

- **Instruction File:** `templates/.github/instructions/service-api-nestjs.instructions.md`
- **Scaffold Prompt:** `templates/.github/prompts/scaffold-api-nestjs-service.md`

---

## See Also

- **[API Services Overview](./README.md)** - Compare with other API technologies
- **[FastAPI](./fastapi.md)** - Python alternative
- **[Fiber](./fiber.md)** - Go alternative
- **[Workers - BullMQ](../workers/bullmq.md)** - Background jobs with NestJS

---

**Last Updated:** 2025-10-09  
**Template Version:** 1.0.0
