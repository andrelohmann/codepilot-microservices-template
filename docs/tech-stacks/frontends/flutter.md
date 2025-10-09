# Flutter - Cross-Platform Mobile & Desktop Framework

Build native applications for iOS, Android, Web, and Desktop from a single codebase using Dart and Flutter.

---

## Overview

**Naming Convention:** `frontend-flutter-{purpose}`

**Examples:**
- `frontend-flutter-mobile` - Mobile app (iOS + Android)
- `frontend-flutter-kiosk` - Kiosk application
- `frontend-flutter-tablet` - Tablet interface

**Flutter** enables you to build beautiful, native applications across mobile, web, and desktop platforms with a single codebase.

---

## Technology Stack

- **Flutter 3.16+** - UI framework
- **Dart 3.2+** - Programming language
- **Riverpod 2.x** - State management
- **GoRouter** - Navigation
- **Dio** - HTTP client
- **Hive** - Local storage
- **Flutter Hooks** - Reusable widget logic

---

## Features

- ✅ **Cross-platform** - iOS, Android, Web, Desktop
- ✅ **Single codebase** - Write once, run everywhere
- ✅ **Native performance** - Compiled to native code
- ✅ **Material Design 3** - Beautiful UI components
- ✅ **Hot reload** - Instant UI updates
- ✅ **Custom animations** - Smooth, fluid animations

---

## Ideal Use Cases

✅ **Mobile Apps** - iOS and Android applications  
✅ **Tablet Applications** - Optimized for tablets  
✅ **Kiosk Interfaces** - Full-screen interactive displays  
✅ **Desktop Apps** - Windows, macOS, Linux  
✅ **Embedded UIs** - IoT and embedded systems

❌ **Not Ideal For:**
- Web-only applications → Use React/Next.js
- Admin dashboards → Use React SPA
- Static websites → Use SSG

---

## Project Structure

```
frontend-flutter-{purpose}/
├── lib/
│   ├── main.dart
│   ├── app/
│   │   ├── router.dart       # GoRouter setup
│   │   └── theme.dart        # App theme
│   ├── features/
│   │   ├── auth/
│   │   │   ├── screens/
│   │   │   ├── widgets/
│   │   │   └── providers/
│   │   └── home/
│   ├── shared/
│   │   ├── widgets/
│   │   ├── models/
│   │   └── utils/
│   └── services/
│       ├── api_service.dart
│       └── storage_service.dart
├── test/
├── assets/
│   ├── images/
│   └── fonts/
├── pubspec.yaml
└── README.md
```

---

## Code Examples

### Main Entry Point

```dart
// lib/main.dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'app/router.dart';
import 'app/theme.dart';

void main() {
  runApp(const ProviderScope(child: MyApp()));
}

class MyApp extends ConsumerWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final router = ref.watch(routerProvider);
    
    return MaterialApp.router(
      title: 'My App',
      theme: AppTheme.lightTheme,
      darkTheme: AppTheme.darkTheme,
      routerConfig: router,
    );
  }
}
```

### Router Setup (GoRouter)

```dart
// lib/app/router.dart
import 'package:go_router/go_router.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';

final routerProvider = Provider<GoRouter>((ref) {
  return GoRouter(
    initialLocation: '/',
    routes: [
      GoRoute(
        path: '/',
        builder: (context, state) => const HomeScreen(),
      ),
      GoRoute(
        path: '/login',
        builder: (context, state) => const LoginScreen(),
      ),
      GoRoute(
        path: '/profile/:id',
        builder: (context, state) {
          final id = state.pathParameters['id']!;
          return ProfileScreen(userId: id);
        },
      ),
    ],
  );
});
```

### API Service (Dio)

```dart
// lib/services/api_service.dart
import 'package:dio/dio.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';

final dioProvider = Provider<Dio>((ref) {
  final dio = Dio(BaseOptions(
    baseUrl: 'https://api.example.com',
    connectTimeout: const Duration(seconds: 5),
    receiveTimeout: const Duration(seconds: 3),
  ));

  dio.interceptors.add(InterceptorsWrapper(
    onRequest: (options, handler) {
      // Add auth token
      final token = ref.read(authTokenProvider);
      if (token != null) {
        options.headers['Authorization'] = 'Bearer $token';
      }
      handler.next(options);
    },
  ));

  return dio;
});

final apiServiceProvider = Provider<ApiService>((ref) {
  return ApiService(ref.watch(dioProvider));
});

class ApiService {
  final Dio _dio;

  ApiService(this._dio);

  Future<List<User>> getUsers() async {
    final response = await _dio.get('/users');
    return (response.data as List)
        .map((json) => User.fromJson(json))
        .toList();
  }
}
```

