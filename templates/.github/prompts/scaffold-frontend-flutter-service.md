# Scaffold Flutter Application

**Objective:** Create a production-ready Flutter application with complete directory structure, state management, routing, API integration, and cross-platform support.

---

## 📋 Application Specification

Before running this prompt, gather the following information:

1. **Service Name:** `[e.g., frontend-flutter-mobile, frontend-flutter-dashboard]`
2. **App Purpose:** `[Brief description of the application]`
3. **Target Platforms:** `[iOS, Android, Web, Desktop - select all that apply]`
4. **Backend API:** `[Base URL of the backend API]`
5. **Authentication:** `[Yes/No - JWT/OAuth]`
6. **Offline Support:** `[Yes/No - local storage/cache]`
7. **Key Features:** `[List main features]`

---

## 🏗️ Architecture Overview

```
┌────────────────────────────────────────────────────────────────────┐
│                   Flutter Application Architecture                 │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                   Presentation Layer                         │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │   Pages    │  │   Widgets  │  │   Themes   │            │ │
│  │  │  (Screens) │  │(Components)│  │  (Styles)  │            │ │
│  │  └──────┬─────┘  └────────────┘  └────────────┘            │ │
│  └─────────┼────────────────────────────────────────────────────┘ │
│            │                                                      │
│            ↓                                                      │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                 Application Layer (Riverpod)                 │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │  Providers │  │   State    │  │Controllers │            │ │
│  │  │(DI/State)  │  │  Notifiers │  │            │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
│            ↓                ↓                ↓                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                      Domain Layer                            │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │   Models   │  │Repositories│  │Use Cases   │            │ │
│  │  │ (Entities) │  │(Interfaces)│  │  (Logic)   │            │ │
│  │  └────────────┘  └──────┬─────┘  └────────────┘            │ │
│  └──────────────────────────┼──────────────────────────────────── │
│                             │                                     │
│            ┌────────────────┴────────────────┐                   │
│            ↓                                 ↓                   │
│  ┌──────────────────────┐        ┌──────────────────────┐       │
│  │      Data Layer      │        │   Local Storage      │       │
│  │  ┌────────────────┐  │        │  ┌────────────────┐  │       │
│  │  │ API Client     │  │        │  │  SharedPrefs   │  │       │
│  │  │  (Dio/HTTP)    │  │        │  │  Hive/SQLite   │  │       │
│  │  └────────────────┘  │        │  └────────────────┘  │       │
│  └──────────┬───────────┘        └──────────┬───────────┘       │
│             │                               │                   │
└─────────────┼───────────────────────────────┼───────────────────┘
              │                               │
              ↓                               ↓
      ┌──────────────┐              ┌──────────────┐
      │  Backend API │              │Local Database│
      │  (REST/      │              │  (Device     │
      │   GraphQL)   │              │   Storage)   │
      └──────────────┘              └──────────────┘
```

---

## 📁 Directory Structure

Create the following complete directory structure:

