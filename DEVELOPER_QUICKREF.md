# OpenHuman Developer Cheat Sheet

## 初始设置

```bash
# 1. 安装依赖
pnpm install

# 2. 复制环境变量文件
cp .env.example .env

# 3. 加载环境变量（每次开发前执行）
# Bash (macOS/Linux/Git Bash):
source scripts/load-dotenv.sh

# PowerShell (Windows): 手动加载 .env
Get-Content .env | ForEach-Object {
    if ($_ -match "^([^=]+)=(.*)$") {
        [Environment]::SetEnvironmentVariable($matches[1], $matches[2], "Process")
    }
}

# 或者用 Git Bash（推荐）
source scripts/load-dotenv.sh
```

## 开发启动

```bash
# Vite 开发服务器（仅前端，无 Tauri）
pnpm dev

# 完整 Tauri 桌面应用（含 CEF runtime）
pnpm dev:app

# Windows 专用
pnpm dev:app:win
```

## 构建与检查

```bash
pnpm build              # 生产构建
pnpm typecheck          # TypeScript 类型检查 (= compile)
pnpm lint               # ESLint 检查
pnpm format             # Prettier 格式化 + cargo fmt
pnpm format:check       # 仅检查格式
```

## 测试

```bash
pnpm test               # Vitest 单元测试
pnpm test:coverage      # 覆盖率报告
pnpm test:rust          # Rust 核心测试
pnpm test:e2e           # E2E 测试
pnpm test:e2e:flows     # E2E 完整流程
```

## Rust 开发

```bash
cargo check --manifest-path Cargo.toml
cargo build --bin openhuman-core --manifest-path Cargo.toml

# 桌面端 Rust 检查
pnpm rust:check
```

## Debug 工具

```bash
# Vitest（单元测试）
pnpm debug unit                          # 全部
pnpm debug unit src/components/Foo.test.tsx  # 单文件
pnpm debug unit -t "renders empty state"    # 按名称过滤

# WDIO E2E（一次跑一个 spec）
pnpm debug e2e test/e2e/specs/smoke.spec.ts

# Rust 测试
pnpm debug rust
pnpm debug rust json_rpc_e2e

# 查看 Debug 日志
pnpm debug logs                  # 列出最近 50 个
pnpm debug logs last             # 打印最新（最后 400 行）
pnpm debug logs last --tail 100  # 最后 100 行
```

## Mock 后台

```bash
pnpm mock:api   # 启动 mock API 服务器
```

## 关键文件路径

| 路径 | 说明 |
|------|------|
| `app/src/` | React 前端源码 |
| `app/src-tauri/` | Tauri/Rust 桌面端 |
| `src/` | Rust 核心库 |
| `src/openhuman/` | 业务域（channels, agent, memory 等） |
| `src/core/` | 传输层（HTTP/JSON-RPC/CLI） |
| `src/main.rs` | CLI 入口 |
| `gitbooks/developing/` | 架构文档 |

## 环境变量说明

| 变量 | 说明 |
|------|------|
| `OPENHUMAN_APP_ENV` | `production` 或 `staging` |
| `OPENHUMAN_CORE_PORT` | 核心 RPC 端口（默认 7788） |
| `OPENHUMAN_CORE_TOKEN` | RPC 认证 token |
| `BACKEND_URL` | 后端 API 地址 |
| `VITE_BACKEND_URL` | 前端对应的后端地址 |

## 常见问题

**Q: `pnpm dev` 报端口占用？**
→ `lsof -i :5173` 或检查是否有其他 Vite 进程

**Q: Rust 编译失败？**
→ `cargo check --manifest-path Cargo.toml` 先检查

**Q: E2E 测试跑不过？**
→ 确认 mock API 已启动：`pnpm mock:api`

**Q: Windows 下 `source` 命令不识别？**
→ Bash/Git Bash: `source scripts/load-dotenv.sh`
→ PowerShell: `. .\scripts\load-dotenv.sh`（用点号，不是 source）

**Q: Node 版本要求 >= 24.0.0？**
→ 用 nvm-windows（https://github.com/coreybutler/nvm-windows/releases）安装，安装到 `D:\nvm` 避开 Program Files 空格。
→ 安装后需要管理员权限 PowerShell 执行 `nvm use 24`。
→ 每次切换 Node 版本后需要 `npm install -g pnpm` 重新安装 pnpm。