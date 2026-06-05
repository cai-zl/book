# Dockerfile 详解

## 常用指令

### FROM — 基础镜像

```dockerfile
FROM node:20-alpine
FROM golang:1.22 AS builder       # AS 命名阶段（多阶段构建用）
FROM scratch                      # 空镜像（适合静态编译的 Go 程序）
```

### RUN — 执行命令

```dockerfile
# 每个 RUN 都会产生新层，用 && 合并减少层数
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl git && \
    rm -rf /var/lib/apt/lists/*

# 注意：alpine 用 apk
RUN apk add --no-cache curl git
```

### COPY / ADD — 复制文件

```dockerfile
COPY . /app                       # 复制到容器内
COPY package.json package-lock.json /app/
COPY --from=builder /app/dist /usr/share/nginx/html   # 从其他阶段复制

# ADD 会自动解压 tar 文件，也能用 URL（一般优先用 COPY）
ADD archive.tar.gz /app/
```

### WORKDIR — 工作目录

```dockerfile
WORKDIR /app                      # 后续指令都在此目录下执行
```

### ENV / ARG — 环境变量

```dockerfile
# ENV — 运行时存在
ENV NODE_ENV=production
ENV APP_PORT=3000

# ARG — 仅构建时存在
ARG VERSION=1.0.0
RUN echo "Building version ${VERSION}"
```

### EXPOSE — 声明端口

```dockerfile
EXPOSE 3000                       # 仅声明，不会自动映射（文档作用）
```

### VOLUME — 数据卷

```dockerfile
VOLUME ["/data"]                  # 声明匿名卷
```

### USER — 运行用户

```dockerfile
RUN addgroup -S app && adduser -S app -G app
USER app                          # 后续指令以 app 用户运行
```

### CMD / ENTRYPOINT — 启动命令

```dockerfile
# CMD — 容器启动命令，可被 docker run 参数覆盖
CMD ["node", "server.js"]
CMD ["npm", "start"]

# ENTRYPOINT — 入口点，不会被覆盖（CMD 会作为参数传给它）
ENTRYPOINT ["python", "app.py"]

# 组合使用：ENTRYPOINT 固定程序，CMD 提供默认参数
ENTRYPOINT ["python", "app.py"]
CMD ["--help"]                    # 默认参数，可被 docker run 覆盖
```

### HEALTHCHECK — 健康检查

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
    CMD curl -f http://localhost:3000/health || exit 1
```

## 多阶段构建

减小最终镜像体积，编译和运行分离：

```dockerfile
# ---- 构建阶段 ----
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# ---- 运行阶段 ----
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY --from=builder /app/dist ./dist
EXPOSE 3000
CMD ["node", "dist/main.js"]
```

Go 项目示例（最终镜像可以极小）：

```dockerfile
FROM golang:1.22 AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -o server .

FROM alpine:3.19
RUN apk add --no-cache ca-certificates tzdata
COPY --from=builder /app/server /server
EXPOSE 8080
CMD ["/server"]
```

## .dockerignore

在项目根目录创建 `.dockerignore`，避免无关文件进入构建上下文：

```
.git
node_modules
dist
*.md
.env
.dockerignore
Dockerfile
docker-compose*.yml
```

## 构建优化

### 利用缓存

Docker 按层构建，指令不变则使用缓存。把**变化少的放前面**：

```dockerfile
# 好：先复制依赖文件，依赖不变时跳过 npm install
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# 差：每次代码变动都重新安装依赖
COPY . .
RUN npm ci
RUN npm run build
```

### 减小镜像体积

```dockerfile
# 1. 用 alpine 替代完整镜像（node:20 → node:20-alpine，约小 10 倍）
FROM node:20-alpine

# 2. 多阶段构建（见上文）

# 3. 清理包管理器缓存
RUN npm ci && npm cache clean --force
RUN apk add --no-cache curl

# 4. 合并 RUN 指令减少层数
RUN apt-get update && \
    apt-get install -y curl && \
    rm -rf /var/lib/apt/lists/*
```

## 常用模板

### Node.js

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY . .
EXPOSE 3000
USER node
CMD ["node", "server.js"]
```

### Python

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Java (Spring Boot)

```dockerfile
FROM eclipse-temurin:21-jdk AS builder
WORKDIR /app
COPY gradlew build.gradle settings.gradle ./
COPY gradle ./gradle
RUN ./gradlew dependencies --no-daemon
COPY src ./src
RUN ./gradlew bootJar --no-daemon

FROM eclipse-temurin:21-jre
COPY --from=builder /app/build/libs/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Nginx 部署前端

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

## 构建与运行

```bash
# 构建镜像
docker build -t myapp:1.0 .
docker build -f Dockerfile.prod -t myapp:1.0 .

# 无缓存构建
docker build --no-cache -t myapp:1.0 .

# 指定平台
docker build --platform linux/amd64 -t myapp:1.0 .

# 运行
docker run -d -p 3000:3000 --name myapp myapp:1.0

# 查看镜像层信息
docker history myapp:1.0
```
