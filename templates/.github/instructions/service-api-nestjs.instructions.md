---
applyTo: "**/api-nestjs-*/**"
description: NestJS API service with TypeScript, TypeORM, and dependency injection
---

# NestJS API Service - Copilot Guidance

Quick reference for GitHub Copilot when working with NestJS API services.

## Reference Documentation

- **[Complete NestJS Tech-Stack Guide](../../../docs/tech-stacks/apis/nestjs.md)** - Full implementation details, examples, and best practices
- **[Scaffold Prompt](../prompts/scaffold-api-nestjs-service.prompt.md)** - Generate new NestJS services

## Naming Convention

`api-nestjs-{purpose}` (e.g., `api-nestjs-billing`, `api-nestjs-payments`)

## Project Structure

```
services/api-nestjs-{purpose}/
├── src/
│   ├── main.ts                    # Application bootstrap
│   ├── app.module.ts              # Root module
│   ├── config/                    # Configuration files
│   ├── modules/                   # Feature modules
│   │   ├── users/                 # Example: Users module
│   │   │   ├── users.module.ts
│   │   │   ├── users.controller.ts
│   │   │   ├── users.service.ts
│   │   │   ├── entities/
│   │   │   └── dto/
│   │   └── auth/                  # Example: Auth module
│   ├── common/                    # Shared code
│   │   ├── filters/
│   │   ├── guards/
│   │   ├── interceptors/
│   │   └── decorators/
│   └── database/
│       └── migrations/
├── test/
├── Dockerfile
├── package.json
└── tsconfig.json
```

## Copilot Coding Guidance

### Use These Patterns

1. **Module-Driven Architecture** - One module per feature/domain
2. **Dependency Injection** - Always use constructor injection
3. **DTOs with Validation** - Use class-validator decorators
4. **TypeORM Entities** - Decorate with TypeORM decorators
5. **Swagger Documentation** - Add @Api decorators to controllers
6. **Layered Structure** - Controller → Service → Repository

### Quick Patterns Reference

**Module Structure**:
```typescript
@Module({
  imports: [TypeOrmModule.forFeature([User])],
  controllers: [UsersController],
  providers: [UsersService],
  exports: [UsersService],
})
export class UsersModule {}
```

**Controller with Validation**:
```typescript
@Controller('users')
@ApiTags('users')
export class UsersController {
  constructor(private usersService: UsersService) {}

  @Post()
  @ApiOperation({ summary: 'Create user' })
  create(@Body() dto: CreateUserDto) {
    return this.usersService.create(dto);
  }
}
```

**DTO with Validation**:
```typescript
export class CreateUserDto {
  @IsEmail()
  @ApiProperty()
  email: string;

  @IsString()
  @MinLength(8)
  @ApiProperty()
  password: string;
}
```

**TypeORM Entity**:
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

**Injectable Service**:
```typescript
@Injectable()
export class UsersService {
  constructor(
    @InjectRepository(User)
    private usersRepository: Repository<User>,
  ) {}

  async findOne(id: string): Promise<User> {
    return this.usersRepository.findOneBy({ id });
  }
}
```

### Code Style

- **Naming**: PascalCase for classes, camelCase for methods/properties
- **Decorators**: Use NestJS decorators (@Injectable, @Controller, etc.)
- **Imports**: Group by external, @nestjs, local
- **Dependency Injection**: Constructor injection only
- **Testing**: One describe block per class, nest contexts

### Anti-Patterns to Avoid

❌ Business logic in controllers  
❌ Direct repository access in controllers  
❌ Missing validation on DTOs  
❌ Circular module dependencies  
❌ Not using dependency injection  
❌ Synchronous operations in async context  

For detailed examples, deployment guides, testing strategies, and advanced patterns, refer to the [NestJS Tech-Stack Documentation](../../../docs/tech-stacks/apis/nestjs.md).

