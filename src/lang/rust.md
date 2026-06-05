# Rust

## 官方资源

- 官网: [rust-lang.org](https://www.rust-lang.org)
- 官方书: [The Rust Programming Language](https://doc.rust-lang.org/book/)
- 标准库文档: [doc.rust-lang.org/std](https://doc.rust-lang.org/std/)
- Rust by Example: [doc.rust-lang.org/rust-by-example](https://doc.rust-lang.org/rust-by-example/)
- Crate 文档: [docs.rs](https://docs.rs)
- Playground: [play.rust-lang.org](https://play.rust-lang.org/)

## 环境安装

```bash
# 官方安装器（推荐）
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# 更新
rustup update

# 查看版本
rustc --version
cargo --version
```

## 常用命令

```bash
cargo new <project>          # 创建新项目
cargo init                   # 在当前目录初始化
cargo build                  # 构建（debug）
cargo build --release        # 构建（release，优化编译）
cargo run                    # 编译并运行
cargo test                   # 运行测试
cargo test <test_name>       # 运行指定测试
cargo check                  # 快速检查（不生成二进制，比 build 快）
cargo fmt                    # 格式化
cargo clippy                 # Lint
cargo add <crate>            # 添加依赖
cargo doc --open             # 生成本地文档并打开
cargo update                 # 更新依赖
```

## 常用框架/库

| 类别 | 推荐 |
|------|------|
| Web 框架 | [Axum](https://github.com/tokio-rs/axum)、[Actix Web](https://actix.rs) |
| 异步运行时 | [Tokio](https://tokio.rs) |
| 序列化 | [Serde](https://serde.rs) |
| HTTP 客户端 | [reqwest](https://github.com/seanmonstar/reqwest) |
| CLI | [Clap](https://github.com/clap-rs/clap) |
| 数据库 | [SQLx](https://github.com/launchbadge/sqlx)、[Diesel](https://diesel.rs) |
| 错误处理 | [Anyhow](https://github.com/dtolnay/anyhow)、[Thiserror](https://github.com/dtolnay/thiserror) |
| 日志 | [Tracing](https://github.com/tokio-rs/tracing) |
