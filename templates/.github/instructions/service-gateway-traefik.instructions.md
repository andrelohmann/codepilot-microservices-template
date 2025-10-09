---
applyTo:
  - "services/gateway-traefik-*/**"
---

# Traefik Gateway Service Instructions

Apply to Traefik reverse proxy and load balancer configurations.

## Technology Stack

- **Proxy:** Traefik v2.10+
- **Configuration:** YAML/TOML static + Docker labels dynamic
- **TLS:** Let's Encrypt ACME or manual certificates
- **Middleware:** Built-in and custom
- **Monitoring:** Prometheus metrics, dashboard

## Configuration Structure

```
services/gateway-traefik-{purpose}/
├── config/
│   ├── traefik.yml          ← Static configuration
│   ├── dynamic/
│   │   ├── middlewares.yml  ← Middleware definitions
│   │   ├── routers.yml      ← HTTP routers
│   │   └── services.yml     ← Backend services
│   └── acme/                ← Let's Encrypt storage
├── docker-compose.yml       ← Service definition
└── README.md
```

## Core Patterns

**Static Configuration (traefik.yml):**
- Entry points: HTTP (80), HTTPS (443), custom ports
- Certificate resolvers for Let's Encrypt
- Providers: Docker, file, Kubernetes
- API/Dashboard configuration
- Logging level and access logs

**Dynamic Configuration:**
- HTTP routers with path/host rules
- TCP/UDP routers for non-HTTP services
- Services with load balancing strategies
- Middleware chains per router
- TLS options and certificates

**Router Rules:**
- Host matching: `Host(`example.com`)`
- Path matching: `PathPrefix(`/api`)`
- Method matching: `Method(`GET`, `POST`)`
- Headers matching: `Headers(`X-Custom`, `value`)`
- Combine with `&&` and `||` operators

**Middleware Chain:**
- Order matters: auth → rate limit → compress → strip prefix
- Common middlewares:
  - `stripPrefix` - Remove path segments
  - `addPrefix` - Add path segments
  - `rateLimit` - Request rate limiting
  - `basicAuth` - Basic authentication
  - `compress` - Response compression
  - `headers` - Custom headers
  - `redirectScheme` - HTTP to HTTPS redirect

**Load Balancing:**
- Strategies: `roundrobin` (default), `wrr` (weighted), `leastconn`
- Health checks with interval and timeout
- Sticky sessions with cookies
- Circuit breakers for failing backends

**TLS Configuration:**
- Automatic HTTPS with Let's Encrypt
- HTTP challenge or DNS challenge
- Certificate storage in acme.json
- Custom certificates via file provider
- TLS versions and cipher suites

**Service Discovery:**
- Docker labels for automatic discovery
- File provider for manual configuration
- Service updates without restart
- Watch mode for configuration changes

## Docker Integration

**Traefik Labels Pattern:**
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

**Middleware Labels:**
```yaml
- "traefik.http.middlewares.api-auth.basicauth.users=user:$$apr1$$..."
- "traefik.http.middlewares.api-ratelimit.ratelimit.average=100"
- "traefik.http.routers.api.middlewares=api-auth,api-ratelimit"
```

## Security Best Practices

- Enable HTTPS redirect for all services
- Use Let's Encrypt for automatic certificates
- Set secure headers middleware globally
- Rate limiting per service
- Basic auth for admin dashboard
- Whitelist IP addresses for sensitive endpoints
- Keep Traefik updated

**Secure Headers Middleware:**
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

## Monitoring & Observability

- Enable Prometheus metrics endpoint
- Access logs to stdout/file
- Dashboard for visual overview
- Health check endpoint
- Metrics: requests, duration, errors by service

## Anti-Patterns

❌ Exposing dashboard without authentication  
❌ Not setting up HTTPS redirect  
❌ Ignoring health checks for backends  
❌ Too many middlewares causing latency  
❌ Not backing up acme.json certificates  
❌ Using `insecureSkipVerify` in production  
❌ Hardcoding service IPs instead of discovery  
❌ Not monitoring Traefik metrics  

## Common Use Cases

**1. API Gateway:**
- Route `/api/v1/users` → `user-service:8000`
- Route `/api/v1/orders` → `order-service:8001`
- Add auth middleware globally
- Rate limit per endpoint

**2. Multi-Domain Hosting:**
- `app1.example.com` → `service1`
- `app2.example.com` → `service2`
- Separate certificates per domain
- Path-based routing within domains

**3. WebSocket Support:**
- Specific router for WS connections
- Sticky sessions enabled
- Increased timeouts
- Connection upgrade headers

**4. Canary Deployments:**
- Weighted load balancing
- Route 90% to stable, 10% to canary
- Header-based routing for testing
- Gradual traffic shift

## Required Environment Variables

- `TRAEFIK_DOMAIN` - Primary domain
- `LETSENCRYPT_EMAIL` - Email for Let's Encrypt
- `TRAEFIK_DASHBOARD_AUTH` - Basic auth credentials
- `LOG_LEVEL` - debug/info/warn/error

## Testing

- Test routing rules with curl
- Verify TLS certificate validity
- Check middleware application order
- Load test with different strategies
- Monitor metrics during traffic spikes

## Docker Compose Integration

Mount:
- `/var/run/docker.sock` - Docker socket (read-only)
- `./config/traefik.yml:/etc/traefik/traefik.yml`
- `./config/dynamic:/etc/traefik/dynamic`
- `./config/acme:/acme`

Expose:
- `80:80` - HTTP
- `443:443` - HTTPS
- `8080:8080` - Dashboard (behind auth)

Networks:
- Connect to service networks
- Create dedicated `traefik` network
