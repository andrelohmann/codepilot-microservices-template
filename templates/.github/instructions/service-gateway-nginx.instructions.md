---
applyTo:
  - "services/gateway-nginx-*/**"
---

# Nginx Gateway Service Instructions

Apply to Nginx reverse proxy and load balancer configurations.

**Reference**: [Complete Nginx Tech Stack Documentation](../../../docs/tech-stacks/gateways/nginx.md)  
**Scaffold**: Use the `scaffold-gateway-nginx-service.prompt.md` for new services

## Configuration Structure

```
services/gateway-nginx-{purpose}/
├── nginx/
│   ├── nginx.conf       # Main configuration
│   ├── conf.d/          # Server blocks
│   │   ├── api.conf
│   │   └── static.conf
│   ├── ssl/             # Certificates
│   └── snippets/        # Reusable configs
└── Dockerfile
```

## Quick Reference Patterns

### 1. Reverse Proxy with Load Balancing

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
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### 2. Rate Limiting

```nginx
limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;

location /api/ {
    limit_req zone=api_limit burst=20 nodelay;
    proxy_pass http://api_backend;
}
```

### 3. WebSocket Support

```nginx
location /ws/ {
    proxy_pass http://websocket_backend;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_read_timeout 86400;
}
```

### 4. Static Files with Caching

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

### 5. Security Headers

```nginx
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
```

## Code Style

- Worker processes: `auto`
- Worker connections: 1024-4096
- Keepalive timeout: 65s
- One server block per domain/service in `conf.d/`
- Location priority: exact (`=`) > prefix > regex (`~`)
- Upstream blocks for load balancing (round-robin, least_conn, ip_hash)
- Always set proxy headers (Host, X-Real-IP, X-Forwarded-For, X-Forwarded-Proto)
- Enable gzip compression (level 6)
- HTTP/2 support for HTTPS
- SSL protocols: TLSv1.2, TLSv1.3 only
- Strong SSL ciphers only
- HSTS header for HTTPS
- Rate limiting for public endpoints
- Log to stdout/stderr in Docker

## Common Use Cases

**API Gateway**: Route `/api/users` → user-service, `/api/orders` → order-service with rate limiting  
**CDN/Cache**: Cache API responses, serve static assets with long expiry  
**Security**: Rate limiting, geographic restrictions, security headers  
**WebSocket**: Upgrade headers + long read timeout

## Anti-Patterns

❌ Not setting `proxy_set_header Host`  
❌ Using regex locations unnecessarily  
❌ Not implementing rate limiting  
❌ Missing timeout configurations  
❌ Not logging to stdout in Docker  
❌ Hardcoding server IPs  
❌ Not testing config with `nginx -t`
