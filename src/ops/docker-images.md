# 实用 Docker 镜像

## 数据库

### PostgreSQL

```bash
docker run -d --name postgres \
  -e POSTGRES_USER=app \
  -e POSTGRES_PASSWORD=secret \
  -e POSTGRES_DB=appdb \
  -p 5432:5432 \
  -v pgdata:/var/lib/postgresql/data \
  postgres:16-alpine
```

### MySQL

```bash
docker run -d --name mysql \
  -e MYSQL_ROOT_PASSWORD=rootsecret \
  -e MYSQL_USER=app \
  -e MYSQL_PASSWORD=secret \
  -e MYSQL_DATABASE=appdb \
  -p 3306:3306 \
  -v mysqldata:/var/lib/mysql \
  mysql:8.0
```

### Redis

```bash
docker run -d --name redis \
  -p 6379:6379 \
  -v redisdata:/data \
  redis:7-alpine \
  redis-server --appendonly yes
```

### MongoDB

```bash
docker run -d --name mongo \
  -e MONGO_INITDB_ROOT_USERNAME=admin \
  -e MONGO_INITDB_ROOT_PASSWORD=secret \
  -p 27017:27017 \
  -v mongodata:/data/db \
  mongo:7
```

### Elasticsearch + Kibana

```bash
docker run -d --name elasticsearch \
  -p 9200:9200 \
  -e "discovery.type=single-node" \
  -e "xpack.security.enabled=false" \
  -v esdata:/usr/share/elasticsearch/data \
  docker.elastic.co/elasticsearch/elasticsearch:8.12.0

docker run -d --name kibana \
  -p 5601:5601 \
  -e "ELASTICSEARCH_HOSTS=http://host.docker.internal:9200" \
  docker.elastic.co/kibana/kibana:8.12.0
```

## 数据库管理

### Adminer（轻量数据库客户端）

支持 MySQL、PostgreSQL、SQLite、MongoDB 等。

```bash
docker run -d --name adminer \
  -p 8080:8080 \
  adminer
# 访问 http://localhost:8080
```

### pgAdmin（PostgreSQL 管理）

```bash
docker run -d --name pgadmin \
  -e PGADMIN_DEFAULT_EMAIL=admin@admin.com \
  -e PGADMIN_DEFAULT_PASSWORD=admin \
  -p 5050:80 \
  dpage/pgadmin4
# 访问 http://localhost:5050
```

### Redis Insight（Redis 管理）

```bash
docker run -d --name redis-insight \
  -p 5540:5540 \
  redis/redisinsight
# 访问 http://localhost:5540
```

### Mongo Express（MongoDB 管理）

```bash
docker run -d --name mongo-express \
  -e ME_CONFIG_MONGODB_URL="mongodb://admin:secret@host.docker.internal:27017/" \
  -p 8081:8081 \
  mongo-express
# 访问 http://localhost:8081
```

## Web 服务器 / 反向代理

### Nginx

```bash
docker run -d --name nginx \
  -p 80:80 \
  -v ./nginx.conf:/etc/nginx/conf.d/default.conf:ro \
  -v ./html:/usr/share/nginx/html:ro \
  nginx:alpine
```

### Caddy（自动 HTTPS）

```bash
docker run -d --name caddy \
  -p 80:80 -p 443:443 \
  -v ./Caddyfile:/etc/caddy/Caddyfile:ro \
  -v caddy_data:/data \
  -v caddy_config:/config \
  caddy:alpine
```

### Traefik（云原生反向代理）

```bash
docker run -d --name traefik \
  -p 80:80 -p 443:443 -p 8080:8080 \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  traefik:v3.0 \
  --api.insecure=true --providers.docker
# Dashboard: http://localhost:8080
```

## 消息队列

### RabbitMQ

```bash
# 带管理界面
docker run -d --name rabbitmq \
  -p 5672:5672 -p 15672:15672 \
  rabbitmq:3-management
# 管理界面: http://localhost:15672  guest/guest
```

### Kafka (用 Bitnami 镜像)

```bash
docker run -d --name kafka \
  -p 9092:9092 \
  -e KAFKA_CFG_NODE_ID=0 \
  -e KAFKA_CFG_PROCESS_ROLES=broker,controller \
  -e KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=0@localhost:9093 \
  -e KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093 \
  -e KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092 \
  -e KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER \
  -e KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT \
  bitnami/kafka:3.7
```

## 对象存储

### MinIO（S3 兼容）

```bash
docker run -d --name minio \
  -p 9000:9000 -p 9001:9001 \
  -e MINIO_ROOT_USER=admin \
  -e MINIO_ROOT_PASSWORD=admin123 \
  -v minio_data:/data \
  minio/minio server /data --console-address ":9001"
# API: http://localhost:9000  控制台: http://localhost:9001
```

## 监控

### Prometheus + Grafana

```bash
docker run -d --name prometheus \
  -p 9090:9090 \
  -v ./prometheus.yml:/etc/prometheus/prometheus.yml:ro \
  prom/prometheus

docker run -d --name grafana \
  -p 3000:3000 \
  -v grafana_data:/var/lib/grafana \
  grafana/grafana
# 访问 http://localhost:3000  admin/admin
```

### Jaeger（链路追踪）

```bash
docker run -d --name jaeger \
  -p 16686:16686 -p 4317:4317 \
  jaegertracing/all-in-one:1.54
# UI: http://localhost:16686  OTLP gRPC: 4317
```

## 开发工具

### Mailpit（邮件测试）

拦截 SMTP 邮件，Web 界面查看，开发调试用。

```bash
docker run -d --name mailpit \
  -p 8025:8025 -p 1025:1025 \
  axllent/mailpit
# Web: http://localhost:8025  SMTP: localhost:1025
```

### MinIO（本地 S3）

见上方对象存储章节。

### LocalStack（本地 AWS）

模拟 AWS 服务（S3、Lambda、DynamoDB、SQS 等）。

```bash
docker run -d --name localstack \
  -p 4566:4566 \
  -e SERVICES=s3,lambda,dynamodb,sqs \
  -v localstack_data:/var/lib/localstack \
  localstack/localstack
# Endpoint: http://localhost:4566
```

### Vault（密钥管理）

```bash
docker run -d --name vault \
  -p 8200:8200 \
  -e VAULT_DEV_ROOT_TOKEN_ID=root \
  vault
# 访问 http://localhost:8200  Token: root
```

## Docker 管理

### Portainer（Docker 可视化管理）

```bash
docker run -d --name portainer \
  -p 9443:9443 \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
# 访问 https://localhost:9443
```

### Dozzle（容器日志查看）

```bash
docker run -d --name dozzle \
  -p 8080:8080 \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  amir20/dozzle
# 访问 http://localhost:8080
```
