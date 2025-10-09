---
applyTo: "**/gateway-traefik-*/**"
description: Traefik reverse proxy and load balancer with dynamic configuration and service discovery
---

# Traefik Gateway Service Instructions

Apply to Traefik reverse proxy and load balancer configurations.

**Reference**: [Complete Traefik Tech Stack Documentation](../../../docs/tech-stacks/gateways/traefik.md)  
**Scaffold**: Use the `scaffold-gateway-traefik-service.prompt.md` for new services

## Configuration Structure

```
services/gateway-traefik-{purpose}/
├── config/
│   ├── traefik.yml          # Static configuration
│   ├── dynamic/
│   │   ├── middlewares.yml
│   │   ├── routers.yml
│   │   └── services.yml
│   └── acme/               # Let's Encrypt
└── docker-compose.yml
```

## Quick Reference Patterns

### 1. Docker Labels (Service Discovery)

```yaml
services:
  api:
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.api.rule=Host(`api.example.com`)"
      - "traefik.http.routers.api.entrypoints=websecure"
      - "traefik.http.routers.api.tls.certresolver=letsencrypt"
      - "traefik.http.services.api.loadbalancer.server.port=8000"
```

### 2. Middleware Chain

```yaml
# Middleware labels
- "traefik.http.middlewares.api-auth.basicauth.users=user:$$apr1$$..."
- "traefik.http.middlewares.api-ratelimit.ratelimit.average=100"
- "traefik.http.middlewares.api-strip.stripprefix.prefixes=/api"
- "traefik.http.routers.api.middlewares=api-auth,api-ratelimit,api-strip"
```

### 3. Router Rules

```yaml
# Host matching
Host(`example.com`)

# Path matching
PathPrefix(`/api`)

# Combination
Host(`api.example.com`) && PathPrefix(`/v1`)

# Method + Header
Method(`GET`,`POST`) && Headers(`X-API-Key`, `secret`)
```

### 4. Secure Headers Middleware

```yaml
http:
  middlewares:
    secure-headers:
      headers:
        frameDeny: true
        contentTypeNosniff: true
        browserXssFilter: true
        stsSeconds: 31536000
        stsIncludeSubdomains: true
```

## Code Style

- Static config in `traefik.yml` (entry points, providers, ACME)
- Dynamic config in `dynamic/` files or Docker labels
- Use Docker labels for service discovery
- Middleware chain order: auth → rate limit → compress → strip prefix
- Load balancing strategies: roundrobin (default), wrr, leastconn
- Always enable HTTPS redirect
- Let's Encrypt for automatic certificates
- Health checks for backends
- Enable Prometheus metrics
- Secure dashboard with basic auth

## Common Use Cases

**API Gateway**: Route `/api/v1/users` → `user-service:8000` with auth/rate limiting  
**Multi-Domain**: `app1.example.com` → `service1`, `app2.example.com` → `service2`  
**WebSocket**: Sticky sessions + increased timeouts  
**Canary**: Weighted load balancing (90% stable, 10% canary)

## Anti-Patterns

❌ Exposing dashboard without authentication  
❌ Not setting up HTTPS redirect  
❌ Ignoring health checks for backends  
❌ Not backing up acme.json certificates  
❌ Using `insecureSkipVerify` in production  
❌ Hardcoding service IPs instead of discovery
