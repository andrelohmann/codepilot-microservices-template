# Scaffold Nginx Gateway Service

**Objective:** Create a production-ready Nginx reverse proxy and API gateway with complete configuration, SSL/TLS, load balancing, caching, and security.

---

## 📋 Service Specification

Before running this prompt, gather the following information:

1. **Service Name:** `[e.g., gateway-nginx-main, gateway-nginx-api]`
2. **Purpose:** `[Brief description - main gateway, internal gateway, etc.]`
3. **Ports:** `[HTTP: 80, HTTPS: 443, or custom ports]`
4. **Backend Services:** `[List of services to route to]`
5. **SSL/TLS:** `[Let's Encrypt with Certbot, custom certificates, or none]`
6. **Caching:** `[Yes/No - static assets, API responses]`
7. **Load Balancing:** `[Round-robin, least-connections, IP hash]`

---

## 🏗️ Architecture Overview

```
┌────────────────────────────────────────────────────────────────────┐
│                     Nginx Gateway Architecture                     │
│                                                                     │
│                          ┌──────────────┐                          │
│                          │   Internet   │                          │
│                          └──────┬───────┘                          │
│                                 │                                  │
│                                 ↓                                  │
│                    ┌────────────────────────┐                      │
│                    │    Nginx (Port 80/443) │                      │
│                    │  ┌──────────────────┐  │                      │
│                    │  │  Server Blocks   │  │                      │
│                    │  │  - api.*         │  │                      │
│                    │  │  - www.*         │  │                      │
│                    │  │  - admin.*       │  │                      │
│                    │  └──────────────────┘  │                      │
│                    │  ┌──────────────────┐  │                      │
│                    │  │   Location       │  │                      │
│                    │  │   Directives     │  │                      │
│                    │  │  - /api/*        │  │                      │
│                    │  │  - /static/*     │  │                      │
│                    │  └──────────────────┘  │                      │
│                    │  ┌──────────────────┐  │                      │
│                    │  │  Security        │  │                      │
│                    │  │  - Rate Limit    │  │                      │
│                    │  │  - Headers       │  │                      │
│                    │  │  - Basic Auth    │  │                      │
│                    │  └──────────────────┘  │                      │
│                    │  ┌──────────────────┐  │                      │
│                    │  │  Caching         │  │                      │
│                    │  │  - Proxy Cache   │  │                      │
│                    │  │  - FastCGI       │  │                      │
│                    │  └──────────────────┘  │                      │
│                    └──────────┬─────────────┘                      │
│                               │                                    │
│         ┌─────────────────────┼─────────────────────┐              │
│         │                     │                     │              │
│         ↓                     ↓                     ↓              │
│  ┌────────────┐        ┌────────────┐       ┌────────────┐        │
│  │ Service A  │        │ Service B  │       │ Service C  │        │
│  │  (API)     │        │ (Frontend) │       │ (Static)   │        │
│  └────────────┘        └────────────┘       └────────────┘        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📁 Directory Structure

Create the following complete directory structure:

```
services/[service-name]/
├── conf/
│   ├── nginx.conf               ← Main configuration
│   ├── conf.d/                  ← Server blocks
│   │   ├── default.conf
│   │   ├── api.conf
│   │   ├── frontend.conf
│   │   └── admin.conf
│   ├── snippets/                ← Reusable configs
│   │   ├── ssl.conf
│   │   ├── security.conf
│   │   ├── proxy.conf
│   │   └── cache.conf
│   └── upstream.conf            ← Upstream definitions
│
├── ssl/                         ← SSL certificates
│   ├── certs/
│   │   └── .gitkeep
│   └── private/
│       └── .gitkeep
│
├── www/                         ← Static files
│   ├── html/
│   │   ├── index.html
│   │   ├── 404.html
│   │   └── 50x.html
│   └── static/
│
├── cache/                       ← Cache storage
│   └── .gitkeep
│
├── logs/                        ← Access and error logs
│   └── .gitkeep
│
├── scripts/
│   ├── setup.sh                 ← Setup script
│   ├── renew-certs.sh           ← Certificate renewal
│   └── backup.sh                ← Backup script
│
├── .devcontainer/
│   └── [service-name]-container/
│       └── devcontainer.json
│
├── Dockerfile
├── docker-compose.yml
├── .env.example
├── .gitignore
└── README.md
```

---

## 🔨 Implementation Steps

### Step 1: Create conf/nginx.conf

```nginx
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