```
services/[service-name]/
├── lib/
│   ├── main.dart                   ← Application entry point
│   │
│   ├── core/                       ← Core utilities
│   │   ├── constants/
│   │   │   ├── api_constants.dart
│   │   │   └── app_constants.dart
│   │   ├── theme/
│   │   │   ├── app_theme.dart
│   │   │   ├── colors.dart
│   │   │   └── text_styles.dart
│   │   ├── router/
│   │   │   └── app_router.dart
│   │   ├── errors/
│   │   │   └── failures.dart
│   │   └── utils/
│   │       ├── validators.dart
│   │       └── formatters.dart
│   │
│   ├── features/                   ← Feature modules
│   │   ├── auth/
│   │   │   ├── data/
│   │   │   │   ├── datasources/
│   │   │   │   │   └── auth_remote_datasource.dart
│   │   │   │   ├── models/
│   │   │   │   │   └── user_model.dart
│   │   │   │   └── repositories/
│   │   │   │       └── auth_repository_impl.dart
│   │   │   ├── domain/
│   │   │   │   ├── entities/
│   │   │   │   │   └── user.dart
│   │   │   │   ├── repositories/
│   │   │   │   │   └── auth_repository.dart
│   │   │   │   └── usecases/
│   │   │   │       ├── login.dart
│   │   │   │       └── logout.dart
│   │   │   └── presentation/
│   │   │       ├── pages/
│   │   │       │   ├── login_page.dart
│   │   │       │   └── register_page.dart
│   │   │       ├── providers/
│   │   │       │   └── auth_provider.dart
│   │   │       └── widgets/
│   │   │           └── login_form.dart
│   │   │
│   │   └── home/
│   │       ├── data/
│   │       ├── domain/
│   │       └── presentation/
│   │           ├── pages/
│   │           │   └── home_page.dart
│   │           └── widgets/
│   │
│   └── shared/                     ← Shared components
│       ├── widgets/
│       │   ├── loading_indicator.dart
│       │   ├── error_widget.dart
│       │   └── custom_button.dart
│       ├── providers/
│       │   └── dio_provider.dart
│       └── models/
│           └── api_response.dart
│
├── test/                           ← Tests
│   ├── unit/
│   ├── widget/
│   └── integration/
│
├── assets/                         ← Assets
│   ├── images/
│   ├── icons/
│   └── fonts/
│
├── .devcontainer/
│   └── [service-name]-container/
│       └── devcontainer.json
│
├── android/                        ← Android specific
├── ios/                            ← iOS specific
├── web/                            ← Web specific
├── linux/                          ← Linux specific
├── macos/                          ← macOS specific
├── windows/                        ← Windows specific
│
├── pubspec.yaml
├── analysis_options.yaml
├── Dockerfile
├── docker-compose.yml
├── .env.example
├── .gitignore
└── README.md
```

---

## 🔨 Implementation Steps

### Step 1: Create pubspec.yaml

```yaml
name: [service_name]
description: [App description]
version: 1.0.0+1

environment:
  sdk: '>=3.2.0 <4.0.0'

dependencies:
  flutter:
    sdk: flutter
  
  # State Management
  flutter_riverpod: ^2.4.9
  riverpod_annotation: ^2.3.3
  
  # Routing
  go_router: ^13.0.0
  
  # Network
  dio: ^5.4.0
  pretty_dio_logger: ^1.3.1
  
  # Local Storage
  shared_preferences: ^2.2.2
  flutter_secure_storage: ^9.0.0
  hive: ^2.2.3
  hive_flutter: ^1.1.0
  
  # JSON Serialization
  freezed_annotation: ^2.4.1
  json_annotation: ^4.8.1
  
  # UI
  flutter_svg: ^2.0.9
  cached_network_image: ^3.3.0
  shimmer: ^3.0.0
  
  # Forms & Validation
  flutter_form_builder: ^9.1.1
  form_builder_validators: ^9.1.0
  
  # Utils
  intl: ^0.18.1
  url_launcher: ^6.2.2
  
  # Development
  flutter_lints: ^3.0.1

dev_dependencies:
  flutter_test:
    sdk: flutter
  
  # Code Generation
  build_runner: ^2.4.7
  freezed: ^2.4.6
  json_serializable: ^6.7.1
  riverpod_generator: ^2.3.9
  
  # Testing
  mockito: ^5.4.4
  integration_test:
    sdk: flutter

flutter:
  uses-material-design: true
  
  assets:
    - assets/images/
    - assets/icons/
  
  fonts:
    - family: Inter
      fonts:
        - asset: assets/fonts/Inter-Regular.ttf
        - asset: assets/fonts/Inter-Bold.ttf
          weight: 700
```

### Step 2: Create lib/main.dart

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:hive_flutter/hive_flutter.dart';

import 'core/router/app_router.dart';
import 'core/theme/app_theme.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  
  // Initialize Hive
  await Hive.initFlutter();
  
  runApp(
    const ProviderScope(
      child: MyApp(),
    ),
  );
}

class MyApp extends ConsumerWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final router = ref.watch(routerProvider);
    
    return MaterialApp.router(
      title: '[App Name]',
      theme: AppTheme.lightTheme,
      darkTheme: AppTheme.darkTheme,
      routerConfig: router,
      debugShowCheckedModeBanner: false,
    );
  }
}
```

### Step 3: Create lib/core/router/app_router.dart

```dart
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';
import 'package:riverpod_annotation/riverpod_annotation.dart';

