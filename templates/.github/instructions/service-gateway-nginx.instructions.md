---
applyTo:
  - "services/gateway-nginx-*/**"
---

# Nginx Gateway Service Instructions

Apply to Nginx reverse proxy and load balancer configurations.

## Technology Stack

- **Server:** Nginx 1.25+
- **Configuration:** nginx.conf with includes
- **TLS:** Certbot/Let's Encrypt or manual certificates
- **Modules:** Core + optional (geoip, stream, etc.)
- **Monitoring:** Stub status, access logs

## Configuration Structure

```
services/gateway-nginx-{purpose}/
├── nginx/
│   ├── nginx.conf           ← Main configuration
│   ├── conf.d/
│   │   ├── default.conf     ← Default server
│   │   ├── api.conf         ← API gateway config
│   │   └── static.conf      ← Static file serving
│   ├── ssl/                 ← Certificates
│   ├── snippets/            ← Reusable configs
│   └── sites-enabled/       ← Virtual hosts
├── Dockerfile
└── README.md
```

## Core Patterns

**Main Configuration (nginx.conf):**
- Worker processes: auto (or CPU count)
- Worker connections: 1024-4096
- Keepalive timeout: 65s
- Client body buffer size
- Gzip compression enabled
- Log formats (access, error)

**Server Blocks:**
- One file per domain/service
- Listen on 80 (HTTP) and 443 (HTTPS)
- Server name directives
- Root/location blocks
- SSL certificate paths
- Access/error log paths

**Location Blocks:**
- Exact match: `location = /api/health`
- Prefix match: `location /api/`
- Regex match: `location ~ \.php$`
- Named locations: `location @fallback`
- Priority: exact > prefix > regex

**Reverse Proxy:**
- `proxy_pass http://backend:8000`
- Preserve headers: `X-Real-IP`, `X-Forwarded-For`, `X-Forwarded-Proto`
- Proxy buffering configuration
- Timeouts: `proxy_connect_timeout`, `proxy_read_timeout`
- WebSocket support with upgrade headers

**Load Balancing:**
- Upstream blocks with servers
- Strategies: round-robin (default), least_conn, ip_hash, hash
- Health checks: `max_fails`, `fail_timeout`
- Backup servers
- Weighted distribution

**Caching:**
- Proxy cache path configuration
- Cache zones with sizes
- Cache keys based on URI/args
- Cache bypass conditions
- Purge mechanisms

**SSL/TLS Configuration:**
- Certificate and key paths
- SSL protocols (TLSv1.2, TLSv1.3)
- SSL ciphers (strong only)
- SSL session cache
- HSTS header
- OCSP stapling

## Common Configurations

**API Gateway:**
```nginx
upstream api_backend {
    least_conn;
    server api1:8000 weight=3;
    server api2:8000 weight=1;
    keepalive 32;
}

server {
    listen 443 ssl http2;
    server_name api.example.com;

    location /api/ {
        proxy_pass http://api_backend;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

**Static File Serving:**
```nginx
location /static/ {
    alias /var/www/static/;
    expires 1y;
    add_header Cache-Control "public, immutable";
    access_log off;
}

location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
    expires 7d;
    add_header Cache-Control "public";
}
```

**Rate Limiting:**
```nginx
limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;

location /api/ {
    limit_req zone=api_limit burst=20 nodelay;
    proxy_pass http://api_backend;
}
```

**WebSocket Support:**
```nginx
location /ws/ {
    proxy_pass http://websocket_backend;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_read_timeout 86400;
}
```

## Security Headers

```nginx
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header Content-Security-Policy "default-src 'self'" always;
```

## Performance Optimizations

- Enable gzip compression (level 6)
- HTTP/2 support
- Keepalive connections to upstreams
- Open file cache
- Buffer sizes tuned for workload
- TCP optimizations (nodelay, nopush)

**Gzip Configuration:**
```nginx
gzip on;
gzip_vary on;
gzip_min_length 1024;
gzip_comp_level 6;
gzip_types text/plain text/css application/json application/javascript text/xml application/xml;
```

## Monitoring

- Stub status module: `http://localhost/nginx_status`
- Access logs with custom format
- Error logs with appropriate level
- Metrics exporters for Prometheus
- Request/response times logging

**Status Endpoint:**
```nginx
location /nginx_status {
    stub_status on;
    access_log off;
    allow 127.0.0.1;
    deny all;
}
```

## Anti-Patterns

❌ Not setting `proxy_set_header Host`  
❌ Ignoring buffer size warnings  
❌ Using regex locations unnecessarily  
❌ Not implementing rate limiting  
❌ Missing timeout configurations  
❌ Not logging to stdout in Docker  
❌ Hardcoding server IPs  
❌ Not testing config with `nginx -t`  

## Docker Integration

**Dockerfile:**
```dockerfile
FROM nginx:alpine
COPY nginx/nginx.conf /etc/nginx/nginx.conf
COPY nginx/conf.d /etc/nginx/conf.d
RUN nginx -t
```

**Configuration Mounts:**
- `/etc/nginx/nginx.conf`
- `/etc/nginx/conf.d/`
- `/etc/nginx/ssl/`
- `/var/log/nginx/` (optional)

**Logging to Stdout:**
```nginx
access_log /dev/stdout main;
error_log /dev/stderr warn;
```

## SSL/TLS Setup

**Let's Encrypt (Certbot):**
- Use certbot/certbot Docker image
- Mount webroot for challenges
- Renewal hooks for nginx reload
- Store certificates in volume

**Manual Certificates:**
```nginx
ssl_certificate /etc/nginx/ssl/cert.pem;
ssl_certificate_key /etc/nginx/ssl/key.pem;
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers HIGH:!aNULL:!MD5;
ssl_prefer_server_ciphers on;
```

## Health Checks

- Return 200 for `/health` endpoint
- Proxy to backend health checks
- Monitor upstream status
- Graceful degradation on failures

## Testing

- Syntax check: `nginx -t`
- Reload config: `nginx -s reload`
- Test with curl and headers
- Load test with ab/wrk
- SSL test with ssllabs.com

## Common Use Cases

**1. Multi-Service Gateway:**
- `/api/users` → user-service
- `/api/orders` → order-service
- `/admin` → admin-frontend
- `/` → main-frontend

**2. CDN/Cache Layer:**
- Cache API responses
- Serve static assets
- Browser caching headers
- Cache purging endpoint

**3. Security Layer:**
- Rate limiting per IP
- Geographic restrictions
- DDoS mitigation
- Request filtering

## Environment Variables

Support via envsubst:
- `BACKEND_HOST`
- `BACKEND_PORT`
- `SSL_CERTIFICATE_PATH`
- `LOG_LEVEL`

## Required Modules

Core modules (built-in):
- http_ssl_module
- http_v2_module
- http_gzip_static_module
- http_stub_status_module

Optional:
- http_realip_module (behind load balancer)
- stream_module (TCP/UDP proxy)
- http_geoip_module (geographic filtering)
