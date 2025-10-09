---
description: "Scaffold a React SPA frontend service"
mode: agent
tools: [file, workspace, terminal]
---

# Scaffold React Frontend Service

Create a production-ready `${input:serviceName}` SPA using React, TypeScript, Vite, TanStack Query, and Tailwind CSS.

## Reference Documentation

- **[React Frontend Tech-Stack Guide](../../../../docs/tech-stacks/frontends/react.md)** - Complete implementation details
- **[React Instructions](../instructions/service-frontend-react.instructions.md)** - Copilot guidance

## Service Configuration

Ask the user for:
- **Service Name**: `${input:serviceName}` (e.g., `frontend-react-dashboard`, `frontend-react-admin`)
- **Purpose**: `${input:servicePurpose}`
- **Port**: `${input:port:3000}`
- **API URL**: `${input:apiUrl:http://localhost:8000}`

## Project Structure

```
services/${input:serviceName}/
├── src/
│   ├── main.tsx
│   ├── App.tsx
│   ├── components/
│   ├── pages/
│   ├── hooks/
│   ├── services/
│   ├── store/
│   └── types/
├── vite.config.ts
├── tailwind.config.ts
├── package.json
└── README.md
```

## Implementation Steps

1. Initialize Vite React TypeScript project
2. Configure Tailwind CSS
3. Set up React Router v6
4. Configure TanStack Query
5. Create Zustand store for global state
6. Set up Axios with interceptors
7. Implement authentication flow
8. Create reusable components
9. Configure Vitest for testing
10. Create Dockerfile for production
11. Update docker-compose.yml

Refer to [tech-stack guide](../../../../docs/tech-stacks/frontends/react.md) for detailed templates.

## Completion Checklist

- [ ] Vite project configured
- [ ] React Router set up
- [ ] TanStack Query configured
- [ ] Zustand store created
- [ ] Axios configured
- [ ] Components created
- [ ] Tests passing
- [ ] Docker builds successfully

---

**Note**: Refer to [complete documentation](../../../../docs/tech-stacks/frontends/react.md) for implementation details.
