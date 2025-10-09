# Scaffold React SPA Service

**Objective:** Create a production-ready React Single Page Application with Vite, React Router, TanStack Query, Zustand state management, and comprehensive testing.

---

## 📋 Service Specification

Before running this prompt, gather the following information:

1. **Service Name:** `[e.g., frontend-react-dashboard, frontend-react-admin]`
2. **Service Purpose:** `[Brief description of the application]`
3. **API Endpoints:** `[Backend API URL]`
4. **Authentication:** `[Yes/No - JWT, OAuth]`
5. **State Management:** `[Zustand (recommended), Context API]`
6. **UI Library:** `[Tailwind CSS (default), Material-UI, Ant Design]`
7. **Port:** `[Default: 3000, or specify custom]`

---

## 🏗️ Architecture Overview

```
┌────────────────────────────────────────────────────────────────────┐
│                      React SPA Architecture                        │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                      Presentation Layer                      │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │   Pages    │  │ Components │  │   Layouts  │            │ │
│  │  │  (Routes)  │  │    (UI)    │  │  (Common)  │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
│            ↓                ↓                ↓                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                      Business Logic Layer                    │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │   Hooks    │  │   State    │  │   Forms    │            │ │
│  │  │  (Custom)  │  │  (Zustand) │  │(React Hook │            │ │
│  │  │            │  │            │  │   Form)    │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
│            ↓                ↓                ↓                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                      Data Layer                              │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐            │ │
│  │  │  TanStack  │  │   Axios    │  │WebSockets  │            │ │
│  │  │   Query    │  │  (HTTP)    │  │ (Optional) │            │ │
│  │  └──────┬─────┘  └──────┬─────┘  └──────┬─────┘            │ │
│  └─────────┼────────────────┼────────────────┼──────────────────┘ │
│            │                │                │                    │
└────────────┼────────────────┼────────────────┼────────────────────┘
             │                │                │
             ↓                ↓                ↓
    ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
    │  REST API   │  │  GraphQL    │  │  WebSocket  │
    │  (Backend)  │  │ (Optional)  │  │   Server    │
    └─────────────┘  └─────────────┘  └─────────────┘
```

---

## 📁 Directory Structure

Create the following complete directory structure:

