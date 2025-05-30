# Docker Compose Multi-Service Architecture

Dokumentasi lengkap untuk setup multi-service dengan Docker Compose yang mencakup database, admin tools, backend services, dan frontend applications dengan Nginx sebagai reverse proxy.

## 📁 Struktur Folder

```
project-root/
├── docker-compose.yml                 # Main compose file
├── .env                              # Environment variables
├── README.md                         # Dokumentasi ini
├── nginx/
│   ├── docker-compose.yml
│   ├── nginx.conf
│   ├── conf.d/
│   │   ├── admin.conf               # Admin dashboard routes
│   │   ├── api.conf                 # API gateway routes
│   │   └── default.conf             # Default web app routes
│   └── ssl/                         # SSL certificates (optional)
│       ├── cert.pem
│       └── key.pem
├── databases/
│   ├── mongodb/
│   │   ├── docker-compose.yml
│   │   └── init/
│   │       └── init-mongo.js        # MongoDB initialization script
│   ├── influxdb/
│   │   ├── docker-compose.yml
│   │   └── config/
│   │       └── influxdb.conf        # InfluxDB configuration
│   └── mariadb/
│       ├── docker-compose.yml
│       └── init/
│           └── init.sql             # MariaDB initialization script
├── admin/
│   ├── mongo-express/
│   │   └── docker-compose.yml
│   ├── chronograf/
│   │   └── docker-compose.yml
│   └── phpmyadmin/
│       └── docker-compose.yml
├── backend/
│   ├── api-gateway/
│   │   ├── docker-compose.yml
│   │   ├── Dockerfile
│   │   └── src/
│   ├── user-service/
│   │   ├── docker-compose.yml
│   │   ├── Dockerfile
│   │   └── src/
│   └── data-service/
│       ├── docker-compose.yml
│       ├── Dockerfile
│       └── src/
├── frontend/
│   ├── web-app/
│   │   ├── docker-compose.yml
│   │   ├── Dockerfile
│   │   └── src/
│   └── admin-dashboard/
│       ├── docker-compose.yml
│       ├── Dockerfile
│       └── src/
├── scripts/
│   ├── setup.sh                     # Setup script
│   ├── start.sh                     # Start script
│   ├── stop.sh                      # Stop script
│   └── cleanup.sh                   # Cleanup script
└── volumes/                         # Volume mount points
    ├── mongodb/
    ├── influxdb/
    ├── mariadb/
    ├── nginx/
    └── logs/
```

## 🚀 Quick Start

### Prerequisites
- Docker Engine 20.10+
- Docker Compose 2.0+
- Minimal 4GB RAM
- Minimal 10GB disk space

### 1. Setup Awal

```bash
# Clone atau buat project directory
mkdir my-docker-project
cd my-docker-project

# Buat file .env
cp .env.example .env
# Edit .env sesuai kebutuhan

# Buat volumes
docker volume create mongodb_data
docker volume create influxdb_data
docker volume create mariadb_data
docker volume create nginx_logs
docker volume create chronograf_data

# Set permissions (Linux/Mac)
chmod +x scripts/*.sh
```

### 2. Jalankan Services

```bash
# Jalankan semua services
docker-compose up -d

# Atau gunakan script
./scripts/start.sh
```

### 3. Verifikasi

```bash
# Cek status containers
docker-compose ps

# Cek logs
docker-compose logs -f

# Test endpoints
curl http://localhost/health
```

## 📋 File Konfigurasi

### 1. Main Docker Compose (docker-compose.yml)

```yaml
version: '3.8'

services:
  # Nginx Reverse Proxy
  nginx:
    extends:
      file: ./nginx/docker-compose.yml
      service: nginx
    depends_on:
      - mongo-express
      - chronograf
      - phpmyadmin
      - api-gateway
    networks:
      - web-network
      - admin-network
      - app-network
    ports:
      - "80:80"
      - "443:443"

  # Databases
  mongodb:
    extends:
      file: ./databases/mongodb/docker-compose.yml
      service: mongodb
    networks:
      - database-network
      - app-network

  influxdb:
    extends:
      file: ./databases/influxdb/docker-compose.yml
      service: influxdb
    networks:
      - database-network
      - app-network

  mariadb:
    extends:
      file: ./databases/mariadb/docker-compose.yml
      service: mariadb
    networks:
      - database-network
      - app-network

  # Database Admin Tools
  mongo-express:
    extends:
      file: ./admin/mongo-express/docker-compose.yml
      service: mongo-express
    depends_on:
      - mongodb
    networks:
      - database-network
      - admin-network

  chronograf:
    extends:
      file: ./admin/chronograf/docker-compose.yml
      service: chronograf
    depends_on:
      - influxdb
    networks:
      - database-network
      - admin-network

  phpmyadmin:
    extends:
      file: ./admin/phpmyadmin/docker-compose.yml
      service: phpmyadmin
    depends_on:
      - mariadb
    networks:
      - database-network
      - admin-network

  # Backend Services
  api-gateway:
    extends:
      file: ./backend/api-gateway/docker-compose.yml
      service: api-gateway
    depends_on:
      - mongodb
      - influxdb
      - mariadb
    networks:
      - app-network
      - database-network

  user-service:
    extends:
      file: ./backend/user-service/docker-compose.yml
      service: user-service
    depends_on:
      - mongodb
    networks:
      - app-network
      - database-network

  data-service:
    extends:
      file: ./backend/data-service/docker-compose.yml
      service: data-service
    depends_on:
      - influxdb
    networks:
      - app-network
      - database-network

  # Frontend Services
  web-app:
    extends:
      file: ./frontend/web-app/docker-compose.yml
      service: web-app
    depends_on:
      - api-gateway
    networks:
      - app-network

  admin-dashboard:
    extends:
      file: ./frontend/admin-dashboard/docker-compose.yml
      service: admin-dashboard
    depends_on:
      - api-gateway
    networks:
      - app-network

networks:
  web-network:
    driver: bridge
  database-network:
    driver: bridge
    internal: true  # Database network tidak bisa akses keluar
  app-network:
    driver: bridge
  admin-network:
    driver: bridge

volumes:
  mongodb_data:
    driver: local
  influxdb_data:
    driver: local
  mariadb_data:
    driver: local
  nginx_logs:
    driver: local
  chronograf_data:
    driver: local
```

