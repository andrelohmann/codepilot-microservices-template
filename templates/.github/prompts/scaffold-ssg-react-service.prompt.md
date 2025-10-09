---
description: "Scaffold a React SSG service"
mode: agent
tools: [file, workspace, terminal]
---

# Scaffold React SSG Service

Create a production-ready `${input:serviceName}` static site using React, Vite, MDX, and Tailwind CSS for content-driven websites.

## Reference Documentation

- **[React SSG Tech-Stack Guide](../../../../docs/tech-stacks/ssg/react.md)** - Complete implementation details
- **[React SSG Instructions](../instructions/service-ssg-react.instructions.md)** - Copilot guidance

## Service Configuration

Ask the user for:
- **Service Name**: `${input:serviceName}` (e.g., `ssg-react-docs`, `ssg-react-blog`)
- **Purpose**: `${input:servicePurpose}`
- **Port**: `${input:port:3000}`

## Project Structure

```
services/${input:serviceName}/
├── content/          # Markdown files (pages)
│   ├── index.md
│   └── docs/
├── src/
│   ├── components/
│   ├── pages/
│   └── lib/
├── vite.config.ts
└── README.md
```

## Implementation Steps

1. Initialize Vite React project
2. Configure MDX plugin
3. Set up Tailwind CSS
4. Create content directory structure
5. Implement MDX React components
6. Configure markdown processing (remark/rehype)
7. Create static site generation
8. Add form integration for contact
9. Create Dockerfile with Nginx
10. Update docker-compose.yml

Refer to [tech-stack guide](../../../../docs/tech-stacks/ssg/react.md) for detailed templates.

## Completion Checklist

- [ ] Vite + MDX configured
- [ ] Content structure created
- [ ] React components working in MDX
- [ ] Static build generates correctly
- [ ] Forms integrated
- [ ] Docker builds successfully

---

**Note**: Refer to [complete documentation](../../../../docs/tech-stacks/ssg/react.md) for implementation details.
