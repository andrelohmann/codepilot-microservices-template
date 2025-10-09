---
description: "Scaffold a Flutter mobile/desktop app"
mode: agent
tools: [file, workspace, terminal]
---

# Scaffold Flutter Frontend Service

Create a production-ready `${input:serviceName}` cross-platform app using Flutter with Riverpod, GoRouter, and clean architecture.

## Reference Documentation

- **[Flutter Tech-Stack Guide](../../../../docs/tech-stacks/frontends/flutter.md)** - Complete implementation details
- **[Flutter Instructions](../instructions/service-frontend-flutter.instructions.md)** - Copilot guidance

## Service Configuration

Ask the user for:
- **Service Name**: `${input:serviceName}` (e.g., `frontend-flutter-mobile`, `frontend-flutter-pos`)
- **Purpose**: `${input:servicePurpose}`
- **Platform**: `${input:platform:mobile}` (mobile, web, desktop)
- **API URL**: `${input:apiUrl:http://localhost:8000}`

## Project Structure

```
services/${input:serviceName}/
├── lib/
│   ├── main.dart
│   ├── app.dart
│   ├── core/
│   ├── features/
│   │   └── {feature}/
│   │       ├── data/
│   │       ├── domain/
│   │       └── presentation/
│   ├── shared/
│   └── router/
├── pubspec.yaml
└── README.md
```

## Implementation Steps

1. Initialize Flutter project
2. Configure Riverpod for state management
3. Set up GoRouter for navigation
4. Configure Dio for HTTP
5. Implement feature-based architecture
6. Create Freezed models
7. Set up flutter_secure_storage
8. Configure theme (light/dark mode)
9. Implement authentication flow
10. Write widget tests
11. Create Dockerfile for web
12. Update docker-compose.yml

Refer to [tech-stack guide](../../../../docs/tech-stacks/frontends/flutter.md) for detailed templates.

## Completion Checklist

- [ ] Flutter project initialized
- [ ] Riverpod configured
- [ ] GoRouter set up
- [ ] Dio HTTP client configured
- [ ] Feature modules created
- [ ] Authentication implemented
- [ ] Tests passing
- [ ] Builds successfully

---

**Note**: Refer to [complete documentation](../../../../docs/tech-stacks/frontends/flutter.md) for implementation details.
