# AGENTS.md - Agency Specs

> AI 代理工作指南 - Spec Driven Development 工作流

## 仓库概览

这是一个 **Spec Driven Development (规范驱动开发)** 仓库，采用实验性的结构化工作流来管理软件变更。

```
/Volumes/code/agency-specs/
├── agency/              # 代码目录 (Nx monorepo)
├── openspec/            # OpenSpec 配置和变更管理
├── .claude/             # Claude Code 技能和命令
└── .qoder/              # Qoder 配置
```

## 核心概念

### Spec Driven Development 工作流

本仓库使用 `openspec` CLI 工具管理软件开发的全生命周期：

```
1. Propose (提案) → 2. Design (设计) → 3. Tasks (任务) → 4. Apply (实现) → 5. Archive (归档)
```

### 关键目录

| 目录 | 用途 |
|------|------|
| `openspec/changes/` | 活跃的变更提案 |
| `openspec/changes/archive/` | 已完成的归档变更 |
| `openspec/specs/` | 共享的规范文档 |
| `agency/` | Nx monorepo 代码库 |

## 可用 Skills

仓库配置了以下 Claude Code Skills（位于 `.claude/skills/`）：

### 1. `openspec-propose`
**用途**: 创建新的变更提案并生成所有必需的工件

**触发**: `/opsx:propose` 或 `/opsx:propose <change-name>`

**流程**:
1. 运行 `openspec new change "<name>"` 创建变更目录
2. 生成 `proposal.md` - 描述"做什么"和"为什么"
3. 生成 `design.md` - 描述"怎么做"
4. 生成 `tasks.md` - 实现步骤清单

**输出**: `openspec/changes/<name>/`

### 2. `openspec-apply-change`
**用途**: 实现变更中的任务

**触发**: `/opsx:apply` 或 `/opsx:apply <change-name>`

**流程**:
1. 读取变更的上下文文件（proposal/design/tasks）
2. 按顺序执行 `tasks.md` 中的任务
3. 完成一个任务后标记 `- [x]`
4. 所有任务完成后提示归档

### 3. `openspec-archive-change`
**用途**: 归档已完成的变更

**触发**: `/opsx:archive` 或 `/opsx:archive <change-name>`

**流程**:
将 `openspec/changes/<name>/` 移动到 `openspec/changes/archive/`

### 4. `openspec-explore`
**用途**: 探索阶段 - 在正式提案前澄清需求和思路

**触发**: `/opsx:explore`

**流程**:
- 思考问题、调查问题、澄清需求
- 不涉及代码修改，纯思考阶段

## 开发工作流

### 开始一个新变更

```bash
# 方法1: 使用 Claude 命令
/opsx:propose my-new-feature

# 方法2: 手动创建
openspec new change "my-new-feature"
# 然后手动编辑 openspec/changes/my-new-feature/ 下的文件
```

### 实现变更

```bash
# 方法1: 使用 Claude 命令
/opsx:apply my-new-feature

# 方法2: 手动实现
# 1. 阅读 openspec/changes/my-new-feature/tasks.md
# 2. 修改 agency/ 下的代码
# 3. 手动更新任务状态
```

### 完成变更

```bash
# 方法1: 使用 Claude 命令
/opsx:archive my-new-feature

# 方法2: 手动归档
openspec archive change "my-new-feature"
```

## 代码目录结构

### `agency/` - Nx Monorepo

```
agency/
├── apps/
│   ├── agency/          # Fastify 后端应用
│   ├── agency-e2e/      # E2E 测试
│   └── client/          # Modern.js + React 前端
├── nx.json              # Nx 配置
├── biome.json           # 代码规范
└── package.json
```

**技术栈**:
- **后端**: Fastify 5 + TypeScript 5.9 + Node.js 20+
- **前端**: Modern.js 3 + React 19
- **构建**: Nx + esbuild + Rspack
- **包管理**: pnpm workspace

**常用命令**:
```bash
cd agency
npx nx serve agency      # 启动后端
npx nx serve client      # 启动前端 (cd apps/client && pnpm dev)
npx nx build agency      # 构建后端
npx nx test agency       # 运行测试
npx biome check .        # 代码检查
```

详见 `agency/AGENTS.md`

## OpenSpec CLI 命令参考

```bash
# 变更管理
openspec new change "<name>"           # 创建新变更
openspec list                          # 列出所有变更
openspec list --json                   # JSON 格式列出
openspec status --change "<name>"      # 查看变更状态
openspec status --change "<name>" --json

# 归档
openspec archive change "<name>"       # 归档变更

# 获取指令
openspec instructions <artifact> --change "<name>" --json
openspec instructions apply --change "<name>" --json
```

## 配置

### `openspec/config.yaml`

```yaml
schema: spec-driven
# context: |          # 项目上下文（可选）
#   Tech stack: ...
# rules:             # 工件规则（可选）
#   proposal:
#     - Keep proposals under 500 words
```

## 注意事项

1. **不要直接修改已归档的变更** - 归档后的变更位于 `openspec/changes/archive/`，只读
2. **保持变更聚焦** - 每个变更应该解决一个独立的问题
3. **最小化代码变更** - 实现时只做必要的修改
4. **不要修改测试文件** - 除非明确要求
5. **agency 是 symlink** - 实际路径是 `/Volumes/code/agency`

## 工作流图示

```
┌─────────────────────────────────────────────────────────────┐
│                    Spec Driven Development                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   User Idea ──► /opsx:explore ──► Clarify requirements     │
│                                      │                      │
│                                      ▼                      │
│   /opsx:propose ──► proposal.md ──► design.md ──► tasks.md │
│                                                           │
│                                      │                      │
│                                      ▼                      │
│                    /opsx:apply ──► Implement tasks         │
│                                                           │
│                                      │                      │
│                                      ▼                      │
│                    /opsx:archive ──► Archive change        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

## 相关文件

- `agency/AGENTS.md` - 代码目录的详细指南
- `.claude/skills/*/SKILL.md` - 各技能的详细说明
- `openspec/config.yaml` - OpenSpec 配置文件
