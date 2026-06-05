# Nginx 配置

## 配置文件结构

```
/etc/nginx/
├── nginx.conf              # 主配置
├── conf.d/                 # 自定义配置（推荐放这里）
│   └── app.conf
├── sites-available/        # 可用站点（Debian/Ubuntu）
├── sites-enabled/          # 已启用站点
└── mime.types
```

主配置一般只做全局设置，具体站点放在 `conf.d/` 或 `sites-enabled/` 下：

```nginx
# /etc/nginx/nginx.conf
worker_processes auto;

events {
    worker_connections 1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile      on;
    keepalive_timeout 65;

    include /etc/nginx/conf.d/*.conf;
    # include /etc/nginx/sites-enabled/*;
}
```

## 反向代理

```nginx
server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host              $host;
        proxy_set_header X-Real-IP         $remote_addr;
        proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # WebSocket 支持
    location /ws/ {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade    $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

## HTTPS / SSL

```nginx
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate     /etc/nginx/ssl/cert.pem;
    ssl_certificate_key /etc/nginx/ssl/key.pem;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers  HIGH:!aNULL:!MD5;

    location / {
        proxy_pass http://127.0.0.1:8080;
    }
}

# HTTP 跳转 HTTPS
server {
    listen 80;
    server_name example.com;
    return 301 https://$host$request_uri;
}
```

用 Certbot 自动管理 Let's Encrypt 证书：

```bash
# 安装并获取证书（自动修改 nginx 配置）
sudo certbot --nginx -d example.com

# 自动续期（certbot 会自动加 cron）
sudo certbot renew --dry-run
```

## 静态资源服务

### 部署前端 SPA

```nginx
server {
    listen 80;
    server_name app.example.com;
    root /var/www/app;
    index index.html;

    # SPA 路由：所有路径回退到 index.html
    location / {
        try_files $uri $uri/ /index.html;
    }

    # 静态资源长缓存（文件名带 hash 的情况）
    location /assets/ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # 禁止缓存 index.html
    location = /index.html {
        add_header Cache-Control "no-cache";
    }

    # 开启 gzip
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml;
    gzip_min_length 1024;
}
```

### 文件下载服务

```nginx
location /download/ {
    alias /data/files/;
    autoindex on;               # 开启目录列表
    autoindex_exact_size off;   # 显示友好大小
}
```

## 负载均衡

```nginx
upstream backend {
    # 默认轮询（round-robin）
    server 127.0.0.1:8080;
    server 127.0.0.1:8081;
    server 127.0.0.1:8082;

    # 权重
    # server 127.0.0.1:8080 weight=3;
    # server 127.0.0.1:8081 weight=1;

    # IP Hash（会话保持）
    # ip_hash;

    # 最少连接
    # least_conn;

    # 健康检查
    # server 127.0.0.1:8083 backup;    # 备用节点
    # server 127.0.0.1:8084 down;      # 标记下线
}

server {
    listen 80;
    location / {
        proxy_pass http://backend;
    }
}
```

## 常用优化

### 连接与超时

```nginx
# /etc/nginx/nginx.conf

worker_processes auto;              # CPU 核心数
worker_rlimit_nofile 65535;         # 最大文件描述符

events {
    worker_connections 4096;         # 单个 worker 连接数
    use epoll;                       # Linux 使用 epoll
}

http {
    keepalive_timeout 65;            # 长连接超时
    client_max_body_size 50m;        # 请求体上限（默认 1m）
    client_body_timeout 30s;
    send_timeout 30s;
}
```

### 代理缓冲

```nginx
http {
    proxy_buffer_size      4k;
    proxy_buffers          8 4k;
    proxy_busy_buffers_size 8k;
}
```

### 日志格式

```nginx
http {
    log_format main '$remote_addr - $remote_user [$time_local] '
                    '"$request" $status $body_bytes_sent '
                    '"$http_referer" "$http_user_agent" '
                    '$request_time';

    access_log /var/log/nginx/access.log main;
    error_log  /var/log/nginx/error.log warn;
}
```

### 安全相关

```nginx
server {
    # 隐藏版本号
    server_tokens off;

    # 安全头
    add_header X-Frame-Options       "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";
    add_header X-XSS-Protection      "1; mode=block";

    # 禁止访问隐藏文件
    location ~ /\. {
        deny all;
    }
}
```

## 常用命令

```bash
# 测试配置语法
sudo nginx -t

# 重载配置（不中断服务）
sudo nginx -s reload

# 启动 / 停止
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx

# 查看状态
sudo systemctl status nginx

# 查看版本
nginx -v
```