import '../../features/auth/presentation/pages/login_page.dart';
import '../../features/auth/presentation/providers/auth_provider.dart';
import '../../features/home/presentation/pages/home_page.dart';

part 'app_router.g.dart';

@riverpod
GoRouter router(RouterRef ref) {
  final authState = ref.watch(authProvider);
  
  return GoRouter(
    initialLocation: '/login',
    redirect: (context, state) {
      final isLoggedIn = authState.isAuthenticated;
      final isLoggingIn = state.matchedLocation == '/login';
      
      if (!isLoggedIn && !isLoggingIn) {
        return '/login';
      }
      
      if (isLoggedIn && isLoggingIn) {
        return '/';
      }
      
      return null;
    },
    routes: [
      GoRoute(
        path: '/login',
        builder: (context, state) => const LoginPage(),
      ),
      GoRoute(
        path: '/',
        builder: (context, state) => const HomePage(),
      ),
    ],
  );
}
```

### Step 4: Create lib/core/theme/app_theme.dart

```dart
import 'package:flutter/material.dart';
import 'colors.dart';
import 'text_styles.dart';

class AppTheme {
  static ThemeData get lightTheme {
    return ThemeData(
      useMaterial3: true,
      colorScheme: ColorScheme.fromSeed(
        seedColor: AppColors.primary,
        brightness: Brightness.light,
      ),
      textTheme: AppTextStyles.textTheme,
      elevatedButtonTheme: ElevatedButtonThemeData(
        style: ElevatedButton.styleFrom(
          minimumSize: const Size.fromHeight(48),
          shape: RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(12),
          ),
        ),
      ),
      inputDecorationTheme: InputDecorationTheme(
        filled: true,
        border: OutlineInputBorder(
          borderRadius: BorderRadius.circular(12),
        ),
      ),
    );
  }
  
  static ThemeData get darkTheme {
    return ThemeData(
      useMaterial3: true,
      colorScheme: ColorScheme.fromSeed(
        seedColor: AppColors.primary,
        brightness: Brightness.dark,
      ),
      textTheme: AppTextStyles.textTheme,
    );
  }
}
```

### Step 5: Create lib/core/theme/colors.dart

```dart
import 'package:flutter/material.dart';

class AppColors {
  static const Color primary = Color(0xFF6366F1);
  static const Color secondary = Color(0xFF8B5CF6);
  static const Color error = Color(0xFFEF4444);
  static const Color success = Color(0xFF10B981);
  static const Color warning = Color(0xFFF59E0B);
  
  static const Color textPrimary = Color(0xFF1F2937);
  static const Color textSecondary = Color(0xFF6B7280);
  static const Color background = Color(0xFFF9FAFB);
}
```

### Step 6: Create lib/core/theme/text_styles.dart

```dart
import 'package:flutter/material.dart';

class AppTextStyles {
  static const TextTheme textTheme = TextTheme(
    displayLarge: TextStyle(
      fontSize: 57,
      fontWeight: FontWeight.bold,
    ),
    displayMedium: TextStyle(
      fontSize: 45,
      fontWeight: FontWeight.bold,
    ),
    headlineLarge: TextStyle(
      fontSize: 32,
      fontWeight: FontWeight.bold,
    ),
    headlineMedium: TextStyle(
      fontSize: 28,
      fontWeight: FontWeight.w600,
    ),
    bodyLarge: TextStyle(
      fontSize: 16,
      fontWeight: FontWeight.normal,
    ),
    bodyMedium: TextStyle(
      fontSize: 14,
      fontWeight: FontWeight.normal,
    ),
  );
}
```

### Step 7: Create lib/shared/providers/dio_provider.dart

```dart
import 'package:dio/dio.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:pretty_dio_logger/pretty_dio_logger.dart';

import '../../core/constants/api_constants.dart';

