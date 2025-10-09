---
description: "Scaffold a Next.js frontend service"
mode: agent
tools: [file, workspace, terminal]
---

# Scaffold Next.js Frontend Service

Create a production-ready `${input:serviceName}` using Next.js 14+ with App Router, React Server Components, and Server Actions.

## Reference Documentation

- **[Next.js Tech-Stack Guide](../../../../docs/tech-stacks/frontends/nextjs.md)** - Complete implementation details
- **[Next.js Instructions](../instructions/service-frontend-nextjs.instructions.md)** - Copilot guidance

## Service Configuration

Ask the user for:
- **Service Name**: `${input:serviceName}` (e.g., `frontend-nextjs-landing`, `frontend-nextjs-docs`)
- **Purpose**: `${input:servicePurpose}`
- **Port**: `${input:port:3000}`
- **Database**: `${input:database:PostgreSQL}` (for Prisma/Drizzle)

## Project Structure

```
services/${input:serviceName}/
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── (auth)/
│   ├── (dashboard)/
│   └── api/
├── components/
├── lib/
├── next.config.js
└── README.md
```

## Implementation Steps

1. Initialize Next.js 14+ project with TypeScript
2. Configure App Router structure
3. Set up Tailwind CSS
4. Configure Prisma/Drizzle ORM
5. Implement Server Actions
6. Set up NextAuth.js
7. Create layouts and route groups
8. Configure metadata for SEO
9. Implement middleware for auth
10. Create Dockerfile
11. Update docker-compose.yml

Refer to [tech-stack guide](../../../../docs/tech-stacks/frontends/nextjs.md) for detailed templates.

## Completion Checklist

- [ ] Next.js App Router configured
- [ ] Server Components working
- [ ] Server Actions implemented
- [ ] Database configured
- [ ] NextAuth.js set up
- [ ] Metadata configured
- [ ] Tests passing
- [ ] Docker builds successfully

---

**Note**: Refer to [complete documentation](../../../../docs/tech-stacks/frontends/nextjs.md) for implementation details.
