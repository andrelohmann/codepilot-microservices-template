---
description: "Scaffold a Traefik gateway service"
mode: agent
tools: [file, workspace, terminal]
---

# Scaffold Traefik Gateway Service

Create a production-ready `${input:serviceName}` reverse proxy using Traefik with automatic HTTPS, service discovery, and load balancing.

## Reference Documentation

- **[Traefik Tech-Stack Guide](../../../../docs/tech-stacks/gateways/traefik.md)** - Complete implementation details
- **[Traefik Instructions](../instructions/service-gateway-traefik.instructions.md)** - Copilot guidance

## Service Configuration

Ask the user for:
- **Service Name**: `${input:serviceName}` (e.g., `gateway-traefik-main`)
- **Domain**: `${input:domain:example.com}`
- **Email**: `${input:email}` (for Let's Encrypt)

## Project Structure

```
services/${input:serviceName}/
├── config/
│   ├── traefik.yml      # Static config
│   ├── dynamic/         # Dynamic config
│   └── acme/            # Certificates
└── docker-compose.yml
```

## Implementation Steps

1. Create configuration directory
2. Configure `traefik.yml` with entry points
3. Set up Let's Encrypt ACME
4. Configure Docker provider
5. Add middleware (security headers, rate limit)
6. Configure dashboard with auth
7. Update docker-compose.yml

Refer to [tech-stack guide](../../../../docs/tech-stacks/gateways/traefik.md) for detailed templates.

## Completion Checklist

- [ ] Static configuration created
- [ ] HTTPS with Let's Encrypt configured
- [ ] Docker provider enabled
- [ ] Middleware configured
- [ ] Dashboard secured
- [ ] Services routing correctly

---

**Note**: Refer to [complete documentation](../../../../docs/tech-stacks/gateways/traefik.md) for implementation details.