# Load dynamic modules
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 2048;
    use epoll;
    multi_accept on;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # Logging
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';
    
    log_format json escape=json '{'
        '"time":"$time_iso8601",'
        '"remote_addr":"$remote_addr",'
        '"request":"$request",'
        '"status":$status,'
        '"body_bytes_sent":$body_bytes_sent,'
        '"request_time":$request_time,'
        '"upstream_response_time":"$upstream_response_time",'
        '"http_referrer":"$http_referer",'
        '"http_user_agent":"$http_user_agent"'
    '}';

    access_log /var/log/nginx/access.log json;

    # Performance
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    client_max_body_size 20M;

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_types text/plain text/css text/xml text/javascript 
               application/json application/javascript application/xml+rss 
               application/rss+xml font/truetype font/opentype 
               application/vnd.ms-fontobject image/svg+xml;
    gzip_disable "msie6";

    # Security headers (global)
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    # Hide version
    server_tokens off;

    # Rate limiting zones
    limit_req_zone $binary_remote_addr zone=general:10m rate=10r/s;
    limit_req_zone $binary_remote_addr zone=api:10m rate=20r/s;
    limit_req_zone $binary_remote_addr zone=login:10m rate=1r/s;

    # Connection limiting
    limit_conn_zone $binary_remote_addr zone=addr:10m;

    # Proxy cache settings
    proxy_cache_path /var/cache/nginx/proxy 
                     levels=1:2 
                     keys_zone=proxy_cache:10m 
                     max_size=1g 
                     inactive=60m 
                     use_temp_path=off;

    # FastCGI cache settings
    fastcgi_cache_path /var/cache/nginx/fastcgi 
                       levels=1:2 
                       keys_zone=fastcgi_cache:10m 
                       max_size=1g 
                       inactive=60m 
                       use_temp_path=off;

    # Upstream definitions
    include /etc/nginx/upstream.conf;

    # Server blocks
    include /etc/nginx/conf.d/*.conf;
}
```

### Step 2: Create conf/upstream.conf

```nginx
# API Services
upstream api_backend {
    least_conn;  # or ip_hash for sticky sessions
    
    server api-fastapi-auth:8000 max_fails=3 fail_timeout=30s;
    server api-nestjs-billing:3000 max_fails=3 fail_timeout=30s;
    
    keepalive 32;
}

# Frontend Services
upstream frontend_backend {
    server frontend-react-app:3000 max_fails=3 fail_timeout=30s;
    server frontend-react-app-2:3000 backup;
    
    keepalive 16;
}

# Admin Services
upstream admin_backend {
    server admin-panel:4000;
}

# WebSocket Services
upstream websocket_backend {
    ip_hash;  # Sticky sessions for WebSocket
    server websocket-server:8080;
    server websocket-server-2:8080 backup;
}
```

### Step 3: Create conf/snippets/ssl.conf

```nginx
# SSL/TLS Configuration
ssl_protocols TLSv1.2 TLSv1.3;
ssl_prefer_server_ciphers on;
ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384;
ssl_ecdh_curve secp384r1;

# SSL session cache
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
ssl_session_tickets off;

# OCSP stapling
ssl_stapling on;
ssl_stapling_verify on;
resolver 8.8.8.8 8.8.4.4 valid=300s;
resolver_timeout 5s;

# HSTS
add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;
```

### Step 4: Create conf/snippets/security.conf

```nginx
# Security headers
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Referrer-Policy "no-referrer-when-downgrade" always;
add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'" always;

# Remove powered by headers
more_clear_headers 'Server';
more_clear_headers 'X-Powered-By';
```

### Step 5: Create conf/snippets/proxy.conf

```nginx
# Proxy headers
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header X-Forwarded-Host $host;
proxy_set_header X-Forwarded-Port $server_port;

# Proxy timeouts
proxy_connect_timeout 60s;
proxy_send_timeout 60s;
proxy_read_timeout 60s;

# Proxy buffering
proxy_buffering on;
proxy_buffer_size 4k;
proxy_buffers 8 4k;
proxy_busy_buffers_size 8k;

# HTTP version
proxy_http_version 1.1;
proxy_set_header Connection "";
```

### Step 6: Create conf/snippets/cache.conf

```nginx
# Cache configuration
proxy_cache_valid 200 302 10m;
proxy_cache_valid 404 1m;
proxy_cache_use_stale error timeout updating http_500 http_502 http_503 http_504;
proxy_cache_background_update on;
proxy_cache_lock on;

# Cache bypass
proxy_cache_bypass $http_cache_control;
add_header X-Cache-Status $upstream_cache_status;
```

### Step 7: Create conf/conf.d/api.conf

```nginx
# API Gateway
server {
    listen 80;
    listen [::]:80;
    server_name api.example.com;

    # Redirect to HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name api.example.com;

    # SSL certificates
    ssl_certificate /etc/nginx/ssl/certs/api.example.com.crt;
    ssl_certificate_key /etc/nginx/ssl/private/api.example.com.key;
    
    # Include SSL config
    include /etc/nginx/snippets/ssl.conf;
    include /etc/nginx/snippets/security.conf;

    # Logging
    access_log /var/log/nginx/api.access.log json;
    error_log /var/log/nginx/api.error.log warn;

    # Rate limiting
    limit_req zone=api burst=20 nodelay;
    limit_conn addr 10;

    # API routes
    location /api/ {
        include /etc/nginx/snippets/proxy.conf;
        
        # CORS headers
        add_header 'Access-Control-Allow-Origin' '*' always;
        add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, OPTIONS' always;
        add_header 'Access-Control-Allow-Headers' 'Authorization, Content-Type' always;
        
        if ($request_method = 'OPTIONS') {
            return 204;
        }
        
        # Proxy to backend
        proxy_pass http://api_backend;
        
        # No caching for API
        proxy_cache_bypass 1;
        proxy_no_cache 1;
    }

    # Health check (no auth)
    location /health {
        access_log off;
        return 200 "healthy\n";
        add_header Content-Type text/plain;
    }

    # Metrics (restricted)
    location /metrics {
        allow 10.0.0.0/8;
        allow 172.16.0.0/12;
        allow 192.168.0.0/16;
        deny all;
        
        proxy_pass http://api_backend;
    }
}
```

### Step 8: Create conf/conf.d/frontend.conf

```nginx
# Frontend Application
server {
    listen 80;
    listen [::]:80;
    server_name example.com www.example.com;

    # Redirect to HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name example.com www.example.com;

    # SSL certificates
    ssl_certificate /etc/nginx/ssl/certs/example.com.crt;
    ssl_certificate_key /etc/nginx/ssl/private/example.com.key;
    
    include /etc/nginx/snippets/ssl.conf;
    include /etc/nginx/snippets/security.conf;

    # Logging
    access_log /var/log/nginx/frontend.access.log json;
    error_log /var/log/nginx/frontend.error.log warn;

    # Rate limiting
    limit_req zone=general burst=10 nodelay;

    # Root
    root /usr/share/nginx/html;
    index index.html;

    # Static files with caching
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
        access_log off;
    }

    # Proxy to frontend
    location / {
        include /etc/nginx/snippets/proxy.conf;
        proxy_pass http://frontend_backend;
        
        # Caching
        proxy_cache proxy_cache;
        include /etc/nginx/snippets/cache.conf;
    }

    # Error pages
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
    
    location = /404.html {
        internal;
    }
    
    location = /50x.html {
        internal;
    }
}
```

### Step 9: Create conf/conf.d/websocket.conf

```nginx
# WebSocket Server
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name ws.example.com;

    ssl_certificate /etc/nginx/ssl/certs/ws.example.com.crt;
    ssl_certificate_key /etc/nginx/ssl/private/ws.example.com.key;
    
    include /etc/nginx/snippets/ssl.conf;

    location / {
        proxy_pass http://websocket_backend;
        
        # WebSocket headers
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        
        # Timeouts
        proxy_connect_timeout 7d;
        proxy_send_timeout 7d;
        proxy_read_timeout 7d;
    }
}
```

### Step 10: Create Dockerfile

```dockerfile
FROM nginx:1.25-alpine

# Install certbot for Let's Encrypt
RUN apk add --no-cache certbot certbot-nginx

# Copy configuration
COPY conf/nginx.conf /etc/nginx/nginx.conf
COPY conf/upstream.conf /etc/nginx/upstream.conf
COPY conf/conf.d/ /etc/nginx/conf.d/
COPY conf/snippets/ /etc/nginx/snippets/

# Copy static files
COPY www/ /usr/share/nginx/html/

# Create directories
RUN mkdir -p /var/cache/nginx/proxy \
    && mkdir -p /var/cache/nginx/fastcgi \
    && mkdir -p /var/log/nginx \
    && chown -R nginx:nginx /var/cache/nginx \
    && chown -R nginx:nginx /var/log/nginx

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD wget --quiet --tries=1 --spider http://localhost/health || exit 1

EXPOSE 80 443

CMD ["nginx", "-g", "daemon off;"]
```

### Step 11: Create docker-compose.yml

```yaml
version: '3.8'

services:
  nginx:
    build: .
    container_name: [service-name]
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      # Configuration
      - ./conf/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./conf/upstream.conf:/etc/nginx/upstream.conf:ro
      - ./conf/conf.d:/etc/nginx/conf.d:ro
      - ./conf/snippets:/etc/nginx/snippets:ro
      
      # SSL certificates
      - ./ssl/certs:/etc/nginx/ssl/certs:ro
      - ./ssl/private:/etc/nginx/ssl/private:ro
      
      # Static files
      - ./www:/usr/share/nginx/html:ro
      
      # Cache
      - ./cache:/var/cache/nginx
      
      # Logs
      - ./logs:/var/log/nginx
    networks:
      - microservices
    environment:
      - TZ=UTC
    depends_on:
      - api-service
      - frontend-service

networks:
  microservices:
    external: true
```

### Step 12: Create scripts/setup.sh

```bash
#!/bin/bash
set -e

echo "Setting up Nginx gateway..."

# Create directories
mkdir -p ssl/certs ssl/private cache logs www/html www/static

# Set permissions
chmod 700 ssl/private
chmod 755 ssl/certs

# Create self-signed certificate for development
if [ ! -f ssl/certs/example.com.crt ]; then
    echo "Creating self-signed certificate..."
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout ssl/private/example.com.key \
        -out ssl/certs/example.com.crt \
        -subj "/C=US/ST=State/L=City/O=Organization/CN=example.com"
fi

# Create Docker network
docker network inspect microservices >/dev/null 2>&1 || \
    docker network create microservices

echo "Nginx gateway setup complete!"
echo "Run 'docker-compose up -d' to start Nginx"
```

### Step 13: Create scripts/renew-certs.sh

```bash
#!/bin/bash
# Certificate renewal script (for Let's Encrypt)

docker-compose exec nginx certbot renew --nginx
docker-compose exec nginx nginx -s reload
```

### Step 14: Create www/html/index.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>Welcome</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; padding: 50px; }
        h1 { color: #333; }
    </style>
</head>
<body>
    <h1>Welcome to Nginx Gateway</h1>
    <p>The gateway is running successfully.</p>
</body>
</html>
```

### Step 15: Create .env.example

```bash
# Nginx Configuration
NGINX_WORKER_PROCESSES=auto
NGINX_WORKER_CONNECTIONS=2048

# Domain
DOMAIN=example.com

# SSL/TLS
SSL_CERTIFICATE_PATH=/etc/nginx/ssl/certs
SSL_PRIVATE_KEY_PATH=/etc/nginx/ssl/private

# Rate Limiting
RATE_LIMIT_GENERAL=10r/s
RATE_LIMIT_API=20r/s
RATE_LIMIT_LOGIN=1r/s
```

### Step 16: Create .gitignore

```
# Logs
logs/*.log

# Certificates
ssl/certs/*.crt
ssl/certs/*.pem
ssl/private/*.key
ssl/private/*.pem

# Cache
cache/*
!cache/.gitkeep

# Environment
.env
.env.local
```

### Step 17: Create .devcontainer/[service-name]-container/devcontainer.json

```json
{
  "name": "[service-name]",
  "dockerComposeFile": [
    "../../docker-compose.yml"
  ],
  "service": "nginx",
  "workspaceFolder": "/workspace",
  "customizations": {
    "vscode": {
      "extensions": [
        "shanoor.vscode-nginx",
        "ms-azuretools.vscode-docker"
      ]
    }
  },
  "forwardPorts": [80, 443]
}
```

### Step 18: Create README.md

```markdown
# Nginx Gateway

High-performance reverse proxy and load balancer with SSL/TLS, caching, and security features.

## Features

- Reverse proxy and load balancing
- SSL/TLS termination
- HTTP/2 support
- Static file serving with caching
- Rate limiting and DDoS protection
- Security headers
- WebSocket support
- Gzip compression
- Access logging

## Prerequisites

- Docker & Docker Compose
- Ports 80, 443 available

## Quick Start

1. Run setup script:
```bash
chmod +x scripts/setup.sh
./scripts/setup.sh
```

2. Start Nginx:
```bash
docker-compose up -d
```

3. Test:
```bash
curl http://localhost/health
```

## Configuration

### Main Configuration
`conf/nginx.conf` - Main Nginx configuration

### Server Blocks
`conf/conf.d/` - Virtual host configurations

### Upstreams
`conf/upstream.conf` - Backend service definitions

### Snippets
`conf/snippets/` - Reusable configuration blocks

## SSL/TLS Setup

### Self-Signed (Development)
Automatically created by setup script.

### Let's Encrypt (Production)
```bash
docker-compose exec nginx certbot --nginx -d example.com
```

### Custom Certificates
Place certificates in:
- `ssl/certs/` - Certificate files
- `ssl/private/` - Private key files

## Load Balancing

Configured in `conf/upstream.conf`:
- **round-robin** (default)
- **least_conn** - Least connections
- **ip_hash** - Sticky sessions

## Caching

### Proxy Cache
Configured for API responses (10m cache, 1g max size)

### Static Files
Cached with 1 year expiry

## Rate Limiting

Zones configured in `nginx.conf`:
- General: 10 req/s
- API: 20 req/s
- Login: 1 req/s

## Monitoring

### Access Logs
JSON format in `logs/access.log`

### Error Logs
Located in `logs/error.log`

### Health Check
```bash
curl http://localhost/health
```

## Security

- TLS 1.2+ only
- Security headers (HSTS, CSP, etc.)
- Rate limiting
- Connection limiting
- Hide Nginx version

## Troubleshooting

### Test Configuration
```bash
docker-compose exec nginx nginx -t
```

### Reload Configuration
```bash
docker-compose exec nginx nginx -s reload
```

### View Logs
```bash
docker-compose logs -f nginx
```

## Production Checklist

- [ ] Replace self-signed certificates
- [ ] Configure real domain names
- [ ] Update upstream backends
- [ ] Set appropriate rate limits
- [ ] Configure log rotation
- [ ] Set up SSL certificate renewal
- [ ] Configure monitoring
- [ ] Test load balancing
- [ ] Security audit

## Useful Commands

```bash
# Reload configuration
docker-compose exec nginx nginx -s reload

# Test configuration
docker-compose exec nginx nginx -t

# View access logs
tail -f logs/access.log

# Check SSL certificate
openssl s_client -connect example.com:443
```

## License

[Your License]
```

---

## ✅ Completion Checklist

- [ ] All configuration files created
- [ ] nginx.conf main configuration complete
- [ ] Server blocks configured
- [ ] Upstream backends defined
- [ ] SSL/TLS configuration complete
- [ ] Security headers configured
- [ ] Rate limiting setup
- [ ] Caching configured
- [ ] Dockerfile created
- [ ] docker-compose.yml complete
- [ ] Self-signed certificates generated
- [ ] Setup script created and executable
- [ ] Static error pages created
- [ ] .gitignore configured
- [ ] README.md with documentation
- [ ] DevContainer configuration
- [ ] Nginx accessible
- [ ] SSL working
- [ ] Backend routing working
- [ ] Health check responding

---

**Next Steps:**
1. Replace self-signed certificates with real ones
2. Configure actual backend services
3. Set up Let's Encrypt auto-renewal
4. Configure log rotation
5. Set up monitoring and alerts
6. Test load balancing
7. Perform security audit
8. Configure backup for configurations
