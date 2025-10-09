# React SPA - Single Page Application

Modern React single-page application with Vite, TypeScript, and Tailwind CSS for interactive web interfaces.

---

## Overview

**Naming Convention:** `frontend-react-{purpose}`

**Examples:**
- `frontend-react-dashboard` - Admin dashboard
- `frontend-react-admin` - Admin panel
- `frontend-react-portal` - B2B portal

**React SPA** is perfect for building interactive web applications like admin dashboards, internal tools, and B2B portals where SEO is not critical.

---

## Technology Stack

- **React 18+** - UI library
- **TypeScript 5+** - Type safety
- **Vite 5+** - Fast build tool with HMR
- **TanStack Query** (React Query) - Data fetching & caching
- **Zustand** - Lightweight state management
- **React Router 6** - Client-side routing
- **Tailwind CSS 3+** - Utility-first styling
- **React Hook Form** - Form handling
- **Zod** - Schema validation

---

## Features

- ✅ **Fast builds** - Vite's lightning-fast HMR
- ✅ **Component-based** - Reusable UI components
- ✅ **Client-side routing** - Instant page transitions
- ✅ **Optimistic updates** - Better UX with TanStack Query
- ✅ **Type-safe** - Full TypeScript support
- ✅ **Modern state management** - Zustand (simpler than Redux)

---

## Ideal Use Cases

✅ **Admin Dashboards** - Data visualization, analytics  
✅ **Internal Tools** - Business operation tools  
✅ **B2B Portals** - Partner/client portals  
✅ **SaaS Applications** - Authenticated web apps  
✅ **Data Entry Applications** - Forms and CRUD operations

❌ **Not Ideal For:**
- Public marketing sites (poor SEO) → Use Next.js or SSG
- Static content sites → Use SSG (Astro/React)
- Mobile apps → Use Flutter

---

## Project Structure

```
frontend-react-{purpose}/
├── src/
│   ├── components/
│   │   ├── ui/              # Reusable UI components
│   │   ├── forms/           # Form components
│   │   └── layout/          # Layout components
│   ├── pages/               # Page components
│   ├── hooks/               # Custom React hooks
│   ├── lib/
│   │   ├── api.ts           # API client
│   │   └── utils.ts         # Utilities
│   ├── stores/              # Zustand stores
│   ├── types/               # TypeScript types
│   ├── App.tsx
│   └── main.tsx
├── public/
├── tests/
├── .env.example
├── package.json
├── vite.config.ts
├── tailwind.config.ts
└── Dockerfile
```

---

## Code Examples

### API Client Setup

```typescript
// src/lib/api.ts
import axios from 'axios';

export const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
  timeout: 10000,
});

api.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

### TanStack Query Setup

```typescript
// src/App.tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 60000,
      retry: 1,
    },
  },
});

export function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <RouterProvider router={router} />
    </QueryClientProvider>
  );
}
```

### Data Fetching with TanStack Query

```typescript
// src/hooks/useUsers.ts
import { useQuery } from '@tanstack/react-query';
import { api } from '@/lib/api';

export function useUsers() {
  return useQuery({
    queryKey: ['users'],
    queryFn: async () => {
      const { data } = await api.get('/users');
      return data;
    },
  });
}

// Usage in component
function UserList() {
  const { data: users, isLoading, error } = useUsers();

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### Zustand Store

```typescript
// src/stores/authStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface AuthState {
  user: User | null;
  token: string | null;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      token: null,
      login: async (email, password) => {
        const { data } = await api.post('/auth/login', { email, password });
        set({ user: data.user, token: data.token });
        localStorage.setItem('token', data.token);
      },
      logout: () => {
        set({ user: null, token: null });
        localStorage.removeItem('token');
      },
    }),
    { name: 'auth-storage' }
  )
);
```

### Form with React Hook Form + Zod

```typescript
// src/components/forms/LoginForm.tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const loginSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
});

type LoginForm = z.infer<typeof loginSchema>;

export function LoginForm() {
  const { register, handleSubmit, formState: { errors } } = useForm<LoginForm>({
    resolver: zodResolver(loginSchema),
  });

  const onSubmit = async (data: LoginForm) => {
    await useAuthStore.getState().login(data.email, data.password);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
      <div>
        <label htmlFor="email">Email</label>
        <input
          {...register('email')}
          type="email"
          className="w-full px-4 py-2 border rounded"
        />
        {errors.email && <p className="text-red-500">{errors.email.message}</p>}
      </div>
      
      <div>
        <label htmlFor="password">Password</label>
        <input
          {...register('password')}
          type="password"
          className="w-full px-4 py-2 border rounded"
        />
        {errors.password && <p className="text-red-500">{errors.password.message}</p>}
      </div>

      <button type="submit" className="bg-blue-600 text-white px-6 py-2 rounded">
        Login
      </button>
    </form>
  );
}
```

---

## Testing

```typescript
// tests/LoginForm.test.tsx
import { render, screen, userEvent } from '@testing-library/react';
import { LoginForm } from '@/components/forms/LoginForm';

test('submits login form', async () => {
  render(<LoginForm />);
  
  await userEvent.type(screen.getByLabelText('Email'), 'test@example.com');
  await userEvent.type(screen.getByLabelText('Password'), 'password123');
  await userEvent.click(screen.getByRole('button', { name: 'Login' }));
  
  // Assertions...
});
```

---

## Deployment

### Docker

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Environment Variables

```env
VITE_API_URL=https://api.example.com
VITE_APP_NAME=My Dashboard
```

---

## Best Practices

✅ Use TanStack Query for server state  
✅ Use Zustand for client state  
✅ Implement proper error boundaries  
✅ Use React.lazy for code splitting  
✅ Optimize images and assets  
✅ Implement proper loading states  
✅ Use TypeScript strictly  
✅ Write tests for critical flows

---

## Related Documentation

- [Frontends Overview](./README.md)
- [Next.js](./nextjs.md) - For SEO-critical sites
- [Flutter](./flutter.md) - For mobile apps
- [API Technologies](../apis/README.md)

---

**Reference Files:**
- Instruction: `service-frontend-react.instructions.md`
- Scaffold: `scaffold-frontend-react-service.md`