final dioProvider = Provider<Dio>((ref) {
  final dio = Dio(
    BaseOptions(
      baseUrl: ApiConstants.baseUrl,
      connectTimeout: const Duration(seconds: 30),
      receiveTimeout: const Duration(seconds: 30),
      headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json',
      },
    ),
  );
  
  // Add interceptors
  dio.interceptors.add(
    PrettyDioLogger(
      requestHeader: true,
      requestBody: true,
      responseBody: true,
      responseHeader: false,
      error: true,
      compact: true,
    ),
  );
  
  // Add auth interceptor
  dio.interceptors.add(
    InterceptorsWrapper(
      onRequest: (options, handler) async {
        // Add auth token
        final token = ''; // Get from secure storage
        if (token.isNotEmpty) {
          options.headers['Authorization'] = 'Bearer $token';
        }
        return handler.next(options);
      },
      onError: (error, handler) async {
        if (error.response?.statusCode == 401) {
          // Handle unauthorized
        }
        return handler.next(error);
      },
    ),
  );
  
  return dio;
});
```

### Step 8: Create lib/features/auth/domain/entities/user.dart

```dart
import 'package:freezed_annotation/freezed_annotation.dart';

part 'user.freezed.dart';
part 'user.g.dart';

@freezed
class User with _$User {
  const factory User({
    required String id,
    required String email,
    required String name,
    String? avatarUrl,
  }) = _User;
  
  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
}
```

### Step 9: Create lib/features/auth/data/datasources/auth_remote_datasource.dart

```dart
import 'package:dio/dio.dart';
import 'package:riverpod_annotation/riverpod_annotation.dart';

import '../../../../shared/providers/dio_provider.dart';
import '../../domain/entities/user.dart';

part 'auth_remote_datasource.g.dart';

@riverpod
AuthRemoteDataSource authRemoteDataSource(AuthRemoteDataSourceRef ref) {
  return AuthRemoteDataSource(ref.watch(dioProvider));
}

class AuthRemoteDataSource {
  final Dio _dio;
  
  AuthRemoteDataSource(this._dio);
  
  Future<User> login(String email, String password) async {
    try {
      final response = await _dio.post(
        '/auth/login',
        data: {
          'email': email,
          'password': password,
        },
      );
      
      return User.fromJson(response.data['user']);
    } on DioException catch (e) {
      throw _handleError(e);
    }
  }
  
  Future<void> logout() async {
    try {
      await _dio.post('/auth/logout');
    } on DioException catch (e) {
      throw _handleError(e);
    }
  }
  
  Exception _handleError(DioException error) {
    if (error.type == DioExceptionType.connectionTimeout) {
      return Exception('Connection timeout');
    }
    if (error.response?.statusCode == 401) {
      return Exception('Invalid credentials');
    }
    return Exception('An error occurred');
  }
}
```

### Step 10: Create lib/features/auth/presentation/pages/login_page.dart

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:go_router/go_router.dart';

import '../providers/auth_provider.dart';

class LoginPage extends ConsumerStatefulWidget {
  const LoginPage({super.key});

  @override
  ConsumerState<LoginPage> createState() => _LoginPageState();
}

class _LoginPageState extends ConsumerState<LoginPage> {
  final _formKey = GlobalKey<FormState>();
  final _emailController = TextEditingController();
  final _passwordController = TextEditingController();
  
  @override
  void dispose() {
    _emailController.dispose();
    _passwordController.dispose();
    super.dispose();
  }
  
  Future<void> _handleLogin() async {
    if (!_formKey.currentState!.validate()) return;
    
    try {
      await ref.read(authProvider.notifier).login(
        _emailController.text,
        _passwordController.text,
      );
      
      if (mounted) {
        context.go('/');
      }
    } catch (e) {
      if (mounted) {
        ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(content: Text(e.toString())),
        );
      }
    }
  }
  
  @override
  Widget build(BuildContext context) {
    final authState = ref.watch(authProvider);
    
    return Scaffold(
      body: SafeArea(
        child: Center(
          child: SingleChildScrollView(
            padding: const EdgeInsets.all(24),
            child: Form(
              key: _formKey,
              child: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                crossAxisAlignment: CrossAxisAlignment.stretch,
                children: [
                  Text(
                    'Welcome Back',
                    style: Theme.of(context).textTheme.headlineLarge,
                    textAlign: TextAlign.center,
                  ),
                  const SizedBox(height: 48),
                  TextFormField(
                    controller: _emailController,
                    decoration: const InputDecoration(
                      labelText: 'Email',
                      prefixIcon: Icon(Icons.email),
                    ),
                    keyboardType: TextInputType.emailAddress,
                    validator: (value) {
                      if (value == null || value.isEmpty) {
                        return 'Please enter your email';
                      }
                      return null;
                    },
                  ),
                  const SizedBox(height: 16),
                  TextFormField(
                    controller: _passwordController,
                    decoration: const InputDecoration(
                      labelText: 'Password',
                      prefixIcon: Icon(Icons.lock),
                    ),
                    obscureText: true,
                    validator: (value) {
                      if (value == null || value.isEmpty) {
                        return 'Please enter your password';
                      }
                      return null;
                    },
                  ),
                  const SizedBox(height: 24),
                  ElevatedButton(
                    onPressed: authState.isLoading ? null : _handleLogin,
                    child: authState.isLoading
                        ? const CircularProgressIndicator()
                        : const Text('Login'),
                  ),
                ],
              ),
            ),
          ),
        ),
      ),
    );
  }
}
```

