---
applyTo: "**/frontend-react-*/**"
description: React SPA with TypeScript, Vite, TanStack Query, and Tailwind
---

# Frontend React - Single Page Application

Build interactive SPAs with React, TypeScript, and modern tooling.

## Naming Convention

`frontend-react-{purpose}` (e.g., `frontend-react-dashboard`, `frontend-react-admin`)

## Technology Stack

- React 18+ (UI library)
- TypeScript 5+ (language)
- Vite (build tool)
- React Router (routing)
- TanStack Query (data fetching)
- Zustand or Jotai (state management)
- Tailwind CSS (styling)
- Axios (HTTP client)
- Vitest + React Testing Library (testing)
- Playwright (E2E)

## Directory Structure

```
services/frontend-react-{purpose}/
├── src/
│   ├── main.tsx
│   ├── App.tsx
│   ├── components/
│   │   ├── ui/
│   │   └── layout/
│   ├── pages/
│   │   ├── Dashboard.tsx
│   │   └── Login.tsx
│   ├── hooks/
│   │   └── useAuth.ts
│   ├── services/
│   │   └── api.ts
│   ├── store/
│   │   └── auth.store.ts
│   ├── types/
│   │   └── index.ts
│   └── utils/
├── public/
├── tests/
├── Dockerfile
├── package.json
├── vite.config.ts
└── tailwind.config.ts
```

## Routing

React Router v6:
```typescript
import { createBrowserRouter } from 'react-router-dom';

const router = createBrowserRouter([
  {
    path: '/',
    element: <Layout />,
    children: [
      { index: true, element: <Dashboard /> },
      { path: 'users', element: <Users /> },
    ],
  },
]);
```

## Data Fetching

TanStack Query:
```typescript
import { useQuery, useMutation } from '@tanstack/react-query';

function Users() {
  const { data, isLoading } = useQuery({
    queryKey: ['users'],
    queryFn: () => api.get('/users'),
  });

  const createUser = useMutation({
    mutationFn: (user) => api.post('/users', user),
    onSuccess: () => queryClient.invalidateQueries(['users']),
  });
}
```

## State Management

Zustand for global state:
```typescript
import { create } from 'zustand';

interface AuthStore {
  user: User | null;
  login: (credentials) => Promise<void>;
  logout: () => void;
}

export const useAuthStore = create<AuthStore>((set) => ({
  user: null,
  login: async (credentials) => {
    const user = await api.login(credentials);
    set({ user });
  },
  logout: () => set({ user: null }),
}));
```

## API Client

Axios with interceptors:
```typescript
import axios from 'axios';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
});

api.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

api.interceptors.response.use(
  (response) => response.data,
  (error) => {
    if (error.response?.status === 401) {
      // Redirect to login
    }
    return Promise.reject(error);
  }
);
```

## Forms

React Hook Form + Zod:
```typescript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
});

function LoginForm() {
  const { register, handleSubmit, formState } = useForm({
    resolver: zodResolver(schema),
  });

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} />
      {formState.errors.email && <span>{formState.errors.email.message}</span>}
    </form>
  );
}
```

## Styling

Tailwind with custom components:
```typescript
const Button = ({ variant = 'primary', children, ...props }) => (
  <button
    className={cn(
      'px-4 py-2 rounded-md font-medium',
      variant === 'primary' && 'bg-blue-600 text-white hover:bg-blue-700',
      variant === 'secondary' && 'bg-gray-200 text-gray-900 hover:bg-gray-300'
    )}
    {...props}
  >
    {children}
  </button>
);
```

## Testing

Component tests:
```typescript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

test('login form submits', async () => {
  const onSubmit = vi.fn();
  render(<LoginForm onSubmit={onSubmit} />);
  
  await userEvent.type(screen.getByLabelText('Email'), 'test@example.com');
  await userEvent.type(screen.getByLabelText('Password'), 'password123');
  await userEvent.click(screen.getByRole('button', { name: /login/i }));
  
  expect(onSubmit).toHaveBeenCalled();
});
```

## Environment Variables

```typescript
// vite-env.d.ts
interface ImportMetaEnv {
  readonly VITE_API_URL: string;
  readonly VITE_APP_NAME: string;
}
```

```bash
# .env.example
VITE_API_URL=http://localhost:8000
VITE_APP_NAME=My App
```

## Build Optimization

```typescript
// vite.config.ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom', 'react-router-dom'],
          ui: ['@tanstack/react-query', 'zustand'],
        },
      },
    },
  },
});
```

## Anti-Patterns

- ❌ Prop drilling (use context/store)
- ❌ Fetching data in useEffect (use TanStack Query)
- ❌ Storing server state in useState
- ❌ Not memoizing expensive computations
- ❌ Missing loading and error states
