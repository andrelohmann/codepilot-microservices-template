# Scaffold Traefik Gateway Service

**Objective:** Create a production-ready Traefik reverse proxy and API gateway with complete configuration, SSL/TLS, service discovery, and monitoring.

---

## 📋 Service Specification

Before running this prompt, gather the following information:

1. **Service Name:** `[e.g., gateway-traefik-main, gateway-traefik-api]`
2. **Purpose:** `[Brief description - main gateway, internal gateway, etc.]`
3. **Entry Points:** `[HTTP: 80, HTTPS: 443, or custom ports]`
4. **Backend Services:** `[List of services to route to]`
5. **SSL/TLS:** `[Let's Encrypt, custom certificates, or none]`
6. **Load Balancing:** `[Round-robin, weighted, least-connections]`
7. **Authentication:** `[Basic Auth, OAuth, JWT, or none]`

---

## 🏗️ Architecture Overview

```
┌────────────────────────────────────────────────────────────────────┐
│                    Traefik Gateway Architecture                    │
│                                                                     │
│                          ┌──────────────┐                          │
│                          │   Internet   │                          │
│                          └──────┬───────┘                          │
│                                 │                                  │
│                                 ↓                                  │
│                    ┌────────────────────────┐                      │
│                    │    Entry Points        │                      │
│                    │  HTTP:80  HTTPS:443    │                      │
│                    └──────────┬─────────────┘                      │
│                               │                                    │
│                               ↓                                    │
│                    ┌────────────────────────┐                      │
│                    │   Traefik (Proxy)      │                      │
│                    │  ┌──────────────────┐  │                      │
│                    │  │  Router Rules    │  │                      │
│                    │  │  - Host()        │  │                      │
│                    │  │  - PathPrefix()  │  │                      │
│                    │  │  - Headers()     │  │                      │
│                    │  └──────────────────┘  │                      │
│                    │  ┌──────────────────┐  │                      │
│                    │  │   Middleware     │  │                      │
│                    │  │  - Auth          │  │                      │
│                    │  │  - Rate Limit    │  │                      │
│                    │  │  - Compress      │  │                      │
│                    │  │  - Headers       │  │                      │
│                    │  └──────────────────┘  │                      │
│                    │  ┌──────────────────┐  │                      │
│                    │  │  TLS/Let's       │  │                      │
│                    │  │  Encrypt         │  │                      │
│                    │  └──────────────────┘  │                      │
│                    └──────────┬─────────────┘                      │
│                               │                                    │
│         ┌─────────────────────┼─────────────────────┐              │
│         │                     │                     │              │
│         ↓                     ↓                     ↓              │
│  ┌────────────┐        ┌────────────┐       ┌────────────┐        │
│  │ Service A  │        │ Service B  │       │ Service C  │        │
│  │  (API)     │        │ (Frontend) │       │  (Worker)  │        │
│  └────────────┘        └────────────┘       └────────────┘        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📁 Directory Structure

Create the following complete directory structure:

```
services/[service-name]/
├── config/
│   ├── traefik.yml              ← Static configuration
│   ├── dynamic/                 ← Dynamic configuration
│   │   ├── routers.yml          ← Router definitions
│   │   ├── middlewares.yml      ← Middleware definitions
│   │   └── services.yml         ← Service definitions
│   └── acme.json                ← Let's Encrypt certificates (created at runtime)
│
├── certs/                       ← Custom SSL certificates
│   ├── cert.pem
│   └── key.pem
│
├── logs/                        ← Access and error logs
│   └── .gitkeep
│
├── .devcontainer/
│   └── [service-name]-container/
│       └── devcontainer.json
│
├── docker-compose.yml
├── .env.example
├── .gitignore
└── README.md
```

---

## 🔨 Implementation Steps

### Step 1: Create config/traefik.yml (Static Configuration)

```yaml
# Static Configuration
global:
  checkNewVersion: true
  sendAnonymousUsage: false

# API and Dashboard
api:
  dashboard: true
  insecure: false  # Set to true for development only

