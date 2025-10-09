---
description: "Scaffold an Nginx gateway service"
mode: agent
tools: [file, workspace, terminal]
---

# Scaffold Nginx Gateway Service

Create a production-ready `${input:serviceName}` reverse proxy using Nginx with load balancing, SSL/TLS, and caching.

## Reference Documentation

- **[Nginx Tech-Stack Guide](../../../../docs/tech-stacks/gateways/nginx.md)** - Complete implementation details
- **[Nginx Instructions](../instructions/service-gateway-nginx.instructions.md)** - Copilot guidance

## Service Configuration

Ask the user for:
- **Service Name**: `${input:serviceName}` (e.g., `gateway-nginx-main`)
- **Domain**: `${input:domain:example.com}`
- **SSL**: `${input:ssl:letsencrypt}` (letsencrypt, manual, none)

## Project Structure

```
services/${input:serviceName}/
├── nginx/
│   ├── nginx.conf
│   ├── conf.d/
│   ├── ssl/
│   └── snippets/
└── Dockerfile
```

## Implementation Steps

1. Create nginx configuration directory
2. Configure `nginx.conf` with workers, gzip
3. Create server blocks in `conf.d/`
4. Configure upstream load balancing
5. Set up SSL/TLS certificates
6. Add security headers
7. Configure rate limiting
8. Create Dockerfile
9. Update docker-compose.yml

Refer to [tech-stack guide](../../../../docs/tech-stacks/gateways/nginx.md) for detailed templates.

## Completion Checklist

- [ ] Nginx configuration created
- [ ] Server blocks configured
- [ ] Load balancing set up
- [ ] SSL/TLS configured
- [ ] Security headers added
- [ ] Services routing correctly

---

**Note**: Refer to [complete documentation](../../../../docs/tech-stacks/gateways/nginx.md) for implementation details.