### 2. Environment File (.env)

```env
# =================================
# DATABASE CREDENTIALS
# =================================
MONGO_ROOT_USERNAME=wena
MONGO_ROOT_PASSWORD=Wena0403

INFLUX_USERNAME=wena
INFLUX_PASSWORD=Wena0403
INFLUX_ORG=myorg
INFLUX_BUCKET=mybucket
INFLUX_ADMIN_TOKEN=Darkside04&

MARIADB_ROOT_PASSWORD=Wena0403
MARIADB_DATABASE=mydb
MARIADB_USER=wena
MARIADB_PASSWORD=Wena0403

# =================================
# ADMIN TOOLS
# =================================
MONGO_EXPRESS_USERNAME=admin
MONGO_EXPRESS_PASSWORD=admin123

PHPMYADMIN_USER=admin
PHPMYADMIN_PASSWORD=admin123

# =================================
# NGINX CONFIGURATION
# =================================
NGINX_HOST=localhost
NGINX_PORT=80
NGINX_SSL_PORT=443

# =================================
# BACKEND SERVICES
# =================================
API_GATEWAY_PORT=3000
USER_SERVICE_PORT=3001
DATA_SERVICE_PORT=3002

# =================================
# FRONTEND APPLICATIONS
# =================================
WEB_APP_PORT=3010
ADMIN_DASHBOARD_PORT=3011

# =================================
# LOGGING & MONITORING
# =================================
LOG_LEVEL=info
ENABLE_METRICS=true

# =================================
# SECURITY
# =================================
JWT_SECRET=your-super-secret-jwt-key
API_KEY=your-api-key
CORS_ORIGIN=http://localhost
```

### 3. Nginx Configuration

#### nginx/docker-compose.yml
```yaml
version: '3.8'

services:
  nginx:
    image: nginx:1.25-alpine
    container_name: nginx-proxy
    restart: unless-stopped
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./conf.d/:/etc/nginx/conf.d/:ro
      - nginx_logs:/var/log/nginx
      - ./ssl:/etc/nginx/ssl:ro
    healthcheck:
      test: ["CMD", "nginx", "-t"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 10s

volumes:
  nginx_logs:
    external: true
```

#### nginx/nginx.conf
```nginx
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log notice;
pid /var/run/nginx.pid;

events {
    worker_connections 1024;
    use epoll;
    multi_accept on;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    # Logging Format
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                   '$status $body_bytes_sent "$http_referer" '
                   '"$http_user_agent" "$http_x_forwarded_for" '
                   'rt=$request_time uct="$upstream_connect_time" '
                   'uht="$upstream_header_time" urt="$upstream_response_time"';

    access_log /var/log/nginx/access.log main;
    error_log /var/log/nginx/error.log warn;

    # Basic Settings
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    client_max_body_size 100M;
    server_tokens off;

    # Buffer Settings
    client_body_buffer_size 128k;
    client_header_buffer_size 1k;
    large_client_header_buffers 4 4k;
    output_buffers 1 32k;
    postpone_output 1460;

    # Timeout Settings
    client_body_timeout 12;
    client_header_timeout 12;
    send_timeout 10;

    # Gzip Settings
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_min_length 1000;
    gzip_types
        text/plain
        text/css
        text/xml
        text/javascript
        application/json
        application/javascript
        application/xml+rss
        application/atom+xml
        image/svg+xml
        application/font-woff
        application/font-woff2;

    # Rate Limiting
    limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;
    limit_req_zone $binary_remote_addr zone=login:10m rate=1r/s;

    # Include server configurations
    include /etc/nginx/conf.d/*.conf;
}
```