### Step 11: Create lib/features/auth/presentation/providers/auth_provider.dart

```dart
import 'package:riverpod_annotation/riverpod_annotation.dart';

import '../../data/datasources/auth_remote_datasource.dart';
import '../../domain/entities/user.dart';

part 'auth_provider.g.dart';

@riverpod
class Auth extends _$Auth {
  @override
  AuthState build() {
    return const AuthState.unauthenticated();
  }
  
  Future<void> login(String email, String password) async {
    state = const AuthState.loading();
    
    try {
      final dataSource = ref.read(authRemoteDataSourceProvider);
      final user = await dataSource.login(email, password);
      
      state = AuthState.authenticated(user);
    } catch (e) {
      state = AuthState.error(e.toString());
      rethrow;
    }
  }
  
  Future<void> logout() async {
    state = const AuthState.loading();
    
    try {
      final dataSource = ref.read(authRemoteDataSourceProvider);
      await dataSource.logout();
      
      state = const AuthState.unauthenticated();
    } catch (e) {
      state = AuthState.error(e.toString());
    }
  }
}

class AuthState {
  final User? user;
  final bool isLoading;
  final String? error;
  
  const AuthState({
    this.user,
    this.isLoading = false,
    this.error,
  });
  
  const AuthState.authenticated(User user)
      : this(user: user, isLoading: false);
  
  const AuthState.unauthenticated()
      : this(user: null, isLoading: false);
  
  const AuthState.loading()
      : this(isLoading: true);
  
  const AuthState.error(String error)
      : this(error: error, isLoading: false);
  
  bool get isAuthenticated => user != null;
}
```

### Step 12: Create lib/features/home/presentation/pages/home_page.dart

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';

import '../../../auth/presentation/providers/auth_provider.dart';

class HomePage extends ConsumerWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final authState = ref.watch(authProvider);
    final user = authState.user;
    
    return Scaffold(
      appBar: AppBar(
        title: const Text('Home'),
        actions: [
          IconButton(
            icon: const Icon(Icons.logout),
            onPressed: () => ref.read(authProvider.notifier).logout(),
          ),
        ],
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(
              'Welcome, ${user?.name ?? "User"}!',
              style: Theme.of(context).textTheme.headlineMedium,
            ),
            const SizedBox(height: 16),
            Text(
              user?.email ?? '',
              style: Theme.of(context).textTheme.bodyLarge,
            ),
          ],
        ),
      ),
    );
  }
}
```

### Step 13: Create lib/shared/widgets/loading_indicator.dart

```dart
import 'package:flutter/material.dart';

class LoadingIndicator extends StatelessWidget {
  const LoadingIndicator({super.key});

  @override
  Widget build(BuildContext context) {
    return const Center(
      child: CircularProgressIndicator(),
    );
  }
}
```

### Step 14: Create lib/core/constants/api_constants.dart

```dart
class ApiConstants {
  static const String baseUrl = String.fromEnvironment(
    'API_BASE_URL',
    defaultValue: 'http://localhost:3000/api/v1',
  );
  