# Entry Points
entryPoints:
  web:
    address: ":80"
    http:
      redirections:
        entryPoint:
          to: websecure
          scheme: https
          permanent: true

  websecure:
    address: ":443"
    http:
      tls:
        certResolver: letsencrypt
        domains:
          - main: example.com
            sans:
              - "*.example.com"

  traefik:
    address: ":8080"

# Certificate Resolvers
certificatesResolvers:
  letsencrypt:
    acme:
      email: admin@example.com
      storage: /etc/traefik/acme.json
      caServer: https://acme-v02.api.letsencrypt.org/directory
      # Use HTTP challenge
      httpChallenge:
        entryPoint: web
      # Or use DNS challenge (requires DNS provider)
      # dnsChallenge:
      #   provider: cloudflare
      #   delayBeforeCheck: 0

# Providers
providers:
  # File provider for static configuration
  file:
    directory: /etc/traefik/dynamic
    watch: true

  # Docker provider for automatic service discovery
  docker:
    endpoint: "unix:///var/run/docker.sock"
    exposedByDefault: false
    network: microservices
    watch: true

# Logging
log:
  level: INFO
  filePath: /var/log/traefik/traefik.log
  format: json

# Access Logs
accessLog:
  filePath: /var/log/traefik/access.log
  format: json
  bufferingSize: 100
  filters:
    statusCodes:
      - "200-599"

# Metrics
metrics:
  prometheus:
    addEntryPointsLabels: true
    addRoutersLabels: true
    addServicesLabels: true
    entryPoint: traefik
    buckets:
      - 0.1
      - 0.3
      - 1.2
      - 5.0

# Tracing (optional)
# tracing:
#   jaeger:
#     samplingServerURL: http://jaeger:5778/sampling
#     localAgentHostPort: jaeger:6831
```

### Step 2: Create config/dynamic/routers.yml

```yaml
# HTTP Routers
http:
  routers:
    # API Service Router
    api-router:
      rule: "Host(`api.example.com`) || PathPrefix(`/api`)"
      service: api-service
      middlewares:
        - auth
        - rate-limit
        - compress
        - secure-headers
      tls:
        certResolver: letsencrypt
      priority: 100

    # Frontend Router
    frontend-router:
      rule: "Host(`example.com`) || Host(`www.example.com`)"
      service: frontend-service
      middlewares:
        - compress
        - secure-headers
      tls:
        certResolver: letsencrypt
      priority: 90

    # Admin Dashboard Router
    admin-router:
      rule: "Host(`admin.example.com`)"
      service: admin-service
      middlewares:
        - auth
        - admin-whitelist
        - secure-headers
      tls:
        certResolver: letsencrypt
      priority: 100

    # WebSocket Router
    websocket-router:
      rule: "Host(`ws.example.com`)"
      service: websocket-service
      middlewares:
        - secure-headers
      tls:
        certResolver: letsencrypt
      priority: 95

    # Health Check Router (no TLS)
    health-router:
      rule: "Host(`health.example.com`) && Path(`/health`)"
      service: health-service
      entryPoints:
        - web
      priority: 150

# TCP Routers (for non-HTTP services)
tcp:
  routers:
    # Database Router (example)
    postgres-router:
      rule: "HostSNI(`db.example.com`)"
      service: postgres-service
      tls:
        passthrough: true
