# Docker Compose 详解

## 基本结构

```yaml
# docker-compose.yml
version: "3.8"                    # 版本号（新版 Docker Compose 可省略）

services:
  web:
    build: .                      # 从当前目录 Dockerfile 构建
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
    depends_on:
      - db
      - redis

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: appdb
    volumes:
      - pgdata:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    volumes:
      - redisdata:/data

volumes:
  pgdata:
  redisdata:
```

## services 配置

### build — 构建配置

```yaml
services:
  app:
    build:
      context: .                           # 构建上下文目录
      dockerfile: Dockerfile.prod          # 指定 Dockerfile
      args:                                # 构建参数
        VERSION: "1.0.0"
      target: runner                       # 多阶段构建指定阶段
      cache_from:
        - myapp:latest
```

### image — 指定镜像

```yaml
services:
  app:
    image: node:20-alpine
    # build 和 image 二选一，也可以同时指定（build 后打上 image 的 tag）
    image: myapp:latest
    build: .
```

### ports — 端口映射

```yaml
ports:
  - "8080:80"                     # 主机:容器
  - "127.0.0.1:3000:3000"         # 限制只监听本机
  - "443:443"
  - "9090-9091:8080-8081"         # 端口范围
```

### environment / env_file — 环境变量

```yaml
# 直接写
environment:
  DB_HOST: db
  DB_PORT: 5432
  DEBUG: "false"

# 列表写法
environment:
  - DB_HOST=db
  - DB_PORT=5432

# 从文件加载
env_file:
  - .env
  - .env.local
```

### volumes — 数据卷

```yaml
volumes:
  # 命名卷（在顶层 volumes 声明）
  - pgdata:/var/lib/postgresql/data

  # 绑定挂载（主机路径:容器路径）
  - ./src:/app/src
  - ./config/nginx.conf:/etc/nginx/conf.d/default.conf:ro   # :ro 只读

  # 匿名卷
  - /app/node_modules
```

### depends_on — 服务依赖

```yaml
depends_on:
  db:
    condition: service_healthy       # 等待 db 健康检查通过
  redis:
    condition: service_started       # 等待 redis 启动（默认）
```

配合健康检查使用：

```yaml
services:
  db:
    image: postgres:16-alpine
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app"]
      interval: 5s
      timeout: 3s
      retries: 5
```

### networks — 网络

```yaml
services:
  app:
    networks:
      - frontend
      - backend

  db:
    networks:
      - backend

networks:
  frontend:
  backend:
    driver: bridge
    internal: true                    # 内部网络，不暴露端口到主机
```

同一网络内的服务可以用**服务名**互相访问（如 `db:5432`）。

### restart — 重启策略

```yaml
restart: always               # 总是重启
restart: on-failure           # 非正常退出时重启
restart: unless-stopped       # 除非手动停止，否则总是重启
restart: "no"                 # 不重启（默认）
```

### deploy — 部署配置（Swarm / Stack）

```yaml
deploy:
  replicas: 3
  resources:
    limits:
      cpus: "0.5"
      memory: 512M
    reservations:
      cpus: "0.1"
      memory: 128M
  restart_policy:
    condition: on-failure
    delay: 5s
    max_attempts: 3
```

### 其他常用选项

```yaml
services:
  app:
    working_dir: /app                 # 工作目录
    entrypoint: ["python", "app.py"]  # 覆盖 ENTRYPOINT
    command: ["--host", "0.0.0.0"]    # 覆盖 CMD
    user: "1000:1000"                 # 运行用户
    container_name: my-app            # 指定容器名
    hostname: app-server              # 主机名
    init: true                        # 启用 tini init（处理僵尸进程）
    stdin_open: true                  # 保持 stdin 开启（-i）
    tty: true                         # 分配终端（-t）
    privileged: true                  # 特权模式（慎用）
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 10s
    logging:
      driver: json-file
      options:
        max-size: "10m"
        max-file: "3"
```

## 常用模板

### Node + PostgreSQL

```yaml
version: "3.8"
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgres://app:secret@db:5432/appdb
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: appdb
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app"]
      interval: 5s
      timeout: 3s
      retries: 5
    restart: unless-stopped

volumes:
  pgdata:
```

### Nginx + 前端 + 后端 API

```yaml
version: "3.8"
services:
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
      - ./ssl:/etc/nginx/ssl:ro
    depends_on:
      - frontend
      - api
    restart: unless-stopped

  frontend:
    build:
      context: ./frontend
      target: runner
    restart: unless-stopped

  api:
    build:
      context: ./backend
    environment:
      DB_HOST: db
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: appdb
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app"]
      interval: 5s
      timeout: 3s
      retries: 5
    restart: unless-stopped

volumes:
  pgdata:
```

### Redis + MySQL

```yaml
version: "3.8"
services:
  redis:
    image: redis:7-alpine
    command: redis-server --appendonly yes
    volumes:
      - redisdata:/data
    ports:
      - "6379:6379"
    restart: unless-stopped

  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: rootsecret
      MYSQL_USER: app
      MYSQL_PASSWORD: secret
      MYSQL_DATABASE: appdb
    volumes:
      - mysqldata:/var/lib/mysql
    ports:
      - "3306:3306"
    command: --default-authentication-plugin=caching_sha2_password
    restart: unless-stopped

volumes:
  redisdata:
  mysqldata:
```

## 常用命令

```bash
# 启动（后台）
docker compose up -d

# 启动并重新构建
docker compose up -d --build

# 停止并删除容器、网络
docker compose down

# 停止并删除（包括卷）
docker compose down -v

# 查看日志
docker compose logs -f                    # 所有服务
docker compose logs -f app                # 指定服务
docker compose logs -f --tail=100 app     # 最后 100 行

# 查看运行状态
docker compose ps

# 在运行中的服务内执行命令
docker compose exec app sh
docker compose exec db psql -U app appdb

# 重启某个服务
docker compose restart app

# 扩缩容
docker compose up -d --scale worker=3

# 查看服务资源占用
docker compose top

# 指定文件
docker compose -f docker-compose.prod.yml up -d

# 使用多个覆盖文件
docker compose -f docker-compose.yml -f docker-compose.override.yml up -d
```
