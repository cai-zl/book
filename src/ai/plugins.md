# Claude Code 插件

实用的 Claude Code 插件及配置方法。

## claude-mem — 跨会话持久记忆

让 Claude Code 在不同会话之间保留上下文，记住架构决策、Bug 修复、实现细节等。

- GitHub: [thedotmack/claude-mem](https://github.com/thedotmack/claude-mem)
- 文档: [docs.claude-mem.ai](https://docs.claude-mem.ai)

### 安装

```bash
# 使用官方安装器（推荐，会自动注册 hooks 和配置）
npx claude-mem@latest install
```

> 注意：`npm install -g claude-mem` 只安装 SDK 库，不会注册插件 hooks。

### 功能

- 自动记录每次会话的观察（observations）
- 下次会话启动时自动注入相关上下文
- 支持 `/learn-codebase` 一次性索引整个仓库
- 内置多种技能（skills）：
  - `/make-plan` — 创建分阶段实施计划
  - `/do` — 执行计划
  - `/babysit` — 监控 PR 直到可合并
  - `/mem-search` — 搜索历史记忆

---

## codegraph — 代码知识图谱

基于 SQLite 的代码符号图谱，毫秒级查询，在写代码前先了解代码结构。

- GitHub: [crsmithmore/codegraph](https://github.com/crsmithmore/codegraph)

### 安装

```bash
npx codegraph@latest install
```

### 功能

- 自动索引项目中的所有符号、调用关系、文件结构
- 提供以下核心能力：
  - `codegraph_explore` — 用自然语言或符号名探索代码（主要工具，一次调用返回相关源码）
  - `codegraph_search` — 按名称搜索符号
  - `codegraph_callers` / `codegraph_callees` — 查看调用关系
  - `codegraph_impact` — 分析修改某个符号会影响哪些代码
  - `codegraph_files` — 项目文件树索引

### 适用场景

- "这个函数是怎么工作的"
- "谁调用了这个方法"
- "改这个接口会影响什么"
- "项目的整体架构是怎样的"

---

## RTK (Rust Token Killer) — Token 用量压缩

CLI 代理工具，拦截并压缩常见开发命令的输出，减少 LLM token 消耗 60-90%。

- GitHub: [rtk-ai/rtk](https://github.com/rtk-ai/rtk)

### 安装

```bash
# 安装并注册 Claude Code hook
rtk init -g
```

### 工作原理

RTK 作为代理坐在 Claude Code 和终端之间，自动拦截和精简以下类型的命令输出：

- `git status`、`git log`、`git diff`
- `ls`、`cat`、`find`
- `npm test`、`pytest`、`cargo build`
- 100+ 其他常用命令

精简后的输出保留关键信息，丢弃冗余内容，从而大幅减少 token 消耗。

### 效果

| 场景 | 原始 token | 压缩后 | 节省 |
|------|-----------|--------|------|
| git status | ~2000 | ~200 | ~90% |
| npm test | ~5000 | ~800 | ~84% |
| ls -la | ~1500 | ~150 | ~90% |

---

## superpowers — 结构化开发工作流

强制 Claude 在编码前先规划、先写测试、写完自审，提升代码质量。

- GitHub: [obra/superpowers](https://github.com/obra/superpowers)
- GitHub Stars: 94k+

### 安装

```bash
npx superpowers@latest install
```

### 工作流程

安装后 Claude 自动遵循以下流程，无需手动配置：

```
1. 头脑风暴（Brainstorming）
   → Claude 先理解需求，提出方案供选择

2. 规划（Planning）
   → 确定方案后输出详细实施步骤

3. 测试驱动（TDD）
   → 先写测试，再写实现代码

4. 代码审查（Code Review）
   → 实现完成后自动进行两轮自审
```

### 特点

- 零配置，安装即生效
- 支持 Claude Code、Cursor、Codex
- 防止 Claude 一上来就写代码，强制先思考
- 包含 Chrome 浏览器控制能力（superpowers-chrome）

---

## andrej-karpathy-skills — 行为准则（Karpathy 风格）

源自 Andrej Karpathy 对 LLM 编码问题的观察，用一个 CLAUDE.md 文件约束 Claude Code 的行为，减少常见错误。

- GitHub: [multica-ai/andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills)

### 解决的问题

> "模型会替你做错误的假设并一路跑下去，不管理困惑、不寻求澄清、不暴露矛盾、不展示权衡、该反驳时不反驳。"

> "它们非常喜欢过度复杂化代码和 API，膨胀抽象，不清理死代码……100 行能搞定的事非要写 1000 行。"

> "它们有时会顺手改掉/删掉自己不够理解的注释和代码，即使这些跟任务无关。"

### 四条原则

| 原则 | 解决什么问题 |
|------|-------------|
| **Think Before Coding** | 错误假设、隐藏困惑、缺少权衡 |
| **Simplicity First** | 过度复杂化、膨胀的抽象 |
| **Surgical Changes** | 无关改动、乱碰不该碰的代码 |
| **Goal-Driven Execution** | 通过测试先行、可验证的成功标准 |

#### 1. Think Before Coding — 先想再写

- 不确定时主动提问，不要猜
- 存在歧义时列出多种理解
- 有更简单的方案时要反驳
- 搞不清楚就停下来问

#### 2. Simplicity First — 简单至上

- 不加没要求的功能
- 只用一次的代码不抽象
- 不加没要求的"灵活性"和"可配置性"
- 不处理不可能发生的错误
- 200 行能写完的就不要写 50 行以上

> 检验标准：一个高级工程师会说这过于复杂吗？如果是，就简化。

#### 3. Surgical Changes — 精准改动

- 不要"顺手改进"相邻的代码、注释或格式
- 不要重构没坏的东西
- 匹配现有风格，即使你会用不同写法
- 发现无关死代码时提一句，不要直接删
- 你自己的改动造成的孤儿代码（无用的 import/变量/函数）要清理

> 检验标准：每一行改动都应该能追溯到用户的请求。

#### 4. Goal-Driven Execution — 目标驱动

把命令式任务转化为可验证的目标：

| 不要这样写 | 改成这样 |
|-----------|---------|
| "添加验证" | "为无效输入写测试，然后让测试通过" |
| "修这个 Bug" | "写一个能复现的测试，然后让它通过" |
| "重构 X" | "确保重构前后测试都通过" |

多步任务先列计划：

```
1. [步骤] → 验证: [检查方式]
2. [步骤] → 验证: [检查方式]
3. [步骤] → 验证: [检查方式]
```

### 安装

**方式 A：Claude Code 插件（推荐）**

```
/plugin marketplace add forrestchang/andrej-karpathy-skills
/plugin install andrej-karpathy-skills@karpathy-skills
```

**方式 B：直接下载 CLAUDE.md**

新项目：

```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md
```

已有项目（追加）：

```bash
curl https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md >> CLAUDE.md
```

### 怎么判断生效了

- diff 中更少无关改动
- 更少的过度复杂化重写
- 实现前先问澄清问题
- 干净、最小化的 PR