#### nginx/conf.d/admin.conf
```nginx
# Admin Dashboard - Main Landing Page
server {
    listen 80;
    server_name admin.localhost localhost;

    # Rate limiting
    limit_req zone=api burst=20 nodelay;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;

    location / {
        return 200 '
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Admin Dashboard</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { 
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; 
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }
        .container { 
            max-width: 1200px; 
            margin: 0 auto; 
            background: rgba(255,255,255,0.95); 
            padding: 40px; 
            border-radius: 20px; 
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            backdrop-filter: blur(10px);
        }
        h1 { 
            color: #333; 
            text-align: center; 
            margin-bottom: 10px; 
            font-size: 2.5rem;
            background: linear-gradient(45deg, #667eea, #764ba2);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        .subtitle {
            text-align: center;
            color: #666;
            margin-bottom: 40px;
            font-size: 1.1rem;
        }
        .grid { 
            display: grid; 
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); 
            gap: 25px; 
            margin-top: 30px; 
        }
        .card { 
            background: white; 
            border: none;
            border-radius: 15px; 
            padding: 30px; 
            text-align: center; 
            transition: all 0.3s ease;
            box-shadow: 0 5px 15px rgba(0,0,0,0.08);
            position: relative;
            overflow: hidden;
        }
        .card::before {
            content: "";
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            height: 4px;
            background: linear-gradient(90deg, #667eea, #764ba2);
        }
        .card:hover { 
            transform: translateY(-10px); 
            box-shadow: 0 20px 40px rgba(0,0,0,0.15);
        }
        .card-icon {
            font-size: 3rem;
            margin-bottom: 15px;
            display: block;
        }
        .card h3 { 
            margin: 0 0 15px 0; 
            color: #333; 
            font-size: 1.3rem;
        }
        .card p {
            color: #666;
            margin-bottom: 20px;
            line-height: 1.5;
        }
        .card a { 
            display: inline-block; 
            background: linear-gradient(45deg, #667eea, #764ba2);
            color: white; 
            padding: 12px 25px; 
            text-decoration: none; 
            border-radius: 25px; 
            margin-top: 10px;
            transition: all 0.3s ease;
            font-weight: 500;
        }
        .card a:hover { 
            transform: scale(1.05);
            box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
        }
        .database::before { background: linear-gradient(90deg, #28a745, #20c997); }
        .backend::before { background: linear-gradient(90deg, #17a2b8, #138496); }
        .frontend::before { background: linear-gradient(90deg, #ffc107, #fd7e14); }
        .status {
            position: absolute;
            top: 15px;
            right: 15px;
            width: 12px;
            height: 12px;
            background: #28a745;
            border-radius: 50%;
            animation: pulse 2s infinite;
        }
        @keyframes pulse {
            0% { box-shadow: 0 0 0 0 rgba(40, 167, 69, 0.7); }
            70% { box-shadow: 0 0 0 10px rgba(40, 167, 69, 0); }
            100% { box-shadow: 0 0 0 0 rgba(40, 167, 69, 0); }
        }
        @media (max-width: 768px) {
            .container { padding: 20px; }
            h1 { font-size: 2rem; }
            .grid { grid-template-columns: 1fr; }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🚀 Admin Dashboard</h1>
        <p class="subtitle">Manage your databases, APIs, and applications</p>
        
        <div class="grid">
            <div class="card database">
                <div class="status"></div>
                <span class="card-icon">🍃</span>
                <h3>MongoDB Admin</h3>
                <p>Manage MongoDB databases, collections, and documents</p>
                <a href="/mongo/" target="_blank">Open Mongo Express</a>
            </div>
            
            <div class="card database">
                <div class="status"></div>
                <span class="card-icon">📈</span>
                <h3>InfluxDB Admin</h3>
                <p>Time series database management and visualization</p>
                <a href="/influx/" target="_blank">Open Chronograf</a>
            </div>
            
            <div class="card database">
                <div class="status"></div>
                <span class="card-icon">🗄️</span>
                <h3>MariaDB Admin</h3>
                <p>MySQL/MariaDB administration and query interface</p>
                <a href="/mariadb/" target="_blank">Open phpMyAdmin</a>
            </div>
            
            <div class="card backend">
                <div class="status"></div>
                <span class="card-icon">🔗</span>
                <h3>API Gateway</h3>
                <p>Backend API management and documentation</p>
                <a href="/api/" target="_blank">Open API Docs</a>
            </div>
            
            <div class="card frontend">
                <div class="status"></div>
                <span class="card-icon">🌐</span>
                <h3>Web Application</h3>
                <p>Main web application interface</p>
                <a href="/app/" target="_blank">Open Web App</a>
            </div>
            
            <div class="card frontend">
                <div class="status"></div>
                <span class="card-icon">⚙️</span>
                <h3>Admin Panel</h3>
                <p>Administrative interface and system management</p>
                <a href="/admin-panel/" target="_blank">Open Admin Panel</a>
            </div>
        </div>
    </div>
</body>
</html>';
        add_header Content-Type text/html;
    }

    # MongoDB Admin (Mongo Express)
    location /mongo/ {
        proxy_pass http://mongo-express:8081/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect off;
    }

    # InfluxDB Admin (Chronograf)
    location /influx/ {
        proxy_pass http://chronograf:8888/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect off;
    }

    # MariaDB Admin (phpMyAdmin)
    location /mariadb/ {
        proxy_pass http://phpmyadmin:80/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect off;
    }
}
```