  static const Duration connectTimeout = Duration(seconds: 30);
  static const Duration receiveTimeout = Duration(seconds: 30);
}
```

### Step 15: Create Dockerfile (for web build)

```dockerfile
FROM debian:latest AS build-env

# Install Flutter dependencies
RUN apt-get update
RUN apt-get install -y curl git wget unzip libgconf-2-4 gdb libstdc++6 libglu1-mesa fonts-droid-fallback lib32stdc++6 python3
RUN apt-get clean

# Clone Flutter repository
RUN git clone https://github.com/flutter/flutter.git /usr/local/flutter

# Set Flutter path
ENV PATH="/usr/local/flutter/bin:/usr/local/flutter/bin/cache/dart-sdk/bin:${PATH}"

# Run flutter doctor
RUN flutter doctor -v
RUN flutter channel stable
RUN flutter upgrade

# Copy files to container and build
RUN mkdir /app/
COPY . /app/
WORKDIR /app/
RUN flutter clean
RUN flutter pub get
RUN flutter build web

# Stage 2 - Create the runtime image
FROM nginx:alpine
COPY --from=build-env /app/build/web /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Step 16: Create docker-compose.yml

```yaml
version: '3.8'

services:
  [service-name]:
    build: .
    ports:
      - "80:80"
    networks:
      - microservices

networks:
  microservices:
    external: true
```

### Step 17: Create .env.example

```bash
API_BASE_URL=http://localhost:3000/api/v1
```

### Step 18: Create analysis_options.yaml

```yaml
include: package:flutter_lints/flutter.yaml

analyzer:
  exclude:
    - "**/*.g.dart"
    - "**/*.freezed.dart"
  
  errors:
    invalid_annotation_target: ignore

linter:
  rules:
    prefer_single_quotes: true
    always_use_package_imports: true
```

### Step 19: Create .gitignore

```
# Flutter/Dart
.dart_tool/
.flutter-plugins
.flutter-plugins-dependencies
.packages
.pub-cache/
.pub/
build/
*.g.dart
*.freezed.dart

# IDE
.idea/
.vscode/
*.iml

# OS
.DS_Store
Thumbs.db

# Environment
.env
.env.local
```

### Step 20: Create README.md

```markdown
# [App Name]

[Brief description of the Flutter application]

## Features

- Cross-platform support (iOS, Android, Web, Desktop)
- Clean Architecture with Riverpod
- Type-safe routing with GoRouter
- Network layer with Dio
- Local storage with Hive
- Form validation
- Material Design 3

## Prerequisites

- Flutter 3.16+
- Dart 3.2+

## Getting Started

1. Install dependencies:
```bash
flutter pub get
```

2. Generate code:
```bash
flutter pub run build_runner build --delete-conflicting-outputs
```

3. Run the app:
```bash
flutter run
```

## Build for Production

### Android
```bash
flutter build apk --release
```

### iOS
```bash
flutter build ios --release
```

### Web
```bash
flutter build web --release
```

## Project Structure

```
lib/
├── core/             # Core utilities, theme, routing
├── features/         # Feature modules (auth, home, etc.)
└── shared/           # Shared components
```

## Testing

Run all tests:
```bash
flutter test
```

## License

[Your License]
```

---

## ✅ Completion Checklist

- [ ] All files created in correct directory structure
- [ ] pubspec.yaml with dependencies configured
- [ ] Main app entry point implemented
- [ ] Routing with GoRouter configured
- [ ] Theme and styling setup
- [ ] Dio HTTP client configured
- [ ] Auth feature implemented
- [ ] State management with Riverpod
- [ ] Form validation
- [ ] Code generation setup
- [ ] Docker configuration for web
- [ ] .env.example created
- [ ] .gitignore configured
- [ ] README.md with documentation
- [ ] App tested on target platforms
- [ ] Build successful for all platforms

---

**Next Steps:**
1. Customize features for your specific use case
2. Add more feature modules
3. Implement offline support with Hive
4. Add biometric authentication
5. Set up CI/CD pipeline
6. Configure app signing for stores
7. Add analytics and crash reporting
8. Implement push notifications