```
services/[service-name]/
├── src/
│   ├── main.tsx                   ← Application entry point
│   ├── App.tsx                    ← Root component
│   ├── vite-env.d.ts              ← Vite types
│   │
│   ├── pages/                     ← Page components
│   │   ├── Home.tsx
│   │   ├── Dashboard.tsx
│   │   ├── Login.tsx
│   │   ├── NotFound.tsx
│   │   └── users/
│   │       ├── UserList.tsx
│   │       ├── UserDetail.tsx
│   │       └── UserCreate.tsx
│   │
│   ├── components/                ← Reusable components
│   │   ├── layout/
│   │   │   ├── Layout.tsx
│   │   │   ├── Header.tsx
│   │   │   ├── Sidebar.tsx
│   │   │   └── Footer.tsx
│   │   ├── common/
│   │   │   ├── Button.tsx
│   │   │   ├── Input.tsx
│   │   │   ├── Modal.tsx
│   │   │   ├── Spinner.tsx
│   │   │   └── ErrorBoundary.tsx
│   │   └── features/
│   │       ├── UserCard.tsx
│   │       └── DataTable.tsx
│   │
│   ├── hooks/                     ← Custom React hooks
│   │   ├── useAuth.ts
│   │   ├── useUsers.ts
│   │   ├── useDebounce.ts
│   │   └── useLocalStorage.ts
│   │
│   ├── store/                     ← Zustand stores
│   │   ├── authStore.ts
│   │   ├── uiStore.ts
│   │   └── userStore.ts
│   │
│   ├── services/                  ← API services
│   │   ├── api.ts                 ← Axios instance
│   │   ├── auth.service.ts
│   │   └── user.service.ts
│   │
│   ├── types/                     ← TypeScript types
│   │   ├── auth.types.ts
│   │   ├── user.types.ts
│   │   └── api.types.ts
│   │
│   ├── utils/                     ← Utility functions
│   │   ├── format.ts
│   │   ├── validation.ts
│   │   └── constants.ts
│   │
│   ├── router/                    ← React Router config
│   │   ├── index.tsx              ← Route definitions
│   │   └── ProtectedRoute.tsx    ← Auth guard
│   │
│   └── styles/                    ← Styles
│       ├── index.css              ← Global styles
│       └── tailwind.css           ← Tailwind imports
│
├── public/                        ← Static assets
│   ├── favicon.ico
│   └── robots.txt
│
├── tests/                         ← Tests
│   ├── setup.ts                   ← Test setup
│   ├── unit/
│   │   ├── components/
│   │   ├── hooks/
│   │   └── utils/
│   └── e2e/
│       └── app.spec.ts
│
├── .eslintrc.cjs                  ← ESLint config
├── .prettierrc                    ← Prettier config
├── vite.config.ts                 ← Vite configuration
├── tsconfig.json                  ← TypeScript config
├── tsconfig.node.json             ← Node TypeScript config
├── tailwind.config.js             ← Tailwind config
├── postcss.config.js              ← PostCSS config
├── package.json                   ← Dependencies
├── Dockerfile                     ← Production Docker image
├── nginx.conf                     ← Nginx configuration
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
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "format": "prettier --write \"src/**/*.{ts,tsx,css}\"",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:e2e": "playwright test",
    "type-check": "tsc --noEmit"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.20.1",
    "@tanstack/react-query": "^5.14.2",
    "@tanstack/react-query-devtools": "^5.14.2",
    "zustand": "^4.4.7",
    "axios": "^1.6.2",
    "react-hook-form": "^7.49.2",
    "zod": "^3.22.4",
    "@hookform/resolvers": "^3.3.3",
    "clsx": "^2.0.0",
    "date-fns": "^3.0.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.43",
    "@types/react-dom": "^18.2.17",
    "@vitejs/plugin-react-swc": "^3.5.0",
    "vite": "^5.0.8",
    "typescript": "^5.3.3",
    "tailwindcss": "^3.4.0",
    "autoprefixer": "^10.4.16",
    "postcss": "^8.4.32",
    "@typescript-eslint/eslint-plugin": "^6.14.0",
    "@typescript-eslint/parser": "^6.14.0",
    "eslint": "^8.55.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.4.5",
    "prettier": "^3.1.1",
    "vitest": "^1.0.4",
    "@vitest/ui": "^1.0.4",
    "@testing-library/react": "^14.1.2",
    "@testing-library/jest-dom": "^6.1.5",
    "@testing-library/user-event": "^14.5.1",
    "@playwright/test": "^1.40.1",
    "jsdom": "^23.0.1"
  }
}
```

---

## 🔧 Step 2: `src/main.tsx` - Application Entry Point

```typescript
import React from 'react';
import ReactDOM from 'react-dom/client';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';
import App from './App';
import './styles/index.css';

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      retry: 1,
      refetchOnWindowFocus: false,
      staleTime: 5 * 60 * 1000, // 5 minutes
    },
  },
});

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <QueryClientProvider client={queryClient}>
      <App />
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  </React.StrictMode>,
);
```

---

## 🔧 Step 3: `src/App.tsx` - Root Component

```typescript
import { BrowserRouter } from 'react-router-dom';
import { ErrorBoundary } from './components/common/ErrorBoundary';
import { AppRouter } from './router';

function App() {
  return (
    <ErrorBoundary>
      <BrowserRouter>
        <AppRouter />
      </BrowserRouter>
    </ErrorBoundary>
  );
}

export default App;
```

---

## 🔧 Step 4: `src/router/index.tsx` - Route Definitions

```typescript
import { Routes, Route, Navigate } from 'react-router-dom';
import { Layout } from '@/components/layout/Layout';
import { ProtectedRoute } from './ProtectedRoute';
import Home from '@/pages/Home';
import Login from '@/pages/Login';
import Dashboard from '@/pages/Dashboard';
import UserList from '@/pages/users/UserList';
import UserDetail from '@/pages/users/UserDetail';
import UserCreate from '@/pages/users/UserCreate';
import NotFound from '@/pages/NotFound';

export function AppRouter() {
  return (
    <Routes>
      {/* Public routes */}
      <Route path="/login" element={<Login />} />
      
      {/* Protected routes with layout */}
      <Route element={<Layout />}>
        <Route
          path="/"
          element={
            <ProtectedRoute>
              <Home />
            </ProtectedRoute>
          }
        />
        <Route
          path="/dashboard"
          element={
            <ProtectedRoute>
              <Dashboard />
            </ProtectedRoute>
          }
        />
        <Route
          path="/users"
          element={
            <ProtectedRoute>
              <UserList />
            </ProtectedRoute>
          }
        />
        <Route
          path="/users/:id"
          element={
            <ProtectedRoute>
              <UserDetail />
            </ProtectedRoute>
          }
        />
        <Route
          path="/users/new"
          element={
            <ProtectedRoute>
              <UserCreate />
            </ProtectedRoute>
          }
        />
      </Route>

      {/* 404 */}
      <Route path="/404" element={<NotFound />} />
      <Route path="*" element={<Navigate to="/404" replace />} />
    </Routes>
  );
}
```

