# Docker 常用命令

## 镜像管理

```bash
# 搜索镜像
docker search <image>

# 拉取镜像
docker pull <image>:<tag>

# 查看本地镜像
docker images

# 删除镜像
docker rmi <image>
docker rmi $(docker images -q)          # 删除所有镜像

# 构建镜像
docker build -t <name>:<tag> .
docker build -f Dockerfile.dev -t <name> .    # 指定 Dockerfile
```

## 容器管理

```bash
# 运行容器
docker run -d --name <name> -p 8080:80 <image>    # 后台运行，映射端口
docker run -it <image> /bin/bash                    # 交互式进入

# 查看运行中的容器
docker ps
docker ps -a                    # 包括已停止的

# 启动 / 停止 / 重启
docker start <container>
docker stop <container>
docker restart <container>

# 删除容器
docker rm <container>
docker rm -f <container>        # 强制删除运行中的
docker rm $(docker ps -aq)      # 删除所有容器

# 查看容器日志
docker logs <container>
docker logs -f <container>      # 实时跟踪
docker logs --tail 100 <container>
```

## 进入容器

```bash
# 在运行中的容器内执行命令
docker exec -it <container> /bin/bash
docker exec -it <container> /bin/sh     # Alpine 系列用 sh

# 拷贝文件
docker cp <container>:/path/to/file ./local/file
docker cp ./local/file <container>:/path/to/file
```

## 资源查看

```bash
# 查看容器资源占用
docker stats

# 查看容器详细信息
docker inspect <container>

# 查看容器端口映射
docker port <container>
```

## 清理

```bash
# 清理所有未使用的资源（镜像、容器、网络）
docker system prune

# 清理所有（包括未使用的镜像）
docker system prune -a

# 清理卷
docker volume prune
```

## Docker Compose

```bash
# 启动（后台）
docker compose up -d

# 停止并删除
docker compose down

# 停止并删除（包括卷）
docker compose down -v

# 查看日志
docker compose logs -f <service>

# 重启某个服务
docker compose restart <service>

# 重新构建并启动
docker compose up -d --build
```
