# TypeScript

## 官方资源

- 官网: [typescriptlang.org](https://www.typescriptlang.org)
- 官方手册: [typescriptlang.org/docs/handbook](https://www.typescriptlang.org/docs/handbook/)
- Playground: [typescriptlang.org/play](https://www.typescriptlang.org/play)
- TypeScript Deep Dive: [basarat.gitbook.io/typescript](https://basarat.gitbook.io/typescript/)

## 环境安装

```bash
# 全局安装
npm install -g typescript

# 项目内安装
npm install -D typescript

# 初始化配置
npx tsc --init
```

## 常用命令

```bash
npx tsc                  # 编译
npx tsc --watch          # 监听模式
npx tsc --noEmit         # 仅类型检查（不输出文件）
npx tsc -p tsconfig.json # 指定配置文件
```

## 常用框架/库

| 类别 | 推荐 |
|------|------|
| 前端框架 | [React](https://react.dev)、[Vue](https://vuejs.org)、[Svelte](https://svelte.dev) |
| 全栈框架 | [Next.js](https://nextjs.org)、[Nuxt](https://nuxt.com) |
| Node 框架 | [Express](https://expressjs.com)、[Fastify](https://fastify.dev)、[Hono](https://hono.dev) |
| ORM | [Prisma](https://www.prisma.io)、[Drizzle](https://orm.drizzle.team) |
| 状态管理 | [Zustand](https://github.com/pmndrs/zustand)、[Jotai](https://jotai.org) |
| 测试 | [Vitest](https://vitest.dev)、[Playwright](https://playwright.dev) |
| 构建工具 | [Vite](https://vitejs.dev)、[esbuild](https://esbuild.github.io) |
