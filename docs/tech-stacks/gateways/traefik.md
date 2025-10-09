# Traefik - Modern Reverse Proxy & Load Balancer

Cloud-native edge router with automatic service discovery, Let's Encrypt SSL, and dynamic configuration.

---

## Overview

**Naming:** `gateway-traefik-{purpose}`

**Examples:** `gateway-traefik-main`, `gateway-traefik-internal`, `gateway-traefik-public`

**Traefik** is a modern reverse proxy designed for microservices with Docker/Kubernetes integration and automatic SSL certificates.

---

## Technology Stack

- **Traefik v2.10+** - Reverse proxy
- **Let's Encrypt ACME** - Automatic SSL
- **Docker provider** - Service discovery
- **Prometheus** - Metrics
- **Dashboard** - Web UI

---

## Features

✅ **Automatic SSL** - Let's Encrypt integration  
✅ **Service discovery** - Docker labels  
✅ **Dynamic configuration** - No reload needed  
✅ **Load balancing** - Multiple algorithms  
✅ **Middleware chains** - Auth, rate-limit, compress  
✅ **WebSocket support** - Native support  
✅ **Dashboard UI** - Real-time monitoring  
✅ **Health checks** - Automatic failover

---

## Ideal Use Cases

✅ **Docker environments** - Native integration  
✅ **Microservices** - Dynamic service discovery  
✅ **Automatic SSL** - Let's Encrypt without hassle  
✅ **Modern architecture** - Cloud-native design  
✅ **Zero-downtime updates** - Dynamic reconfiguration

---

## Configuration

### Static Configuration

```yaml
# traefik.yml
entryPoints:
  web:
    address: ":80"
    http:
      redirections:
        entryPoint:
          to: websecure
          scheme: https
  websecure:
    address: ":443"

certificatesResolvers:
  letsencrypt:
    acme:
      email: admin@example.com
      storage: /letsencrypt/acme.json
      httpChallenge:
        entryPoint: web

providers:
  docker:
    exposedByDefault: false
  file:
    filename: /config/dynamic.yml

api:
  dashboard: true

metrics:
  prometheus: {}
```

### Docker Labels (Service Discovery)

```yaml
# docker-compose.yml
services:
  api:
    image: my-api:latest
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.api.rule=Host(`api.example.com`)"
      - "traefik.http.routers.api.entrypoints=websecure"
      - "traefik.http.routers.api.tls.certresolver=letsencrypt"
      - "traefik.http.services.api.loadbalancer.server.port=8000"

  traefik:
    image: traefik:v2.10
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./traefik.yml:/traefik.yml:ro
      - ./letsencrypt:/letsencrypt
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.dashboard.rule=Host(`traefik.example.com`)"
      - "traefik.http.routers.dashboard.service=api@internal"
      - "traefik.http.routers.dashboard.middlewares=auth"
      - "traefik.http.middlewares.auth.basicauth.users=admin:$$apr1$$..."
```

### Middleware

```yaml
# dynamic.yml
http:
  middlewares:
    rate-limit:
      rateLimit:
        average: 100
        burst: 50

    compress:
      compress: {}

    auth:
      basicAuth:
        users:
          - "admin:$apr1$..."

    headers:
      headers:
        customResponseHeaders:
          X-Frame-Options: "SAMEORIGIN"
          X-Content-Type-Options: "nosniff"
```

---

## Common Patterns

### Multiple Services

```yaml
services:
  api-auth:
    labels:
      - "traefik.http.routers.auth.rule=Host(`api.example.com`) && PathPrefix(`/auth`)"
      - "traefik.http.services.auth.loadbalancer.server.port=8001"

  api-billing:
    labels:
      - "traefik.http.routers.billing.rule=Host(`api.example.com`) && PathPrefix(`/billing`)"
      - "traefik.http.services.billing.loadbalancer.server.port=8002"

  frontend:
    labels:
      - "traefik.http.routers.frontend.rule=Host(`app.example.com`)"
      - "traefik.http.services.frontend.loadbalancer.server.port=3000"
```

### Load Balancing

```yaml
# Weighted load balancing
labels:
  - "traefik.http.services.api.loadbalancer.server.port=8000"
  - "traefik.http.services.api.loadbalancer.sticky.cookie=true"
  - "traefik.http.services.api.loadbalancer.healthcheck.path=/health"
  - "traefik.http.services.api.loadbalancer.healthcheck.interval=10s"
```

### Rate Limiting

```yaml
labels:
  - "traefik.http.routers.api.middlewares=rate-limit@file"
  - "traefik.http.middlewares.rate-limit.ratelimit.average=100"
  - "traefik.http.middlewares.rate-limit.ratelimit.period=1m"
```

---

## Deployment

```dockerfile
# docker-compose.yml
version: '3.8'

services:
  traefik:
    image: traefik:v2.10
    container_name: traefik
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./traefik.yml:/traefik.yml:ro
      - ./dynamic.yml:/config/dynamic.yml:ro
      - ./letsencrypt:/letsencrypt
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.dashboard.rule=Host(`traefik.example.com`)"
      - "traefik.http.routers.dashboard.service=api@internal"
      - "traefik.http.routers.dashboard.entrypoints=websecure"
      - "traefik.http.routers.dashboard.tls.certresolver=letsencrypt"
```

---

## Monitoring

### Dashboard
Access at `https://traefik.example.com/dashboard/`

### Prometheus Metrics
```yaml
# Scrape config
- job_name: 'traefik'
  static_configs:
    - targets: ['traefik:8080']
```

---

## Best Practices

✅ Use Docker labels for configuration  
✅ Enable automatic SSL with Let's Encrypt  
✅ Implement health checks  
✅ Use middleware for security headers  
✅ Enable rate limiting  
✅ Monitor with Prometheus  
✅ Secure dashboard with auth  
✅ Use sticky sessions when needed

---

## Related Documentation

- [Gateways Overview](./README.md)
- [Nginx](./nginx.md)
- [API Technologies](../apis/README.md)

---

**Reference Files:**
- Instruction: `service-gateway-traefik.instructions.md`
- Scaffold: `scaffold-gateway-traefik-service.md`