---

## 🔧 Step 5: `src/router/ProtectedRoute.tsx` - Auth Guard

```typescript
import { Navigate } from 'react-router-dom';
import { useAuthStore } from '@/store/authStore';

interface ProtectedRouteProps {
  children: React.ReactNode;
}

export function ProtectedRoute({ children }: ProtectedRouteProps) {
  const isAuthenticated = useAuthStore((state) => state.isAuthenticated);

  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }

  return <>{children}</>;
}
```

---

## 🔧 Step 6: `src/store/authStore.ts` - Auth Store (Zustand)

```typescript
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface User {
  id: string;
  email: string;
  name: string;
}

interface AuthState {
  user: User | null;
  token: string | null;
  isAuthenticated: boolean;
  login: (token: string, user: User) => void;
  logout: () => void;
  updateUser: (user: Partial<User>) => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      token: null,
      isAuthenticated: false,

      login: (token, user) => {
        set({ token, user, isAuthenticated: true });
      },

      logout: () => {
        set({ token: null, user: null, isAuthenticated: false });
      },

      updateUser: (userData) => {
        set((state) => ({
          user: state.user ? { ...state.user, ...userData } : null,
        }));
      },
    }),
    {
      name: 'auth-storage',
    }
  )
);
```

---

## 🔧 Step 7: `src/services/api.ts` - Axios Instance

```typescript
import axios from 'axios';
import { useAuthStore } from '@/store/authStore';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL || 'http://localhost:8000/api/v1',
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Request interceptor - add auth token
api.interceptors.request.use(
  (config) => {
    const token = useAuthStore.getState().token;
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// Response interceptor - handle errors
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      useAuthStore.getState().logout();
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default api;
```

---

## 🔧 Step 8: `src/services/user.service.ts` - User Service

```typescript
import api from './api';
import { User } from '@/types/user.types';

export const userService = {
  getUsers: async () => {
    const response = await api.get<User[]>('/users');
    return response.data;
  },

  getUser: async (id: string) => {
    const response = await api.get<User>(`/users/${id}`);
    return response.data;
  },

  createUser: async (data: Partial<User>) => {
    const response = await api.post<User>('/users', data);
    return response.data;
  },

  updateUser: async (id: string, data: Partial<User>) => {
    const response = await api.patch<User>(`/users/${id}`, data);
    return response.data;
  },

  deleteUser: async (id: string) => {
    await api.delete(`/users/${id}`);
  },
};
```

---

## 🔧 Step 9: `src/hooks/useUsers.ts` - Custom Hook with TanStack Query

```typescript
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { userService } from '@/services/user.service';
import { User } from '@/types/user.types';

export function useUsers() {
  return useQuery({
    queryKey: ['users'],
    queryFn: userService.getUsers,
  });
}

export function useUser(id: string) {
  return useQuery({
    queryKey: ['users', id],
    queryFn: () => userService.getUser(id),
    enabled: !!id,
  });
}

export function useCreateUser() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: userService.createUser,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
    },
  });
}

export function useUpdateUser() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: ({ id, data }: { id: string; data: Partial<User> }) =>
      userService.updateUser(id, data),
    onSuccess: (_, variables) => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
      queryClient.invalidateQueries({ queryKey: ['users', variables.id] });
    },
  });
}

export function useDeleteUser() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: userService.deleteUser,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
    },
  });
}
```

---

## 🔧 Step 10: `src/components/layout/Layout.tsx` - Layout Component