```

### Step 3: Create config/dynamic/middlewares.yml

```yaml
http:
  middlewares:
    # Authentication
    auth:
      basicAuth:
        users:
          - "admin:$apr1$H6uskkkW$IgXLP6ewTrSuBkTrqE8wj/"  # admin:admin (change in production!)
        # Or use OAuth/JWT
        # forwardAuth:
        #   address: "http://auth-service:4181"
        #   authResponseHeaders:
        #     - "X-Auth-User"

    # Rate Limiting
    rate-limit:
      rateLimit:
        average: 100
        burst: 50
        period: 1s
        sourceCriterion:
          ipStrategy:
            depth: 1

    # Compression
    compress:
      compress:
        excludedContentTypes:
          - text/event-stream

    # Security Headers
    secure-headers:
      headers:
        browserXssFilter: true
        contentTypeNosniff: true
        forceSTSHeader: true
        stsIncludeSubdomains: true
        stsPreload: true
        stsSeconds: 31536000
        customFrameOptionsValue: "SAMEORIGIN"
        customResponseHeaders:
          X-Powered-By: ""
          Server: ""
        contentSecurityPolicy: "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'"

    # CORS
    cors:
      headers:
        accessControlAllowMethods:
          - GET
          - POST
          - PUT
          - DELETE
          - OPTIONS
        accessControlAllowHeaders:
          - "*"
        accessControlAllowOriginList:
          - "https://example.com"
          - "https://www.example.com"
        accessControlMaxAge: 100
        addVaryHeader: true

    # IP Whitelist
    admin-whitelist:
      ipWhiteList:
        sourceRange:
          - "127.0.0.1/32"
          - "192.168.1.0/24"
          - "10.0.0.0/8"

    # Redirect to HTTPS
    redirect-https:
      redirectScheme:
        scheme: https
        permanent: true

    # Strip Prefix
    strip-api-prefix:
      stripPrefix:
        prefixes:
          - "/api/v1"
          - "/api"

    # Add Prefix
    add-prefix:
      addPrefix:
        prefix: "/api"

    # Retry
    retry:
      retry:
        attempts: 3
        initialInterval: 100ms

    # Circuit Breaker
    circuit-breaker:
      circuitBreaker:
        expression: "NetworkErrorRatio() > 0.30"

    # Request/Response Buffering
    buffering:
      buffering:
        maxRequestBodyBytes: 10485760  # 10MB
        memRequestBodyBytes: 2097152   # 2MB
        maxResponseBodyBytes: 10485760
        memResponseBodyBytes: 2097152
```

### Step 4: Create config/dynamic/services.yml

```yaml
http:
  services:
    # API Service
    api-service:
      loadBalancer:
        servers:
          - url: "http://api-fastapi-auth:8000"
          - url: "http://api-nestjs-billing:3000"
        healthCheck:
          path: /health
          interval: 30s
          timeout: 5s
        sticky:
          cookie:
            name: server_id
            secure: true
            httpOnly: true

    # Frontend Service
    frontend-service:
      loadBalancer:
        servers:
          - url: "http://frontend-react-app:3000"
        healthCheck:
          path: /
          interval: 30s
          timeout: 5s

    # Admin Service
    admin-service:
      loadBalancer:
        servers:
          - url: "http://admin-panel:4000"

    # WebSocket Service
    websocket-service:
      loadBalancer:
        servers:
          - url: "http://websocket-server:8080"

    # Health Check Service
    health-service:
      loadBalancer:
        servers:
          - url: "http://health-service:5000"

tcp:
  services:
    # PostgreSQL Service
    postgres-service:
      loadBalancer:
        servers:
          - address: "postgres:5432"
```

### Step 5: Create docker-compose.yml

```yaml
version: '3.8'

services:
  traefik:
    image: traefik:v2.10
    container_name: [service-name]
    restart: unless-stopped
    command:
      - "--configFile=/etc/traefik/traefik.yml"
    ports:
      - "80:80"       # HTTP
      - "443:443"     # HTTPS
      - "8080:8080"   # Dashboard
    volumes:
      # Configuration
      - ./config/traefik.yml:/etc/traefik/traefik.yml:ro
      - ./config/dynamic:/etc/traefik/dynamic:ro
      
      # Certificates
      - ./config/acme.json:/etc/traefik/acme.json
      - ./certs:/etc/traefik/certs:ro
      
      # Logs
      - ./logs:/var/log/traefik
      
      # Docker socket for service discovery
      - /var/run/docker.sock:/var/run/docker.sock:ro
    environment:
      - TZ=UTC
      # DNS Provider credentials (if using DNS challenge)
      # - CLOUDFLARE_EMAIL=${CLOUDFLARE_EMAIL}
      # - CLOUDFLARE_API_KEY=${CLOUDFLARE_API_KEY}
    networks:
      - microservices
    labels:
      # Enable Traefik for itself (dashboard)
      - "traefik.enable=true"
      
      # Dashboard router
      - "traefik.http.routers.dashboard.rule=Host(`traefik.example.com`)"
      - "traefik.http.routers.dashboard.service=api@internal"
      - "traefik.http.routers.dashboard.entrypoints=websecure"
      - "traefik.http.routers.dashboard.tls.certresolver=letsencrypt"
      
      # Dashboard authentication
      - "traefik.http.routers.dashboard.middlewares=dashboard-auth"
      - "traefik.http.middlewares.dashboard-auth.basicauth.users=admin:$$apr1$$H6uskkkW$$IgXLP6ewTrSuBkTrqE8wj/"
    healthcheck:
      test: ["CMD", "traefik", "healthcheck", "--ping"]
      interval: 30s
      timeout: 5s
      retries: 3

