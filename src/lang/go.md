# Go

## 官方资源

- 官网: [go.dev](https://go.dev)
- 官方教程: [go.dev/tour](https://go.dev/tour/)
- 官方文档: [go.dev/doc](https://go.dev/doc)
- 包文档: [pkg.go.dev](https://pkg.go.dev)
- Playground: [go.dev/play](https://go.dev/play/)
- 博客: [go.dev/blog](https://go.dev/blog)

## 环境安装

```bash
# macOS
brew install go

# Linux
wget https://go.dev/dl/go1.22.0.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.22.0.linux-amd64.tar.gz

# 配置环境变量
export PATH=$PATH:/usr/local/go/bin
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
```

## 常用命令

```bash
go mod init <module>        # 初始化模块
go mod tidy                 # 整理依赖
go get <package>            # 添加依赖
go build ./...              # 构建
go run main.go              # 运行
go test ./...               # 测试
go test -v -race ./...      # 详细输出 + 竞态检测
go test -cover ./...        # 覆盖率
go vet ./...                # 静态分析
go fmt ./...                # 格式化
go install <package>@latest # 安装工具
```

## 常用框架/库

| 类别 | 推荐 |
|------|------|
| Web 框架 | [Gin](https://github.com/gin-gonic/gin)、[Echo](https://github.com/labstack/echo)、[Chi](https://github.com/go-chi/chi) |
| ORM | [GORM](https://gorm.io)、[Ent](https://entgo.io) |
| 配置 | [Viper](https://github.com/spf13/viper) |
| 日志 | [Zap](https://github.com/uber-go/zap)、[Slog](https://pkg.go.dev/log/slog) (标准库) |
| CLI | [Cobra](https://github.com/spf13/cobra) |
| HTTP 客户端 | [Resty](https://github.com/go-resty/resty) |
| 定时任务 | [robfig/cron](https://github.com/robfig/cron) |