```typescript
import { Outlet } from 'react-router-dom';
import { Header } from './Header';
import { Sidebar } from './Sidebar';
import { Footer } from './Footer';

export function Layout() {
  return (
    <div className="min-h-screen flex flex-col">
      <Header />
      
      <div className="flex flex-1">
        <Sidebar />
        
        <main className="flex-1 p-6 bg-gray-50">
          <Outlet />
        </main>
      </div>
      
      <Footer />
    </div>
  );
}
```

---

## 🔧 Step 11: `src/components/common/ErrorBoundary.tsx` - Error Boundary

```typescript
import React, { Component, ErrorInfo, ReactNode } from 'react';

interface Props {
  children: ReactNode;
}

interface State {
  hasError: boolean;
  error: Error | null;
}

export class ErrorBoundary extends Component<Props, State> {
  public state: State = {
    hasError: false,
    error: null,
  };

  public static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }

  public componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    console.error('Uncaught error:', error, errorInfo);
  }

  public render() {
    if (this.state.hasError) {
      return (
        <div className="min-h-screen flex items-center justify-center bg-gray-100">
          <div className="bg-white p-8 rounded-lg shadow-lg max-w-md">
            <h1 className="text-2xl font-bold text-red-600 mb-4">
              Something went wrong
            </h1>
            <p className="text-gray-600 mb-4">
              {this.state.error?.message || 'An unexpected error occurred'}
            </p>
            <button
              onClick={() => window.location.reload()}
              className="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600"
            >
              Reload Page
            </button>
          </div>
        </div>
      );
    }

    return this.props.children;
  }
}
```

---

## 🔧 Step 12: `src/pages/users/UserList.tsx` - Example Page

```typescript
import { useState } from 'react';
import { Link } from 'react-router-dom';
import { useUsers, useDeleteUser } from '@/hooks/useUsers';

export default function UserList() {
  const { data: users, isLoading, error } = useUsers();
  const deleteUser = useDeleteUser();

  const handleDelete = async (id: string) => {
    if (confirm('Are you sure you want to delete this user?')) {
      await deleteUser.mutateAsync(id);
    }
  };

  if (isLoading) {
    return <div>Loading...</div>;
  }

  if (error) {
    return <div>Error loading users</div>;
  }

  return (
    <div>
      <div className="flex justify-between items-center mb-6">
        <h1 className="text-2xl font-bold">Users</h1>
        <Link
          to="/users/new"
          className="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600"
        >
          Create User
        </Link>
      </div>

      <div className="bg-white rounded-lg shadow overflow-hidden">
        <table className="min-w-full divide-y divide-gray-200">
          <thead className="bg-gray-50">
            <tr>
              <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">
                Name
              </th>
              <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">
                Email
              </th>
              <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase">
                Actions
              </th>
            </tr>
          </thead>
          <tbody className="bg-white divide-y divide-gray-200">
            {users?.map((user) => (
              <tr key={user.id}>
                <td className="px-6 py-4 whitespace-nowrap">{user.name}</td>
                <td className="px-6 py-4 whitespace-nowrap">{user.email}</td>
                <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                  <Link
                    to={`/users/${user.id}`}
                    className="text-blue-600 hover:text-blue-900 mr-4"
                  >
                    View
                  </Link>
                  <button
                    onClick={() => handleDelete(user.id)}
                    className="text-red-600 hover:text-red-900"
                  >
                    Delete
                  </button>
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
    </div>
  );
}
```

---

## 🔧 Step 13: `vite.config.ts` - Vite Configuration

```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react-swc';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
  server: {
    port: 3000,
    host: true,
  },
  build: {
    outDir: 'dist',
    sourcemap: true,
  },
});
```

---

## 🔧 Step 14: `tsconfig.json` - TypeScript Configuration

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,

    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",

    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,

    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

---

## 🔧 Step 15: `tailwind.config.js` - Tailwind Configuration

```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

---

## 🔧 Step 16: `.env.example` - Environment Variables Template

```bash
# API Configuration
VITE_API_URL=http://localhost:8000/api/v1

# Environment
VITE_ENV=production
```

---

## 🐳 Step 17: `Dockerfile` - Production Docker Image

```dockerfile
# Build stage
FROM node:20-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy source code
COPY . .

# Build application
RUN npm run build

# Production stage with Nginx
FROM nginx:alpine

# Copy built assets from builder
COPY --from=builder /app/dist /usr/share/nginx/html

