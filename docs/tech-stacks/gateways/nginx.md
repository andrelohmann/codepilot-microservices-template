# Nginx - High-Performance Web Server & Reverse Proxy

Battle-tested web server and reverse proxy with excellent performance and caching capabilities.

---

## Overview

**Naming:** `gateway-nginx-{purpose}`

**Examples:** `gateway-nginx-cdn`, `gateway-nginx-cache`, `gateway-nginx-lb`

**Nginx** is a high-performance web server and reverse proxy, excellent for static file serving, caching, and traditional deployments.

---

## Technology Stack

- **Nginx 1.25+** - Web server/proxy
- **OpenResty** - Lua scripting (optional)
- **Certbot** - Let's Encrypt SSL
- **ngx_http_upstream_module** - Load balancing
- **ngx_http_cache_module** - Caching

---

## Features

✅ **High performance** - Fastest web server  
✅ **Static file serving** - Excellent performance  
✅ **Caching** - Proxy cache and static caching  
✅ **Load balancing** - Multiple algorithms  
✅ **SSL/TLS** - Full TLS 1.3 support  
✅ **Rate limiting** - Built-in  
✅ **WebSocket** - Full support  
✅ **Lua scripting** - Advanced routing

---

## Ideal Use Cases

✅ **Static file serving** - Frontend assets, images  
✅ **Caching layer** - CDN-like caching  
✅ **Traditional deployments** - VMs, bare metal  
✅ **High performance** - Maximum throughput  
✅ **Load balancing** - Distribute traffic

---

## Configuration

### Basic Configuration

```nginx
# nginx.conf
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/conf.d/*.conf;
}
```

### Reverse Proxy

```nginx
# /etc/nginx/conf.d/api.conf
upstream api_backend {
    server api1:8000;
    server api2:8000;
    server api3:8000;
    
    keepalive 32;
}

server {
    listen 80;
    server_name api.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name api.example.com;

    ssl_certificate /etc/letsencrypt/live/api.example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/api.example.com/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    location / {
        proxy_pass http://api_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        proxy_http_version 1.1;
        proxy_set_header Connection "";
    }

    location /health {
        access_log off;
        return 200 "healthy\n";
    }
}
```

### Static File Serving + API

```nginx
server {
    listen 443 ssl http2;
    server_name app.example.com;

    ssl_certificate /etc/letsencrypt/live/app.example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/app.example.com/privkey.pem;

    root /usr/share/nginx/html;
    index index.html;

    # Static files
    location / {
        try_files $uri $uri/ /index.html;
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # API proxy
    location /api/ {
        proxy_pass http://api_backend/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### Caching

```nginx
# Cache configuration
proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=api_cache:10m max_size=1g inactive=60m;

server {
    location /api/ {
        proxy_cache api_cache;
        proxy_cache_key $scheme$request_method$host$request_uri;
        proxy_cache_valid 200 304 10m;
        proxy_cache_valid 404 1m;
        proxy_cache_bypass $http_cache_control;
        add_header X-Cache-Status $upstream_cache_status;

        proxy_pass http://api_backend/;
    }
}
```

### Load Balancing

```nginx
# Round-robin (default)
upstream backend {
    server api1:8000;
    server api2:8000;
    server api3:8000;
}

# Least connections
upstream backend {
    least_conn;
    server api1:8000;
    server api2:8000;
}

# IP hash (sticky sessions)
upstream backend {
    ip_hash;
    server api1:8000;
    server api2:8000;
}

# Weighted
upstream backend {
    server api1:8000 weight=3;
    server api2:8000 weight=2;
    server api3:8000 weight=1;
}
```

### Rate Limiting

```nginx
# Define rate limit zone
limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;

server {
    location /api/ {
        limit_req zone=api_limit burst=20 nodelay;
        proxy_pass http://api_backend/;
    }
}
```

---

## SSL with Let's Encrypt

```bash
# Install Certbot
apt-get install certbot python3-certbot-nginx

# Obtain certificate
certbot --nginx -d api.example.com

# Auto-renewal
certbot renew --dry-run
```

---

## Deployment

### Docker

```dockerfile
FROM nginx:alpine

COPY nginx.conf /etc/nginx/nginx.conf
COPY conf.d/ /etc/nginx/conf.d/

EXPOSE 80 443

CMD ["nginx", "-g", "daemon off;"]
```

```yaml
# docker-compose.yml
version: '3.8'
services:
  nginx:
    build: .
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./conf.d:/etc/nginx/conf.d:ro
      - letsencrypt:/etc/letsencrypt
    restart: unless-stopped

volumes:
  letsencrypt:
```

---

## Monitoring

### Access Logs
```nginx
log_format json_combined escape=json
  '{'
    '"time_local":"$time_local",'
    '"remote_addr":"$remote_addr",'
    '"request":"$request",'
    '"status":$status,'
    '"body_bytes_sent":$body_bytes_sent,'
    '"request_time":$request_time,'
    '"upstream_response_time":"$upstream_response_time"'
  '}';

access_log /var/log/nginx/access.log json_combined;
```

### Stub Status
```nginx
location /nginx_status {
    stub_status;
    access_log off;
    allow 127.0.0.1;
    deny all;
}
```

---

## Best Practices

✅ Use HTTP/2  
✅ Enable compression (gzip)  
✅ Set security headers  
✅ Implement caching  
✅ Use rate limiting  
✅ Enable SSL with strong ciphers  
✅ Monitor with access logs  
✅ Use upstream health checks

---

## Related Documentation

- [Gateways Overview](./README.md)
- [Traefik](./traefik.md)
- [API Technologies](../apis/README.md)

---

**Reference Files:**
- Instruction: `service-gateway-nginx.instructions.md`
- Scaffold: `scaffold-gateway-nginx-service.md`
