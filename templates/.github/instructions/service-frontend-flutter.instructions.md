---
applyTo: "**/frontend-flutter-*/**"
description: Flutter cross-platform mobile/desktop applications with Dart, widgets, and state management
---

# Flutter Frontend Service Instructions

Apply to cross-platform mobile/desktop applications using Flutter.

**Reference**: [Complete Flutter Tech Stack Documentation](../../../docs/tech-stacks/frontends/flutter.md)  
**Scaffold**: Use the `scaffold-frontend-flutter-service.prompt.md` for new services

## Project Structure

```
services/frontend-flutter-{purpose}/
├── lib/
│   ├── main.dart
│   ├── app.dart
│   ├── core/           # Config, theme, utils
│   ├── features/       # Feature-based modules
│   │   └── {feature}/
│   │       ├── data/
│   │       ├── domain/
│   │       └── presentation/
│   ├── shared/         # Shared widgets, models
│   └── router/
└── pubspec.yaml
```

## Quick Reference Patterns

### 1. Riverpod State Management

```dart
// Provider
final userProvider = StateNotifierProvider<UserNotifier, User?>((ref) {
  return UserNotifier(ref.read(apiProvider));
});

class UserNotifier extends StateNotifier<User?> {
  UserNotifier(this.api) : super(null);
  final ApiClient api;
  
  Future<void> login(String email, String password) async {
    state = await api.login(email, password);
  }
}

// Consumer widget
class UserProfile extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final user = ref.watch(userProvider);
    return Text(user?.name ?? 'Loading...');
  }
}
```

### 2. GoRouter Navigation

```dart
final router = GoRouter(
  routes: [
    GoRoute(
      path: '/',
      builder: (context, state) => HomePage(),
    ),
    GoRoute(
      path: '/profile/:id',
      builder: (context, state) => ProfilePage(id: state.params['id']!),
    ),
  ],
  redirect: (context, state) {
    final isLoggedIn = /* check auth */;
    if (!isLoggedIn && state.location != '/login') {
      return '/login';
    }
    return null;
  },
);
```

### 3. Dio HTTP Client

```dart
final dio = Dio(
  BaseOptions(baseUrl: 'https://api.example.com'),
);

dio.interceptors.add(InterceptorsWrapper(
  onRequest: (options, handler) {
    options.headers['Authorization'] = 'Bearer $token';
    return handler.next(options);
  },
  onError: (error, handler) {
    if (error.response?.statusCode == 401) {
      // Handle token refresh
    }
    return handler.next(error);
  },
));
```

### 4. Feature Module Structure

```dart
// data/user_repository.dart
class UserRepository {
  final ApiClient api;
  UserRepository(this.api);
  
  Future<User> getUser(String id) => api.get('/users/$id');
}

// domain/user.dart
@freezed
class User with _$User {
  factory User({
    required String id,
    required String name,
  }) = _User;
  
  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
}

// presentation/user_screen.dart
class UserScreen extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final userAsync = ref.watch(userProvider);
    return userAsync.when(
      data: (user) => Text(user.name),
      loading: () => CircularProgressIndicator(),
      error: (err, stack) => Text('Error: $err'),
    );
  }
}
```

## Code Style

- Feature-based architecture (data/domain/presentation)
- Riverpod for state management (avoid StatefulWidget for business logic)
- GoRouter for navigation with named routes
- Dio with interceptors for HTTP
- Repository pattern for data access
- Freezed for immutable models
- flutter_secure_storage for tokens
- Use const constructors everywhere possible
- ListView.builder for long lists
- Handle loading/error states with `.when()`

## Anti-Patterns

❌ Business logic in widgets  
❌ Not disposing controllers  
❌ Overusing `setState()`  
❌ Large widget trees without splitting  
❌ Synchronous operations on main thread  
❌ Not handling async errors  
❌ Hardcoded strings (use localization)  
❌ Not testing platform differences
