# Opencode + OpenSpec Workflow Skill

## Role

AI 编码助手的 **OpenSpec 开发主流程** 控制器。

## 核心规则

### 必须遵守的协议
- 🚫 **禁止**跳过 spec 直接写代码
- ✅ 每次新需求必须先执行 `/opsx-propose` 初始化，或使用 `/opsx-new`（旧版）
- ✅ **OPSX-CONTINUE 是循环过程**：每次执行生成/更新一个 OpenSpec 工件
- ✅ 循环直到所有工件（proposal/specs/design/tasks）都生成完成
- ✅ spec 完成后执行 `/opsx-apply` 让 Opencode 编码
- ✅ 编码后由 **用户** 验证功能
- ✅ 验证通过后执行 `/opsx-archive` 归档本次变更
- ✅ **所有操作后，AI 必须审核代码并执行 `git commit -s && git push`**

### 开发流程（AI 严格执行）

**新版流程（推荐 `propose` + `continue` 循环）：**
```
用户发起需求
    ↓
OPSX-PROPOSE (<需求描述>) → 创建 openspec/changes/<name>/
    ↓
OPSX-CONTINUE (<反馈1>) → 在 <name>/ 下生成 proposal.md
    ↓
OPSX-CONTINUE (<反馈2>) → 在 <name>/ 下生成 specs/ 目录
    ↓
OPSX-CONTINUE (<反馈3>) → 在 <name>/ 下生成 design.md
    ↓
OPSX-CONTINUE (<反馈4>) → 在 <name>/ 下生成 tasks.md
    ↓
所有工件生成完成 → 用户确认 spec
    ↓
OPSX-APPLY → Opencode 根据所有工件编码
    ↓
OPSX-VERIFY → 用户验证功能
    ↓
OPSX-ARCHIVE → 归档变更到 archive/YYYY-MM-DD-name/
    ↓
AI 审核代码
    ↓
用户 git commit -s && git push
```

**旧版流程（`new` + `continue` 更新 project.md）：**
> ⚠️ 旧版已废弃，不再推荐使用

## Command Index

| Command | 作用 | 文件位置 |
|---------|------|----------|
| `/opsx-new <description>` | 初始化新需求 spec（旧版） | `openspec/project.md` |
| `/opsx-propose <description>` | **推荐**：初始化新需求 spec，创建 `openspec/changes/<name>/` | `openspec/changes/<name>/` |
| `/opsx-continue <feedback>` | **循环过程**：每次执行生成/更新一个工件 | `openspec/changes/<name>/{proposal,specs,design,tasks}` |
| `/opsx-apply` | 让 Opencode 根据所有工件编码 | 自动生成代码 |
| `/opsx-verify` | 用户验证 Opencode 的输出 | - |
| `/opsx-archive` | 归档本次变更记录 | `openspec/changes/archive/YYYY-MM-DD-name/` |

## OpenSpec 工件（Artifacts）

每次 `/opsx-continue` 会生成一个工件：

| 工件 | 说明 | 生成顺序 |
|------|------|----------|
| `proposal.md` | 为什么做这个，有什么价值 | 第1个 |
| `specs/` | 需求描述和场景 | 第2个 |
| `design.md` | 技术方案和架构 | 第3个 |
| `tasks.md` | 实施检查清单 | 第4个 |

**循环直到所有工件都存在且完整**，然后执行 `/opsx-apply`。

## Project Structure

```
/workspace/develop/<project>/
├── .opencode/
│   └── command/
│       ├── opsx-new.md
│       ├── opsx-continue.md
│       ├── opsx-apply.md
│       ├── opsx-verify.md
│       └── opsx-archive.md
├── openspec/
│   └─ changes/
│       └── <change-name>/           ← 每个需求一个独立文件夹
│           ├─ proposal.md           ← 工件1：为什么做
│           ├─ specs/                ← 工件2：需求场景
│           ├─ design.md             ← 工件3：技术方案
│           └─ tasks.md              ← 工件4：实施清单
│       └── archive/                 ← 归档目录
│           └── 2025-01-23-name/     ← 归档的变更
└── <code>/                          ← Opencode 自动生成
```

## Key Notes

- 📁 Spec 存在于 `openspec/changes/<name>/` 文件夹，**不是**单个 `project.md`
- 🔄 **OPSX-CONTINUE 是循环过程**：每次执行处理一个工件
- 🧠 AI **必须先理解需求**再写 spec，不能凭空猜测
- 📋 每次 CONTINUE 可能创建新工件或更新已有工件
- 🚫 AI 不跳过任何环节，不"先写代码再补 spec"
- 🧑‍💻 **代码审核责任**：AI 必须审核 Opencode 的输出，用户负责最终功能验证

## Error Handling

- 如果没有 `openspec/changes/` 目录 → 提示用户先执行 `/opsx-propose`
- 如果某个工件内容空 → 提示用户补充对应内容
- 如果 Opencode 编码失败 → 建议用户检查配置 / 重试
- 如果用户需要暂停/取消 → 建议先执行 `/opsx-verify` 再中断

## Loading

Skill is loaded automatically when `openspec/changes/` folder exists.

*Last updated: 2026-03-01*
