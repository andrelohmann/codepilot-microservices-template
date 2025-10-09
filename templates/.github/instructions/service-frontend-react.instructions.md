---
applyTo: "**/frontend-react-*/**"
description: React SPA with TypeScript, Vite, TanStack Query, and Tailwind
---

# Frontend React - Single Page Application

Build interactive SPAs with React, TypeScript, and modern tooling.

**Reference**: [Complete React Frontend Tech Stack Documentation](../../../docs/tech-stacks/frontends/react.md)  
**Scaffold**: Use the `scaffold-frontend-react-service.prompt.md` for new services

## Naming Convention

`frontend-react-{purpose}` (e.g., `frontend-react-dashboard`, `frontend-react-admin`)

## Project Structure

```
services/frontend-react-{purpose}/
├── src/
│   ├── main.tsx
│   ├── App.tsx
│   ├── components/
│   ├── pages/
│   ├── hooks/
│   ├── services/
│   ├── store/
│   └── types/
└── vite.config.ts
```

## Quick Reference Patterns

### 1. React Router v6

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

### 2. TanStack Query

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

### 3. Zustand Store

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

### 4. React Hook Form + Zod

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

## Code Style

- Use React Router v6 for routing with nested routes
- TanStack Query for all server state management
- Zustand or Jotai for global client state
- React Hook Form + Zod for form validation
- Tailwind CSS for styling
- Axios with interceptors for API calls
- Always handle loading and error states
- Memoize expensive computations with useMemo

## Anti-Patterns

- ❌ Prop drilling (use context/store)
- ❌ Fetching data in useEffect (use TanStack Query)
- ❌ Storing server state in useState
- ❌ Not memoizing expensive computations
- ❌ Missing loading and error states