### State Management (Riverpod)

```dart
// lib/features/auth/providers/auth_provider.dart
import 'package:flutter_riverpod/flutter_riverpod.dart';

final authStateProvider = StateNotifierProvider<AuthNotifier, AuthState>((ref) {
  return AuthNotifier(ref.watch(apiServiceProvider));
});

class AuthNotifier extends StateNotifier<AuthState> {
  final ApiService _api;

  AuthNotifier(this._api) : super(AuthState.initial());

  Future<void> login(String email, String password) async {
    state = AuthState.loading();
    try {
      final user = await _api.login(email, password);
      state = AuthState.authenticated(user);
    } catch (e) {
      state = AuthState.error(e.toString());
    }
  }

  void logout() {
    state = AuthState.initial();
  }
}

class AuthState {
  final User? user;
  final bool isLoading;
  final String? error;

  AuthState({this.user, this.isLoading = false, this.error});

  factory AuthState.initial() => AuthState();
  factory AuthState.loading() => AuthState(isLoading: true);
  factory AuthState.authenticated(User user) => AuthState(user: user);
  factory AuthState.error(String error) => AuthState(error: error);
}
```

### Screen Example

```dart
// lib/features/home/screens/home_screen.dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';

class HomeScreen extends ConsumerWidget {
  const HomeScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final users = ref.watch(usersProvider);

    return Scaffold(
      appBar: AppBar(
        title: const Text('Users'),
      ),
      body: users.when(
        data: (userList) => ListView.builder(
          itemCount: userList.length,
          itemBuilder: (context, index) {
            final user = userList[index];
            return ListTile(
              leading: CircleAvatar(child: Text(user.name[0])),
              title: Text(user.name),
              subtitle: Text(user.email),
              onTap: () => context.go('/profile/${user.id}'),
            );
          },
        ),
        loading: () => const Center(child: CircularProgressIndicator()),
        error: (error, stack) => Center(child: Text('Error: $error')),
      ),
    );
  }
}
```

### Local Storage (Hive)

```dart
// lib/services/storage_service.dart
import 'package:hive_flutter/hive_flutter.dart';

class StorageService {
  static Future<void> init() async {
    await Hive.initFlutter();
    await Hive.openBox('settings');
  }

  static Box get _box => Hive.box('settings');

  static Future<void> saveToken(String token) async {
    await _box.put('auth_token', token);
  }

  static String? getToken() {
    return _box.get('auth_token');
  }

  static Future<void> clearToken() async {
    await _box.delete('auth_token');
  }
}
```

---

## Testing

```dart
// test/features/auth/auth_notifier_test.dart
import 'package:flutter_test/flutter_test.dart';
import 'package:mockito/mockito.dart';

void main() {
  group('AuthNotifier', () {
    late AuthNotifier notifier;
    late MockApiService mockApi;

    setUp(() {
      mockApi = MockApiService();
      notifier = AuthNotifier(mockApi);
    });

    test('login success updates state', () async {
      final user = User(id: '1', name: 'Test User');
      when(mockApi.login(any, any)).thenAnswer((_) async => user);

      await notifier.login('test@example.com', 'password');

      expect(notifier.state.user, equals(user));
      expect(notifier.state.isLoading, isFalse);
    });
  });
}
```

---

## Deployment

### Android

```bash
flutter build apk --release
flutter build appbundle --release
```

### iOS

```bash
flutter build ios --release
# Open in Xcode and archive
```

### Web

```bash
flutter build web --release
# Deploy dist/ to static hosting
```

---

## Best Practices

✅ Use Riverpod for state management  
✅ Implement proper error handling  
✅ Use const constructors everywhere possible  
✅ Implement proper loading states  
✅ Follow Material Design guidelines  
✅ Use GoRouter for navigation  
✅ Implement offline-first with Hive  
✅ Write widget tests

---

## Related Documentation

- [Frontends Overview](./README.md)
- [React SPA](./react.md) - For web apps
- [API Technologies](../apis/README.md)

---

**Reference Files:**
- Instruction: `service-frontend-flutter.instructions.md`
- Scaffold: `scaffold-frontend-flutter-service.md`