#### nginx/conf.d/api.conf
```nginx
# API Gateway and Backend Services
server {
    listen 80;
    server_name api.localhost;

    # Rate limiting untuk API
    limit_req zone=api burst=50 nodelay;

    # Security headers
    add_header X-Frame-Options "DENY" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    # API Gateway
    location /api/ {
        proxy_pass http://api-gateway:3000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # Timeout settings
        proxy_connect_timeout 30s;
        proxy_send_timeout 30s;
        proxy_read_timeout 30s;
        
        # CORS headers
        add_header 'Access-Control-Allow-Origin' '$http_origin' always;
        add_header 'Access-Control-Allow-Credentials' 'true' always;
        add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, OPTIONS, PATCH' always;
        add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range,Authorization,X-API-Key' always;
        
        # Handle preflight requests
        if ($request_method = 'OPTIONS') {
            add_header 'Access-Control-Max-Age' 1728000;
            add_header 'Content-Type' 'text/plain; charset=utf-8';
            add_header 'Content-Length' 0;
            return 204;
        }
    }

    # User Service
    location /api/users/ {
        proxy_pass http://user-service:3001/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Data Service
    location /api/data/ {
        proxy_pass http://data-service:3002/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # API Documentation
    location /docs/ {
        proxy_pass http://api-gateway:3000/docs/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

#### nginx/conf.d/default.conf
```nginx
# Default server for web applications
server {
    listen 80 default_server;
    server_name _;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    # Main Web Application
    location /app/ {
        proxy_pass http://web-app:3010/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    # Admin Dashboard Frontend
    location /admin-panel/ {
        proxy_pass http://admin-dashboard:3011/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Static files caching
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
        try_files $uri @proxy;
    }

    # Fallback to proxy
    location @proxy {
        proxy_pass http://web-app:3010;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Redirect root to app
    location = / {
        return 301 $scheme://$host/app/;
    }

    # Health check endpoint
    location /health {
        access_log off;
        return 200 '{"status":"healthy","timestamp":"$time_iso8601","server":"$hostname"}';
        add_header Content-Type application/json;
    }

    # Nginx status (optional, untuk monitoring)
    location /nginx-status {
        stub_status on;
        access_log off;
        allow 127.0.0.1;
        allow 172.16.0.0/12;
        deny all;
    }
}
```

## 🗄️ Database Configurations

### databases/mongodb/docker-compose.yml
```yaml
version: '3.8'

services:
  mongodb:
    image: mongo:7-jammy
    container_name: mongodb
    restart: unless-stopped
    environment:
      MONGO_INITDB_ROOT_USERNAME: ${MONGO_ROOT_USERNAME}
      MONGO_INITDB_ROOT_PASSWORD: ${MONGO_ROOT_PASSWORD}
    ports:
      - "27017:27017"
    volumes:
      - mongodb_data:/data/db
      - ./init:/docker-entrypoint-initdb.d/
    healthcheck:
      test: ["CMD", "mongosh", "--eval", "db.adminCommand('ping')"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 30s
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"

volumes:
  mongodb_data:
    external: true
```

### databases/mongodb/init/init-mongo.js
```javascript
// MongoDB initialization script
db = db.getSiblingDB('admin');

// Create application databases
db = db.getSiblingDB('users');
db.createCollection('profiles');
db.profiles.createIndex({ "email": 1 }, { unique: true });
db.profiles.createIndex({ "username": 1 }, { unique: true });

db = db.getSiblingDB('logs');
db.createCollection('application_logs');
db.application_logs.createIndex({ "timestamp": 1 });
db.application_logs.createIndex({ "level": 1 });

print('MongoDB initialization completed');
```

### databases/influxdb/docker-compose.yml
```yaml
version: '3.8'

services:
  influxdb:
    image: influxdb:2.7-alpine
    container_name: influxdb
    restart: unless-stopped
    environment:
      DOCKER_INFLUXDB_INIT_MODE: setup
      DOCKER_INFLUXDB_INIT_USERNAME: ${INFLUX_USERNAME}
      DOCKER_INFLUXDB_INIT_PASSWORD: ${INFLUX_PASSWORD}
      DOCKER_INFLUXDB_INIT_ORG: ${INFLUX_ORG}
      DOCKER_INFLUXDB_INIT_BUCKET: ${INFLUX_BUCKET}
      DOCKER_INFLUXDB_INIT_ADMIN_TOKEN: ${INFLUX_ADMIN_TOKEN}
    ports:
      - "8086:8086"
    volumes:
      - influxdb_data:/var/lib/influxdb2
      - ./config:/etc/influxdb2
    healthcheck:
      test: ["CMD", "influx", "ping"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 30s
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"

volumes:
  influxdb_data:
    external: true
```

### databases/mariadb/docker-compose.yml
```yaml
version: '3.8'

services:
  mariadb:
    image: mariadb:11-jammy
    container_name: mariadb
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: ${MARIADB_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MARIADB_DATABASE}
      MYSQL_USER: ${MARIADB_USER}
      MYSQL_PASSWORD: ${MARIADB_PASSWORD}
    ports:
      - "3306:3306"
    volumes:
      - mariadb_data:/var/lib/mysql
      - ./init:/docker-entrypoint-initdb.d/
    command: --default-authentication-plugin=mysql_native_password
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 30s
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"

volumes:
  mariadb_data:
    external: true
```

### databases/mariadb/init/init.sql
```sql
-- MariaDB initialization script
CREATE DATABASE IF NOT EXISTS app_data;
CREATE DATABASE IF NOT EXISTS sessions;
CREATE DATABASE IF NOT EXISTS analytics;

USE app_data;

-- Users table
CREATE TABLE IF NOT EXISTS users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_email (email),
    INDEX idx_username (username)
);

-- Settings table
CREATE TABLE IF NOT EXISTS settings (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    setting_key VARCHAR(100) NOT NULL,
    setting_value TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    UNIQUE KEY unique_user_setting (user_id, setting_key)
);

USE sessions;

-- Sessions table
CREATE TABLE IF NOT EXISTS user_sessions (
    session_id VARCHAR(128) PRIMARY KEY,
    user_id INT NOT NULL,
    data TEXT,
    expires_at TIMESTAMP NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_user_id (user_id),
    INDEX idx_expires (expires_at)
);

USE analytics;

-- Page views table
CREATE TABLE IF NOT EXISTS page_views (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    url VARCHAR(500) NOT NULL,
    user_agent TEXT,
    ip_address VARCHAR(45),
    referrer VARCHAR(500),
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_url (url),
    INDEX idx_timestamp (timestamp)
);

-- Grant permissions
GRANT ALL PRIVILEGES ON app_data.* TO '${MARIADB_USER}'@'%';
GRANT ALL PRIVILEGES ON sessions.* TO '${MARIADB_USER}'@'%';  
GRANT ALL PRIVILEGES ON analytics.* TO '${MARIADB_USER}'@'%';
FLUSH PRIVILEGES;
```

## 🔧 Admin Tools Configuration

### admin/mongo-express/docker-compose.yml
```yaml
version: '3.8'

services:
  mongo-express:
    image: mongo-express:1-20-alpine3.18
    container_name: mongo-express
    restart: unless-stopped
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: ${MONGO_ROOT_USERNAME}
      ME_CONFIG_MONGODB_ADMINPASSWORD: ${MONGO_ROOT_PASSWORD}
      ME_CONFIG_MONGODB_URL: mongodb://${MONGO_ROOT_USERNAME}:${MONGO_ROOT_PASSWORD}@mongodb:27017/
      ME_CONFIG_BASICAUTH_USERNAME: ${MONGO_EXPRESS_USERNAME}
      ME_CONFIG_BASICAUTH_PASSWORD: ${MONGO_EXPRESS_PASSWORD}
      ME_CONFIG_SITE_BASEURL: /mongo/
      ME_CONFIG_OPTIONS_EDITORTHEME: ambiance
      ME_CONFIG_REQUEST_SIZE: 100kb
    healthcheck:
      test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost:8081/mongo/"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 10s
    logging:
      driver: "json-file"
      options:
        max-size: "5m"
        max-file: "3"
```

### admin/chronograf/docker-compose.yml
```yaml
version: '3.8'

services:
  chronograf:
    image: chronograf:1.10-alpine
    container_name: chronograf
    restart: unless-stopped
    environment:
      INFLUXDB_URL: http://influxdb:8086
      INFLUXDB_USERNAME: ${INFLUX_USERNAME}
      INFLUXDB_PASSWORD: ${INFLUX_PASSWORD}
      BASE_PATH: /influx
      TOKEN_SECRET: ${INFLUX_ADMIN_TOKEN}
      GH_CLIENT_ID: ""
      GH_CLIENT_SECRET: ""
      GOOGLE_CLIENT_ID: ""
      GOOGLE_CLIENT_SECRET: ""
    volumes:
      - chronograf_data:/var/lib/chronograf
    healthcheck:
      test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost:8888/influx/ping"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 10s
    logging:
      driver: "json-file"
      options:
        max-size: "5m"
        max-file: "3"

volumes:
  chronograf_data:
    driver: local
```

### admin/phpmyadmin/docker-compose.yml
```yaml
version: '3.8'

services:
  phpmyadmin:
    image: phpmyadmin:5-apache
    container_name: phpmyadmin
    restart: unless-stopped
    environment:
      PMA_HOST: mariadb
      PMA_PORT: 3306
      PMA_USER: ${MARIADB_USER}
      PMA_PASSWORD: ${MARIADB_PASSWORD}
      MYSQL_ROOT_PASSWORD: ${MARIADB_ROOT_PASSWORD}
      PMA_ABSOLUTE_URI: http://localhost/mariadb/
      UPLOAD_LIMIT: 100M
      MEMORY_LIMIT: 512M
      MAX_EXECUTION_TIME: 300
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost/mariadb/"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 10s
    logging:
      driver: "json-file"
      options:
        max-size: "5m"
        max-file: "3"
```

## 🚀 Backend Services

### backend/api-gateway/docker-compose.yml
```yaml
version: '3.8'

services:
  api-gateway:
    build: 
      context: .
      dockerfile: Dockerfile
    container_name: api-gateway
    restart: unless-stopped
    environment:
      - NODE_ENV=production
      - PORT=3000
      - MONGODB_URI=mongodb://${MONGO_ROOT_USERNAME}:${MONGO_ROOT_PASSWORD}@mongodb:27017/
      - INFLUXDB_URI=http://influxdb:8086
      - INFLUXDB_TOKEN=${INFLUX_ADMIN_TOKEN}
      - MARIADB_URI=mysql://${MARIADB_USER}:${MARIADB_PASSWORD}@mariadb:3306/${MARIADB_DATABASE}
      - JWT_SECRET=${JWT_SECRET}
      - API_KEY=${API_KEY}
      - CORS_ORIGIN=${CORS_ORIGIN}
      - LOG_LEVEL=${LOG_LEVEL}
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 30s
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "5"
    depends_on:
      - mongodb
      - influxdb
      - mariadb
```

### backend/api-gateway/Dockerfile
```dockerfile
FROM node:18-alpine

WORKDIR /app

# Install dependencies
COPY package*.json ./
RUN npm ci --only=production && npm cache clean --force

# Copy source code
COPY . .

# Create non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Change ownership of the app directory
RUN chown -R nodejs:nodejs /app
USER nodejs

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:3000/health || exit 1

EXPOSE 3000

CMD ["npm", "start"]
```

### backend/user-service/docker-compose.yml
```yaml
version: '3.8'

services:
  user-service:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: user-service
    restart: unless-stopped
    environment:
      - NODE_ENV=production
      - PORT=3001
      - MONGODB_URI=mongodb://${MONGO_ROOT_USERNAME}:${MONGO_ROOT_PASSWORD}@mongodb:27017/users
      - JWT_SECRET=${JWT_SECRET}
      - BCRYPT_ROUNDS=12
      - LOG_LEVEL=${LOG_LEVEL}
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3001/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 30s
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "5"
    depends_on:
      - mongodb
```

### backend/data-service/docker-compose.yml
```yaml
version: '3.8'

services:
  data-service:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: data-service
    restart: unless-stopped
    environment:
      - NODE_ENV=production
      - PORT=3002
      - INFLUXDB_URI=http://influxdb:8086
      - INFLUXDB_TOKEN=${INFLUX_ADMIN_TOKEN}
      - INFLUXDB_ORG=${INFLUX_ORG}
      - INFLUXDB_BUCKET=${INFLUX_BUCKET}
      - LOG_LEVEL=${LOG_LEVEL}
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3002/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 30s
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "5"
    depends_on:
      - influxdb
```

## 🌐 Frontend Services

### frontend/web-app/docker-compose.yml
```yaml
version: '3.8'

services:
  web-app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: web-app
    restart: unless-stopped
    environment:
      - NODE_ENV=production
      - PORT=3010
      - REACT_APP_API_URL=http://localhost/api
      - REACT_APP_WS_URL=ws://localhost/api/ws
      - GENERATE_SOURCEMAP=false
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3010/"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 30s
    logging:
      driver: "json-file"
      options:
        max-size: "5m"
        max-file: "3"
    depends_on:
      - api-gateway
```

### frontend/web-app/Dockerfile
```dockerfile
# Multi-stage build
FROM node:18-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine

# Copy built app
COPY --from=builder /app/build /usr/share/nginx/html

# Copy nginx config
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Create non-root user
RUN addgroup -g 1001 -S nginx && \
    adduser -S nginx -u 1001 -G nginx

EXPOSE 3010

CMD ["nginx", "-g", "daemon off;"]
```

### frontend/admin-dashboard/docker-compose.yml
```yaml
version: '3.8'

services:
  admin-dashboard:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: admin-dashboard
    restart: unless-stopped
    environment:
      - NODE_ENV=production
      - PORT=3011
      - REACT_APP_API_URL=http://localhost/api
      - REACT_APP_MONGO_ADMIN_URL=http://localhost/mongo
      - REACT_APP_INFLUX_ADMIN_URL=http://localhost/influx
      - REACT_APP_MARIADB_ADMIN_URL=http://localhost/mariadb
      - GENERATE_SOURCEMAP=false
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3011/"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 30s
    logging:
      driver: "json-file"
      options:
        max-size: "5m"
        max-file: "3"
    depends_on:
      - api-gateway
```

## 📜 Utility Scripts

### scripts/setup.sh
```bash
#!/bin/bash

echo "🚀 Setting up Docker Multi-Service Environment..."

# Create volumes
echo "📦 Creating Docker volumes..."
docker volume create mongodb_data
docker volume create influxdb_data
docker volume create mariadb_data
docker volume create nginx_logs
docker volume create chronograf_data

# Create directories
echo "📁 Creating required directories..."
mkdir -p volumes/{mongodb,influxdb,mariadb,nginx,logs}
mkdir -p nginx/ssl

# Set permissions
echo "🔐 Setting permissions..."
chmod -R 755 volumes/
chmod +x scripts/*.sh

# Generate SSL certificates (self-signed for development)
if [ ! -f "nginx/ssl/cert.pem" ]; then
    echo "🔒 Generating SSL certificates..."
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout nginx/ssl/key.pem \
        -out nginx/ssl/cert.pem \
        -subj "/C=ID/ST=East Java/L=Malang/O=Dev/CN=localhost"
fi

# Check if .env exists
if [ ! -f ".env" ]; then
    echo "⚠️  .env file not found! Please create .env file with required variables."
    echo "📋 See .env.example for reference."
    exit 1
fi

echo "✅ Setup completed successfully!"
echo "🎯 Run './scripts/start.sh' to start all services"
```

### scripts/start.sh
```bash
#!/bin/bash

echo "🚀 Starting Docker Multi-Service Environment..."

# Check if setup was run
if [ ! -d "volumes" ]; then
    echo "⚠️  Setup not completed. Running setup first..."
    ./scripts/setup.sh
fi

# Start services in order
echo "🗄️  Starting databases..."
docker-compose up -d mongodb influxdb mariadb

echo "⏳ Waiting for databases to be ready..."
sleep 30

echo "🔧 Starting admin tools..."
docker-compose up -d mongo-express chronograf phpmyadmin

echo "🚀 Starting backend services..."
docker-compose up -d api-gateway user-service data-service

echo "🌐 Starting frontend services..."
docker-compose up -d web-app admin-dashboard

echo "🔄 Starting nginx reverse proxy..."
docker-compose up -d nginx

echo "✅ All services started successfully!"
echo ""
echo "🌐 Access URLs:"
echo "   - Main Dashboard: http://localhost"
echo "   - Admin Panel: http://localhost/admin-panel/"
echo "   - Web App: http://localhost/app/"
echo "   - MongoDB Admin: http://localhost/mongo/"
echo "   - InfluxDB Admin: http://localhost/influx/"
echo "   - MariaDB Admin: http://localhost/mariadb/"
echo "   - API Gateway: http://localhost/api/"
echo ""
echo "📊 Check status: docker-compose ps"
echo "📋 View logs: docker-compose logs -f [service-name]"
```

### scripts/stop.sh
```bash
#!/bin/bash

echo "🛑 Stopping Docker Multi-Service Environment..."

# Stop all services
docker-compose down

echo "✅ All services stopped successfully!"
echo ""
echo "🔄 To start again: ./scripts/start.sh"
echo "🗑️  To cleanup: ./scripts/cleanup.sh"
```

### scripts/cleanup.sh
```bash
#!/bin/bash

echo "🧹 Cleaning up Docker Multi-Service Environment..."

# Stop and remove containers
echo "🛑 Stopping and removing containers..."
docker-compose down -v --remove-orphans

# Remove custom networks
echo "🌐 Removing custom networks..."
docker network prune -f

# Remove volumes (with confirmation)
read -p "❓ Do you want to delete all data volumes? This will delete ALL data! (y/N): " -n 1 -r
echo
if [[ $REPLY =~ ^[Yy]$ ]]; then
    echo "🗑️  Removing volumes..."
    docker volume rm mongodb_data influxdb_data mariadb_data nginx_logs chronograf_data 2>/dev/null || true
    echo "✅ Volumes removed!"
else
    echo "📦 Volumes preserved."
fi

# Remove unused images
echo "🧹 Removing unused images..."
docker image prune -f

echo "✅ Cleanup completed!"
```

## 📊 Monitoring & Health Checks

### Health Check Endpoints

Setiap service memiliki health check endpoint:

- **Nginx**: `http://localhost/health`
- **API Gateway**: `http://localhost/api/health`
- **User Service**: `http://localhost/api/users/health`
- **Data Service**: `http://localhost/api/data/health`
- **MongoDB**: Internal health check via mongosh
- **InfluxDB**: Internal health check via influx ping
- **MariaDB**: Internal health check via healthcheck.sh

### Monitoring Script

```bash
#!/bin/bash
# scripts/monitor.sh

echo "📊 Docker Services Health Check"
echo "================================="

services=("nginx" "mongodb" "influxdb" "mariadb" "mongo-express" "chronograf" "phpmyadmin" "api-gateway" "user-service" "data-service" "web-app" "admin-dashboard")

for service in "${services[@]}"; do
    status=$(docker-compose ps -q $service 2>/dev/null)
    if [ -n "$status" ]; then
        health=$(docker inspect --format='{{.State.Health.Status}}' $(docker-compose ps -q $service) 2>/dev/null || echo "no health check")
        echo "🔍 $service: Running ($health)"
    else
        echo "❌ $service: Not running"
    fi
done

echo ""
echo "🌐 Testing Web Endpoints:"
endpoints=("http://localhost/health" "http://localhost/api/health")

for endpoint in "${endpoints[@]}"; do
    if curl -s -f $endpoint > /dev/null; then
        echo "✅ $endpoint: OK"
    else
        echo "❌ $endpoint: Failed"
    fi
done
```

## 🛠️ Commands Reference

### Setup Commands
```bash
# Initial setup
./scripts/setup.sh

# Start all services
./scripts/start.sh

# Stop all services
./scripts/stop.sh

# Cleanup everything
./scripts/cleanup.sh

# Monitor services
./scripts/monitor.sh
```

### Individual Service Management
```bash
# Start specific services
docker-compose up -d mongodb influxdb mariadb
docker-compose up -d mongo-express chronograf phpmyadmin
docker-compose up -d api-gateway user-service data-service
docker-compose up -d web-app admin-dashboard nginx

# Restart specific service
docker-compose restart nginx

# View logs
docker-compose logs -f nginx
docker-compose logs -f api-gateway
docker-compose logs -f mongodb

# Scale services (for load testing)
docker-compose up -d --scale user-service=3
```

### Database Management
```bash
# MongoDB CLI
docker exec -it mongodb mongosh -u wena -p Wena0403

# MariaDB CLI
docker exec -it mariadb mysql -u wena -p

# InfluxDB CLI
docker exec -it influxdb influx auth list
```

### Backup & Restore
```bash
# MongoDB backup
docker exec mongodb mongodump --uri="mongodb://wena:Wena0403@localhost:27017" --out=/backup
docker cp mongodb:/backup ./backup-$(date +%Y%m%d)

# MariaDB backup
docker exec mariadb mysqldump -u wena -p mydb > backup-$(date +%Y%m%d).sql

# InfluxDB backup
docker exec influxdb influx backup /backup --token=${INFLUX_ADMIN_TOKEN}
```

## 🌐 Access URLs

| Service | URL | Credentials |
|---------|-----|-------------|
| **Admin Dashboard** | http://localhost | - |
| **Web Application** | http://localhost/app/ | - |
| **Admin Panel** | http://localhost/admin-panel/ | - |
| **API Gateway** | http://localhost/api/ | API Key required |
| **MongoDB Admin** | http://localhost/mongo/ | admin:admin123 |
| **InfluxDB Admin** | http://localhost/influx/ | wena:Wena0403 |
| **MariaDB Admin** | http://localhost/mariadb/ | wena:Wena0403 |
| **Health Check** | http://localhost/health | - |

### Direct Database Access
| Database | Host | Port | Credentials |
|----------|------|------|-------------|
| **MongoDB** | localhost | 27017 | wena:Wena0403 |
| **InfluxDB** | localhost | 8086 | wena:Wena0403 |
| **MariaDB** | localhost | 3306 | wena:Wena0403 |

## 🔒 Security Considerations

### Production Checklist
- [ ] Change default passwords in `.env`
- [ ] Use strong JWT secrets
- [ ] Enable SSL/TLS certificates
- [ ] Configure firewall rules
- [ ] Set up proper backup strategy
- [ ] Enable log rotation
- [ ] Configure rate limiting
- [ ] Set up monitoring alerts
- [ ] Use secrets management
- [ ] Enable container security scanning

### Environment Variables Security
```bash
# Secure .env file permissions
chmod 600 .env

# Use Docker secrets in production
echo "your-secret" | docker secret create jwt_secret -
```

## 🚨 Troubleshooting

### Common Issues

#### 1. Port Already in Use
```bash
# Check what's using the port
lsof -i :80
lsof -i :3306

# Kill process if needed
sudo kill -9 <PID>
```

#### 2. Volume Permission Issues
```bash
# Fix volume permissions
sudo chown -R $USER:$USER volumes/
chmod -R 755 volumes/
```

#### 3. Container Won't Start
```bash
# Check logs
docker-compose logs [service-name]

# Check container status
docker-compose ps

# Restart specific service
docker-compose restart [service-name]
```

#### 4. Database Connection Issues
```bash
# Test database connections
docker exec -it mongodb mongosh --eval "db.adminCommand('ping')"
docker exec -it mariadb mysql -u root -p -e "SELECT 1"
docker exec -it influxdb influx ping
```

#### 5. Nginx Configuration Issues
```bash
# Test nginx configuration
docker exec nginx nginx -t

# Reload nginx configuration
docker exec nginx nginx -s reload
```

### Log Locations
- **Application logs**: `docker-compose logs -f [service]`
- **Nginx logs**: `volumes/nginx/`
- **Database logs**: Within respective containers

## 📚 Additional Resources

### Documentation Links
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Nginx Configuration Guide](https://nginx.org/en/docs/)
- [MongoDB Docker Hub](https://hub.docker.com/_/mongo)
- [InfluxDB Documentation](https://docs.influxdata.com/)
- [MariaDB Docker Guide](https://hub.docker.com/_/mariadb)

### Best Practices
1. **Use specific image tags** instead of `latest`
2. **Implement health checks** for all services
3. **Use multi-stage builds** for smaller images
4. **Set resource limits** for containers
5. **Use secrets management** for sensitive data
6. **Implement proper logging** strategy
7. **Regular backup** of data volumes
8. **Monitor resource usage** and performance

## 🔄 Updates & Maintenance

### Regular Maintenance Tasks
```bash
# Update images
docker-compose pull

# Restart with new images
docker-compose up -d --force-recreate

# Clean up unused resources
docker system prune -f

# Update SSL certificates (if self-signed)
./scripts/ssl-renew.sh
```

### Version Management
- Tag your custom builds with versions
- Use environment-specific compose files
- Implement blue-green deployment for zero-downtime updates

---

## 📋 Summary

Struktur Docker Compose ini memberikan:

### ✅ **Keuntungan:**
1. **Modular Architecture** - Setiap service terpisah dan mudah dikelola
2. **Scalability** - Mudah menambah atau mengurangi service
3. **Security** - Network isolation dan environment variables
4. **Maintainability** - Konfigurasi terorganisir dengan baik
5. **Monitoring** - Health checks dan logging terintegrasi
6. **Development Friendly** - Hot reload dan easy debugging
7. **Production Ready** - SSL, security headers, dan optimisasi

### 🎯 **Use Cases:**
- **Microservices Architecture**
- **Full-stack Web Applications**
- **Data Analytics Platforms**
- **API-first Applications**
- **Multi-tenant Systems**

### 🔧 **Tech Stack:**
- **Reverse Proxy**: Nginx
- **Databases**: MongoDB, InfluxDB, MariaDB
- **Admin Tools**: Mongo Express, Chronograf, phpMyAdmin
- **Backend**: Node.js APIs
- **Frontend**: React Applications
- **Containerization**: Docker & Docker Compose

**Happy Coding! 🚀**
