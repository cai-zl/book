# Claude Code 使用指南

## 安装

```bash
# npm 安装
npm install -g @anthropic-ai/claude-code

# 验证安装
claude --version
```

## 启动

```bash
# 在项目目录下启动
cd my-project
claude

# 指定初始提示
claude "帮我重构这个函数"

# 非交互模式（管道友好）
echo "解释这个函数" | claude
cat error.log | claude "分析这段错误日志"
```

## 常用斜杠命令

```
/help                # 查看帮助
/init                # 初始化 CLAUDE.md 项目文档
/clear               # 清空对话上下文
/compact             # 压缩上下文（对话过长时）
/config              # 打开配置
/model               # 切换模型
/fast                # 切换快速模式（Opus 加速输出）

# 权限相关
/permissions         # 查看当前权限设置
/allowed-tools       # 查看已允许的工具

# 其他
/cost                # 查看本次会话花费
/status              # 查看状态
/bug                 # 报告 bug
```

## CLAUDE.md 项目配置

在项目根目录创建 `CLAUDE.md`，Claude Code 启动时会自动读取，作为项目级指令：

```markdown
# 项目：My App

## 技术栈
- 后端：Go 1.22 + Gin
- 前端：React + TypeScript
- 数据库：PostgreSQL

## 代码规范
- 使用 Go 标准错误处理模式
- 前端组件使用函数式组件 + Hooks
- 提交信息格式：type(scope): message

## 项目结构
- cmd/         入口
- internal/    内部包
- api/         HTTP handler
- web/         前端代码
```

也支持子目录级配置，在子目录下创建 `.claude/CLAUDE.md` 可以覆盖或补充。

## 常用场景

### 代码审查

```bash
# 审查当前改动
claude "review 我的代码改动"

# 审查特定文件
claude "review src/auth/login.go 有没有安全问题"
```

### 修复 Bug

```bash
# 粘贴错误信息
claude "这个错误怎么修：panic: runtime error: invalid memory address"

# 让它看日志分析
cat logs/error.log | claude "分析这些错误日志的根因"
```

### 生成代码

```bash
# 生成新功能
claude "添加一个健康检查接口 GET /healthz"

# 生成测试
claude "为 src/auth/login.go 生成单元测试"
```

### Git 操作

```bash
# 生成提交信息
claude "根据当前改动生成 commit message"

# 创建 PR
claude "帮我创建一个 PR，总结所有改动"
```

### 解释代码

```bash
# 解释整个模块
claude "解释 internal/middleware/ 下的认证流程"

# 解释某个函数
claude "解释 src/utils/validator.go 里的 ValidateJSON 函数"
```

## 权限管理

Claude Code 执行操作前会请求权限，可以在配置中预授权：

```json
// .claude/settings.json
{
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Bash(go test *)",
      "Bash(npm run *)",
      "Read",
      "Edit"
    ],
    "deny": [
      "Bash(rm -rf *)"
    ]
  }
}
```

## MCP Servers

通过 MCP (Model Context Protocol) 扩展 Claude Code 的能力：

```json
// .claude/settings.json
{
  "mcpServers": {
    "my-server": {
      "command": "npx",
      "args": ["-y", "my-mcp-server"]
    }
  }
}
```

## 键盘快捷键

| 快捷键 | 功能 |
|--------|------|
| `Enter` | 发送消息 |
| `Shift + Enter` | 换行 |
| `Escape` | 取消当前操作 |
| `Ctrl + C` | 中断 |
| `↑` / `↓` | 浏览历史消息 |
| `Tab` | 自动补全 |

## 实用技巧

- **上下文过长时**用 `/compact` 压缩，避免 token 浪费
- **大项目**在 `CLAUDE.md` 里写清楚项目结构，减少探索开销
- **复杂任务**用 `@文件名` 引用特定文件，让 Claude 聚焦
- **迭代修改**不满意时直接说"不对，改一下 XXX"，比重新开始高效
- **批量操作**可以一次让 Claude 改多个文件，比一个个改快