# Copy nginx configuration
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost/ || exit 1

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

---

## 🔧 Step 18: `nginx.conf` - Nginx Configuration

```nginx
server {
    listen 80;
    server_name _;
    root /usr/share/nginx/html;
    index index.html;

    # Compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript application/x-javascript application/xml+rss application/json;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    # SPA routing - serve index.html for all routes
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Static assets caching
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

---

## 🐳 Step 19: Docker Compose Integration

Add to `docker-compose.yml`:

```yaml
  [service-name]:
    build:
      context: ./services/[service-name]
      dockerfile: Dockerfile
    container_name: [service-name]
    restart: unless-stopped
    ports:
      - "3000:80"
    environment:
      - VITE_API_URL=${API_URL}
    networks:
      - microservices
    healthcheck:
      test: ["CMD", "wget", "--no-verbose", "--tries=1", "--spider", "http://localhost/"]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 5s
```

---

## 🔧 Step 20: `.devcontainer/[service-name]-container/devcontainer.json`

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
        "bradlc.vscode-tailwindcss",
        "dsznajder.es7-react-js-snippets"
      ],
      "settings": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
          "source.fixAll.eslint": "explicit"
        },
        "tailwindCSS.experimental.classRegex": [
          ["clsx\\(([^)]*)\\)", "(?:'|\"|`)([^']*)(?:'|\"|`)"]
        ]
      }
    }
  },
  "forwardPorts": [3000],
  "postCreateCommand": "npm install",
  "remoteUser": "node"
}
```

---

## 📝 Step 21: `README.md` - Service Documentation

```markdown
# [Service Name]

[Service Purpose]

## Technology Stack

- **Framework:** React 18
- **Build Tool:** Vite 5
- **Language:** TypeScript 5
- **Routing:** React Router 6
- **State Management:** Zustand
- **Data Fetching:** TanStack Query
- **Forms:** React Hook Form + Zod
- **Styling:** Tailwind CSS
- **HTTP Client:** Axios
- **Testing:** Vitest, Playwright
- **Server:** Nginx

## Features

- ✅ React 18 with TypeScript
- ✅ React Router for SPA routing
- ✅ TanStack Query for server state
- ✅ Zustand for client state
- ✅ Form validation with Zod
- ✅ Tailwind CSS for styling
- ✅ Axios with interceptors
- ✅ Protected routes
- ✅ Error boundaries
- ✅ Unit and E2E testing
- ✅ Production-ready Nginx config

## Getting Started

### Development

```bash
# Install dependencies
npm install

# Run development server
npm run dev

# Run tests
npm run test

# Run E2E tests
npm run test:e2e
```

## Environment Variables

See `.env.example` for required environment variables.

## Project Structure

- `src/pages/` - Page components (routes)
- `src/components/` - Reusable components
- `src/hooks/` - Custom React hooks
- `src/store/` - Zustand stores
- `src/services/` - API services
- `src/types/` - TypeScript types
- `src/router/` - Route configuration

## Testing

```bash
# Unit tests
npm run test

# E2E tests
npm run test:e2e

# Type check
npm run type-check
```

## Building

```bash
# Build for production
npm run build

# Preview production build
npm run preview
```
```

---

## ✅ Completion Checklist

- [ ] All directories created
- [ ] `package.json` configured
- [ ] `src/main.tsx` entry point created
- [ ] `src/App.tsx` root component created
- [ ] Router configuration complete
- [ ] Protected routes implemented
- [ ] Zustand auth store created
- [ ] Axios instance configured
- [ ] TanStack Query hooks created
- [ ] Layout components created
- [ ] Error boundary implemented
- [ ] Example pages created
- [ ] Vite configuration complete
- [ ] TypeScript configuration complete
- [ ] Tailwind CSS configured
- [ ] `Dockerfile` created with Nginx
- [ ] `nginx.conf` for SPA routing
- [ ] `.env.example` documented
- [ ] Docker Compose integration added
- [ ] `.devcontainer/[service-name]-container/devcontainer.json` created at project root
- [ ] `README.md` completed
- [ ] Service builds successfully
- [ ] All routes accessible
- [ ] Authentication flow working
- [ ] API calls working

---

**Note:** Replace all `[service-name]` placeholders with your actual service name. Update API endpoints and authentication logic based on your backend requirements.