networks:
  microservices:
    external: true
```

### Step 6: Create .env.example

```bash
# Traefik Configuration
TRAEFIK_LOG_LEVEL=INFO
TRAEFIK_DASHBOARD_ENABLED=true

# Let's Encrypt
ACME_EMAIL=admin@example.com
ACME_CA_SERVER=https://acme-v02.api.letsencrypt.org/directory

# DNS Provider (if using DNS challenge)
CLOUDFLARE_EMAIL=
CLOUDFLARE_API_KEY=

# Dashboard Authentication
DASHBOARD_USER=admin
DASHBOARD_PASSWORD=changeme

# Domain
DOMAIN=example.com
```

### Step 7: Create Example Service with Traefik Labels

```yaml
# Example: docker-compose for a backend service

version: '3.8'

services:
  api-fastapi-auth:
    image: api-fastapi-auth:latest
    networks:
      - microservices
    labels:
      # Enable Traefik
      - "traefik.enable=true"
      
      # HTTP Router
      - "traefik.http.routers.api-auth.rule=Host(`api.example.com`) && PathPrefix(`/auth`)"
      - "traefik.http.routers.api-auth.entrypoints=websecure"
      - "traefik.http.routers.api-auth.tls.certresolver=letsencrypt"
      
      # Service
      - "traefik.http.services.api-auth.loadbalancer.server.port=8000"
      
      # Middleware
      - "traefik.http.routers.api-auth.middlewares=rate-limit,compress,secure-headers"
      
      # Health Check
      - "traefik.http.services.api-auth.loadbalancer.healthcheck.path=/health"
      - "traefik.http.services.api-auth.loadbalancer.healthcheck.interval=30s"

networks:
  microservices:
    external: true
```

### Step 8: Create setup script

```bash
#!/bin/bash
# scripts/setup.sh

set -e

echo "Setting up Traefik..."

# Create acme.json with correct permissions
touch config/acme.json
chmod 600 config/acme.json

# Create logs directory
mkdir -p logs

# Create Docker network if it doesn't exist
docker network inspect microservices >/dev/null 2>&1 || \
    docker network create microservices

