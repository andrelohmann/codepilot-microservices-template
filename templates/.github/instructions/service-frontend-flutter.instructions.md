---
applyTo:
  - "services/frontend-flutter-*/**"
---

# Flutter Frontend Service Instructions

Apply to cross-platform mobile/desktop applications using Flutter.

## Technology Stack

- **Framework:** Flutter 3.16+
- **Language:** Dart 3.2+
- **State Management:** Riverpod 2.x
- **Routing:** GoRouter
- **HTTP Client:** dio
- **Local Storage:** shared_preferences / Hive
- **DI:** Riverpod providers

## Project Structure

```
services/frontend-flutter-{purpose}/
├── lib/
│   ├── main.dart
│   ├── app.dart
│   ├── core/
│   │   ├── config/
│   │   ├── constants/
│   │   ├── theme/
│   │   └── utils/
│   ├── features/
│   │   └── {feature}/
│   │       ├── data/
│   │       ├── domain/
│   │       └── presentation/
│   ├── shared/
│   │   ├── widgets/
│   │   ├── models/
│   │   └── providers/
│   └── router/
├── test/
├── pubspec.yaml
├── Dockerfile (for web)
└── README.md
```

## Core Patterns

**Feature-Based Architecture:**
- Group by feature: `auth/`, `users/`, `dashboard/`
- Each feature: `data/`, `domain/`, `presentation/`
- Data layer: API clients, DTOs, repositories
- Domain layer: Models, use cases
- Presentation layer: Screens, widgets, providers

**State Management (Riverpod):**
- Use `StateNotifierProvider` for complex state
- Use `FutureProvider` for async data fetching
- Use `StreamProvider` for real-time updates
- Consumer widgets: `Consumer`, `ConsumerWidget`
- Avoid `StatefulWidget` for business logic

**Routing (GoRouter):**
- Define routes in `router/app_router.dart`
- Use named routes with parameters
- Guard routes with `redirect` for auth
- Deep linking support for mobile
- Browser URL updates for web

**HTTP Client (dio):**
- Single dio instance with interceptors
- Request interceptor for auth tokens
- Response interceptor for error handling
- Retry logic for failed requests
- Base URL from environment config

**Error Handling:**
- Custom exception classes: `ApiException`, `NetworkException`
- Display user-friendly error messages
- Log errors with stack traces
- Retry mechanisms for network failures
- Graceful degradation for offline mode

**Form Validation:**
- Use `flutter_form_builder` or custom validators
- Real-time validation feedback
- FormKey for validation state
- Separate validation logic from UI
- Consistent error message format

**Theming:**
- Material Design 3 theme
- Light/dark mode support
- Theme provider for runtime switching
- Custom color schemes
- Consistent spacing and typography

**API Integration:**
- Repository pattern for data access
- DTOs for API responses
- Model mapping in data layer
- Cache responses when appropriate
- Handle pagination with infinite scroll

## Responsive Design

- Use `LayoutBuilder` for responsive layouts
- Breakpoints: mobile (<600), tablet (600-1200), desktop (>1200)
- `MediaQuery` for device info
- Adaptive widgets for platform differences
- Test on multiple screen sizes

## Testing Standards

- Unit tests for providers and models
- Widget tests for UI components
- Integration tests for critical flows
- Mock HTTP responses with `mockito`
- Test coverage target: >80%

## Performance Optimizations

- Use `const` constructors where possible
- Implement `ListView.builder` for long lists
- Image caching with `cached_network_image`
- Lazy loading for heavy widgets
- Profile with DevTools for bottlenecks

## Security Requirements

- Secure storage for tokens (flutter_secure_storage)
- HTTPS only for API calls
- Certificate pinning for sensitive apps
- Biometric authentication support
- Input sanitization

## Platform-Specific

**Mobile:**
- Handle app lifecycle events
- Background fetch configuration
- Push notifications setup
- Deep links configuration
- App permissions handling

**Web:**
- PWA support configuration
- SEO meta tags
- Service worker for offline
- Browser compatibility
- Responsive breakpoints

**Desktop:**
- Window size constraints
- Keyboard shortcuts
- Native menu integration
- File system access

## Anti-Patterns

❌ Business logic in widgets  
❌ Not disposing controllers  
❌ Overusing `setState()`  
❌ Large widget trees without splitting  
❌ Synchronous operations on main thread  
❌ Not handling async errors  
❌ Hardcoded strings (use localization)  
❌ Not testing platform differences  

## Dependencies

Required:
- `flutter_riverpod: ^2.4.0`
- `go_router: ^13.0.0`
- `dio: ^5.4.0`
- `shared_preferences: ^2.2.0`

Common:
- `freezed: ^2.4.0` (immutable models)
- `flutter_secure_storage: ^9.0.0` (secure storage)
- `cached_network_image: ^3.3.0` (image caching)
- `flutter_localizations` (i18n)

## Configuration

Environment variables via `--dart-define`:
- `API_URL` - Backend API endpoint
- `ENV` - Environment (dev/staging/prod)
- `SENTRY_DSN` - Error tracking (optional)

## Build Targets

- Development: `flutter run --dart-define=ENV=dev`
- Staging: `flutter build apk --dart-define=ENV=staging`
- Production: `flutter build apk --dart-define=ENV=prod --release`

## Code Generation

- Run `flutter pub run build_runner build` for:
  - Freezed models
  - JSON serialization
  - Riverpod code generation
