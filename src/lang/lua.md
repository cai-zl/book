# Lua

## 官方资源

- 官网: [lua.org](https://www.lua.org)
- 官方手册: [lua.org/manual/5.4](https://www.lua.org/manual/5.4/)
- Programming in Lua: [lua.org/pil](https://www.lua.org/pil/) (经典入门书)
- Lua Users Wiki: [lua-users.org/wiki](http://lua-users.org/wiki/)

## 环境安装

```bash
# macOS
brew install lua

# Linux
sudo apt install lua5.4

# 运行
lua script.lua
lua -i                   # 交互模式
```

## 常用发行版

| 发行版 | 说明 |
|--------|------|
| [Lua](https://lua.org) | 官方版本，标准实现 |
| [LuaJIT](https://luajit.org) | 高性能 JIT 编译器，兼容 Lua 5.1 |
| [LuaRocks](https://luarocks.org) | Lua 包管理器 |

```bash
# LuaRocks 安装包
luarocks install <package>
luarocks search <package>
luarocks list              # 已安装的包
```

## 推荐学习资源

- [Programming in Lua (官方)](https://www.lua.org/pil/) — Roberto Ierusalimschy 编写
- [Lua Tutorial (tutorialspoint)](https://www.tutorialspoint.com/lua/index.htm) — 快速入门
- [Learn Lua in 15 Minutes](http://tylerneylon.com/a/learn-lua/) — 速览语法

## 常见应用场景与框架

| 场景 | 框架/工具 |
|------|-----------|
| 游戏脚本 | [LÖVE](https://love2d.org)、[Defold](https://defold.com)、Roblox Luau |
| Nginx | [OpenResty](https://openresty.org)、[ngx_lua](https://github.com/openresty/lua-nginx-module) |
| Redis 脚本 | Redis 内置 Lua 支持（`EVAL` 命令） |
| Neovim 插件 | Neovim 原生 Lua 配置 |
| 嵌入式脚本 | Redis、Nginx、游戏引擎等均支持嵌入 Lua |