echo "Traefik setup complete!"
echo "Run 'docker-compose up -d' to start Traefik"
```

### Step 9: Create .gitignore

```
# Logs
logs/*.log
logs/*.json

# Certificates
config/acme.json
certs/*.pem
certs/*.key

# Environment
.env
.env.local

# Temporary files
*.tmp
*.bak
```

### Step 10: Create .devcontainer/[service-name]-container/devcontainer.json

```json
{
  "name": "[service-name]",
  "dockerComposeFile": [
    "../../docker-compose.yml"
  ],
  "service": "traefik",
  "workspaceFolder": "/workspace",
  "customizations": {
    "vscode": {
      "extensions": [
        "redhat.vscode-yaml",
        "ms-azuretools.vscode-docker"
      ]
    }
  },
  "forwardPorts": [80, 443, 8080]
}
```

### Step 11: Create README.md

```markdown
# Traefik Gateway

Modern reverse proxy and load balancer with automatic SSL/TLS certificate management.

## Features

- Automatic HTTPS with Let's Encrypt
- Service discovery with Docker labels
- Load balancing with health checks
- Rate limiting and authentication
- Security headers
- Prometheus metrics
- Web dashboard
- Access logging

## Prerequisites

- Docker & Docker Compose
- Domain name (for Let's Encrypt)
- Ports 80, 443, 8080 available

## Quick Start

1. Run setup script:
```bash
chmod +x scripts/setup.sh
./scripts/setup.sh
```

2. Configure domains:
```bash
cp .env.example .env
# Edit .env with your domain and email
```

3. Start Traefik:
```bash
docker-compose up -d
```

4. Access dashboard:
```
https://traefik.example.com
```

## Configuration

### Static Configuration
Located in `config/traefik.yml`. Restart required for changes.

### Dynamic Configuration
Located in `config/dynamic/`. Changes are hot-reloaded.

## Adding Services

Add Traefik labels to your service's docker-compose.yml:

```yaml
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.myservice.rule=Host(`myservice.example.com`)"
  - "traefik.http.routers.myservice.entrypoints=websecure"
  - "traefik.http.routers.myservice.tls.certresolver=letsencrypt"
  - "traefik.http.services.myservice.loadbalancer.server.port=8000"
```

## Monitoring

### Dashboard
Access at: `https://traefik.example.com`

### Prometheus Metrics
Available at: `http://localhost:8080/metrics`

### Access Logs
Located in: `logs/access.log`

## Security

### Dashboard Authentication
Default credentials (change in production):
- Username: admin
- Password: admin

Update in `config/traefik.yml` or via environment variables.

### SSL/TLS
- Automatic certificates from Let's Encrypt
- HTTP to HTTPS redirect enabled
- HSTS headers configured

### Rate Limiting
Configured in `config/dynamic/middlewares.yml`:
- 100 requests/second average
- 50 requests burst

## Troubleshooting

### Certificate Issues
```bash
# Check acme.json permissions
ls -l config/acme.json  # Should be -rw-------

# View certificate status
docker-compose logs traefik | grep -i acme
```

### Service Not Accessible
```bash
# Check router rules
docker-compose exec traefik traefik --entryPoints.web.address=:80 --api.dashboard=true healthcheck

# View configuration
curl http://localhost:8080/api/rawdata
```

### Let's Encrypt Rate Limits
Use staging server for testing:
```yaml
caServer: https://acme-staging-v02.api.letsencrypt.org/directory
```

## Production Checklist

- [ ] Change dashboard credentials
- [ ] Configure real email for Let's Encrypt
- [ ] Set appropriate rate limits
- [ ] Configure IP whitelists for admin routes
- [ ] Enable access logging
- [ ] Set up log rotation
- [ ] Configure backup for acme.json
- [ ] Test SSL certificate renewal
- [ ] Configure monitoring alerts
- [ ] Review security headers

## File Structure

```
├── config/
│   ├── traefik.yml        # Static configuration
│   ├── dynamic/           # Dynamic configuration
│   │   ├── routers.yml
│   │   ├── middlewares.yml
│   │   └── services.yml
│   └── acme.json          # Let's Encrypt certificates
├── logs/                  # Access and error logs
└── docker-compose.yml
```

## Useful Commands

```bash
# View logs
docker-compose logs -f traefik

# Restart Traefik
docker-compose restart traefik

# Validate configuration
docker-compose exec traefik traefik version

# Check health
curl http://localhost:8080/ping
```

## License

[Your License]
```

---

## ✅ Completion Checklist

- [ ] All configuration files created
- [ ] traefik.yml static configuration complete
- [ ] Dynamic configuration files (routers, middlewares, services) created
- [ ] Docker Compose configuration complete
- [ ] acme.json created with correct permissions (600)
- [ ] Network 'microservices' created
- [ ] .env.example configured
- [ ] Setup script created and executable
- [ ] .gitignore configured
- [ ] README.md with documentation
- [ ] DevContainer configuration
- [ ] Dashboard accessible
- [ ] SSL certificates obtained
- [ ] Services routing correctly
- [ ] Health checks working
- [ ] Metrics endpoint accessible

---

**Next Steps:**
1. Configure your domain DNS to point to the server
2. Update Let's Encrypt email address
3. Change dashboard credentials
4. Add backend services with Traefik labels
5. Configure monitoring and alerts
6. Set up log aggregation
7. Test certificate renewal process
8. Configure backup for acme.json
