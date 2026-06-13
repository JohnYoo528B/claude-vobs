# 6_CodeData — 代码工作区

## 用户身份

- **零编程基础**：不懂任何编程语言，不会 Git，不会命令行
- **角色定位**：产品经理 + QA 测试员 + 元探索者。不只是用 AI 写代码，也主动总结与 AI 沟通的方法论和框架使用心得，沉淀为可复用的经验
- **工作方式**：Vibe Coding — 用自然语言描述需求，AI 负责所有技术实现
- **沟通语言**：中文

## AI 行为准则

### 工作流程
1. **先计划，再动手**：任何非简单任务（多文件修改、新功能、架构变更）必须先进入 Plan Mode，给出方案等用户确认后再写代码
2. **解释你在做什么**：每条命令、每次修改，用自己的话说明目的和预期结果
3. **用中文回复**：所有解释和讨论用中文
4. **把用户当零基础**：不预设用户懂术语。涉及技术概念时用类比解释

### 编程默认值
- **首选语言**：Python 3.12（已安装在 `D:\1_Download\5_AI_coding\Python\Python312\`）
- **可选语言**：按项目需求决定，但需要先跟用户说明为什么选这个语言
- **Shell**：使用 bash 语法（Claude Code 的 shell 环境是 bash），不是 PowerShell
- **项目位置**：所有新项目创建在 `e:\6_CodeData\` 下

### 约束
- **不要自作主张**：不改变已有架构、不安装全局包、不做破坏性修改，除非用户明确要求
- **写操作需确认**：创建/修改/删除文件、git 操作、安装包 — 都需要用户看到并确认
- **先读再写**：修改代码前先读一遍，理解现有逻辑
- **不推 git**：除非用户明确说"推送"或"提交"，否则不做任何 git 写操作。说"提交"时自动执行四步工作流（commit+push+日志+README），见 memory `auto-change-log.md`。VObS 系统项目（`00-Claude-VObS-公开版`）例外，git 操作完全由 Obsidian 端触发。
- **跨端写入后追加 PENDING-CHANGES**：每次跨端写入 Obsidian vault（MCP 爬取结果、开发日志等），同步追加一行到 vault 的 `_系统管理/PENDING-CHANGES.md`。格式 `- YYYY-MM-DD | 来源 | 文件 | 改了什么`。不阻塞主流程，不可跳过。

### 浏览器操控（Playwright MCP）

已配置 `@playwright/mcp`，可以操控 Chromium 浏览器完成网页操作。

**使用方式**：直接说「打开 xx 网站，做 xx」。Claude 会自动启动浏览器执行。

**典型场景**：
- 网页信息提取：「打开这个页面，把文章内容摘下来」
- 网页操作：「打开淘宝搜 xx，按价格排序」
- 跨端写入：「打开 xx 网页，把内容存到 Obsidian 草稿箱」

**跨端写入规则**（⚠️ 硬性约束）：
- MCP 爬取/提取的所有外部内容，**必须**写入 `～草稿箱/`，禁止直接写入 `1主题研究/` 或其他内容文件夹
- 写入路径：`<你的 Obsidian vault 路径>\～草稿箱\`
- frontmatter 必填：`source`（原始链接）、`status: unread`
- 后续由 Obsidian 端走正常消化流程：草稿箱 → 整理 → 升级到 1主题研究/
- 关联规范：Obsidian 端 `_系统管理/设计规范/笔记格式/外部信息摄入指南.md`

**技术备注**（供 AI 查阅，用户无需关心）：
- MCP 服务器：`playwright`（npx @playwright/mcp@latest）
- 配置文件：`C:\Users\<你的用户名>\.claude.json` → `projects.<vault>.mcpServers.playwright`
- 浏览器引擎：Chromium（`C:\Users\<你的用户名>\AppData\Local\ms-playwright\chromium-1223`）
- 配置时间：2026-06-13

## 项目结构约定

```
e:\6_CodeData\
├─ CLAUDE.md                  ← 本文件（全局规则，所有子项目共用）
├─ 01-德州扑克-20260606/      ← 每个项目一个独立文件夹
│   ├─ texas_holdem_v8.py
│   └─ equity_calculator.py
├─ 02-下一个项目-YYYYMMDD/    ← 新项目自动按此格式创建
│   └─ ...
└─ ...
```

### 项目命名规则

新建项目文件夹必须使用以下格式：
```
序号-中文项目名-YYYYMMDD
```
- **序号**：扫 `e:\6_CodeData\` 下现有文件夹，自动递增
- **中文项目名**：用中文描述项目内容
- **日期**：创建当天，格式 YYYYMMDD

两端文件夹名**必须一致**，方便 Obsidian 端自动对齐。规范文档位置：Obsidian 端 `_系统管理/设计规范/_全局规范/备忘-项目规范与开发日志流程.md` → "一、项目命名与结构规范"。

### 自动追踪规则

每次在项目中创建新源码文件（`.py` 等），必须：
1. 将文件名记录到 `~/.claude/projects/e--6-CodeData/memory/` 对应的项目记忆中
2. 确保 Obsidian 端后续读取时能知道有哪些文件需要建 `.md`

每个子项目可以有自己的 `CLAUDE.md` 补充项目专属规则。

### 新建项目标准化流程

收到"新建项目"指令后，按以下清单执行，一项不漏：

```
□ 1. 扫 e:\6_CodeData\ 确定序号，按 序号-中文项目名-YYYYMMDD 建文件夹
□ 2. 创建首个源码文件
□ 3. Obsidian 端建同名项目文件夹 + 首个同名 .md 日志文件
   → frontmatter 中 `project` 字段值必须与文件夹名逐字一致
   → 写入后回读校验：确认 `project` 值 = 文件夹名，不一致则当场修正
□ 4. 日志 .md 写入 frontmatter 模板（含 project 字段）
□ 5. 更新 MEMORY.md：在对应层级下新增项目分组
□ 6. 告知用户两端文件夹已建好

### 项目完结流程

用户说"项目完结/这个项目做完了"时：

□ 1. 运行最终验证（跑一遍核心功能，确认正常）
□ 2. 在 Obsidian 项目文件夹创建 回顾-项目总结.md（成果/收获/可复用资产/已知限制）
□ 3. 判断是否有可复用经验需要升级为全局 memory（scope: global）
□ 4. MEMORY.md 项目分组标记 ✅
□ 5. 告知用户项目已归档

> 跨端规范见 Obsidian 端 `跨端共享规范.md` §七「项目生命周期」→「完结流程」。

### 完结后维护规则

项目完结后的小修（Bug 修复、兼容性调整等，不涉及新功能或架构变更）：
- 在项目总结末尾追加 **"完结后维护记录"** 表格（日期 + 内容）
- 不改变完结状态，不重新走完结流程
- 如果是新功能（非小修），则说明项目已重新激活，移除 ✅ 标记

> 跨端规范见 Obsidian 端 `跨端共享规范.md` §七「项目生命周期」→「完结后维护规则」。
```

### 敏感信息管理

- API Key 统一放在 `settings.json` 的 `env` 中，通过环境变量传给项目代码
- 项目代码用 `os.environ.get("KEY_NAME", "")` 读取，**不硬编码**
- 新项目需要 API Key 时，在 settings.json 新增 env 条目

### 会话启动检查

每次新对话开始时，AI 自动执行以下检查（静默，不打断用户）：

0. **加载跨端共享规范**（优先）：读取 Obsidian vault 中的 `_系统管理/设计规范/核心设计规范/跨端共享规范.md`。若 vault 不可访问，跳过本条，后续跨端操作使用本地记忆中的规则。
1. 运行 `date "+%u %d"` 获取星期几（1=周一）和日期
2. **回顾提醒合并逻辑**（检查 `~/.claude/projects/e--6-CodeData/last-review.txt`）：
   - 今天周一（%u=1）且距上次回顾 >7 天 → "📅 今天周一，距上次回顾已 N 天，要不要更新历史记录？"
   - 距上次回顾 >14 天（任意天）→ "🕐 距上次系统回顾已 N 天，要不要更新历史记录？"
   - 文件不存在 → 视为首次使用，不提醒
3. **1 号**（%d=01）→ 简短提醒："📅 今天月初，要不要更新历史记录？说「更新历史记录」就行。"
4. 检查 `~/.claude/failed-ops.jsonl`（哨兵文件）：
   - 无文件或无未处理条目 → 静默跳过
   - 有条目 → 简短提醒："⚠️ 上次有 N 个操作未完成，说「恢复未完成操作」处理。"
5. 检查 `~/.claude/fallback/`（降级暂存目录）：
   - 目录为空或不存在 → 静默跳过
   - 有文件 → 简短提醒："📦 上次有 N 条内容暂存在本地，说「恢复暂存内容」迁移。"
6. **Git 未提交变更**（静默执行，有变更才开口）：跑 `git diff --stat`（先 vault 目录，再代码区目录），有输出 → "📝 上次有 N 个文件改动未提交：<文件摘要>"
7. **依赖文件可达性**（静默执行，仅缺失时提醒）：验证 `~/.claude/skills/review-creator/SKILL.md`、`~/.claude/skills/dev-log-creator/SKILL.md` 存在。任一缺失 → "⚠️ 依赖文件缺失：<文件名>，部分功能可能不可用"

### Token 追踪

每次对话接近尾声（用户表示任务完成、准备关闭、或自然结束）时，AI 追加一条记录到：
`C:\Users\<你的用户名>\.claude\projects\e--6-CodeData\token-usage.jsonl`

格式：`YYYY-MM-DD HH:MM | <涉及项目名> | <估算token数> | <备注>`

估算方法：对话轮次 × 2000 tokens/轮（经验均值）。不追求精确，偏差不可怕，趋势才重要。

### 记忆备份提醒

AI 行为记忆存储在 `~/.claude/projects/` 下。定期提醒用户备份该目录（建议每月一次，备份到 Obsidian vault 或云盘）。

## 错误处理与恢复

### 统一规则（所有流水线适用）

**重试**：任何流水线步骤失败 → 等 2 秒 → 重试 1 次 → 仍失败 → fallback + 哨兵，当场告知用户原因和建议补救操作。

**超时**：单次操作超过 30 秒未完成 → 视为失败，走重试规则。

**哨兵格式**（写入 `~/.claude/failed-ops.jsonl`，一行一条 JSON）：
```json
{"date": "YYYY-MM-DDTHH:MM", "pipeline": "<标识>", "detail": "<断裂原因>", "recovery": "<建议恢复操作>"}
```

### 统一哨兵表

所有流水线统一处理：当场告知 + 写哨兵 + 下次启动提醒。

| 流水线 | pipeline 标识 | 断裂场景 | 告知 + 哨兵内容 |
|--------|-------------|---------|---------------|
| ① 新建项目（Obsidian 侧） | `new-project-init` | vault 不可访问，Obsidian 侧未初始化 | `"recovery": "确认 vault 可访问后说「补建 Obsidian 日志」"` |
| ② 代码变更→日志 | `dev-log` | vault 不可访问，日志写入失败 | `"recovery": "确认 vault 可访问后对<项目>说「更新日志」"` |
| ③ 历史记录 | `review` | review-creator 磁盘扫描等步骤出错 | `"recovery": "说「更新历史记录」重试"` |

### 静默降级
断裂时不直接失败，而是降到"本地缓存模式"，等条件恢复后自动迁移。

| 场景 | 降级行为 | 恢复触发 |
|------|---------|---------|
| vault 不可访问 | 内容写入 `~/.claude/fallback/`，文件名 = `<流水线标识>-<时间戳>.json` | 下次任意 vault 操作成功时提醒迁移 |
| Obsidian 侧操作失败 | 跳过该项目继续执行，失败项记入哨兵 | 哨兵处理时一起重试 |

降级文件格式：`{"pipeline": "degraded-<原pipeline标识>", "target": "<Obsidian vault 内目标路径>", "content": "<暂存内容>"}`。

### 恢复操作
用户说"恢复未完成操作"后，逐条读取 `~/.claude/failed-ops.jsonl`，按 `recovery` 字段建议执行，全部处理完后清空文件。

用户说"恢复暂存内容"后，逐文件读取 `~/.claude/fallback/`，按 `target` 路径写入 vault，全部完成后清空 fallback 目录。

## 记忆与知识系统（三层模型）

本工作区的记忆和知识分为三层，通过 `scope` frontmatter 字段标记：

| 层级 | scope | 说明 | 存放位置 |
|------|-------|------|---------|
| L1 全局规则 | `global` | 所有项目通用 | `memory/*.md`（MEMORY.md 的"全局规则"组） |
| L2 项目专属 | `project` | 仅对某一项目有效 | `memory/*.md`（MEMORY.md 的项目组），必填 `project` 字段 |
| L3 个人偏好 | `user` | 用户工作习惯 | `memory/*.md`（MEMORY.md 的"个人偏好"组） |

### 跨系统项目标识

- **VSCode 记忆**：`project: <序号-中文项目名-YYYYMMDD>`（frontmatter 字段）
- **Obsidian 日志**：`project: <同一值>`（frontmatter 字段）+ `项目/<短名>`（tag）
- **两端 project 值必须一致**，用于精确关联

### 新记忆创建规则

- 内容只涉及一个具体项目 → `scope: project`，必填 `project` 字段
- 内容适用于所有项目 → `scope: global`
- 用户的个人偏好/反馈 → `scope: user`
- 写入后更新 MEMORY.md 对应分组

## 关联系统

本工作区是个人知识管理系统的 VSCode 端。三端分工：

| 端点 | 角色 | CLAUDE.md |
|------|------|-----------|
| **VSCode** | 工程师（写代码） | 本文件 |
| **Obsidian** | 编辑（管笔记） | `<你的 Obsidian vault 路径>\CLAUDE.md` |
| **手机** | 秘书（快速存取） | 通过 cc-connect 路由到 skill |

- Obsidian vault 路径：`<你的 Obsidian vault 路径>\`
- 开发日志规范 → Obsidian 端 `_系统管理/设计规范/_全局规范/备忘-项目规范与开发日志流程.md`
- 系统设计全景 → Obsidian 端 `_系统管理/设计规范/核心设计规范/系统设计原理.md`
- **跨端共享规范（唯一权威）** → Obsidian 端 `_系统管理/设计规范/核心设计规范/跨端共享规范.md`（GitHub 发布、README 规范、触发词边界、project 字段等所有跨端规则）

> **路径权威来源**：本文件为代码区路径（`e:\6_CodeData\`、`~/.claude/` 等）的唯一权威。Obsidian vault 路径以 `<你的 Obsidian vault 路径>\CLAUDE.md` 为准。各自维护，互不覆盖，避免同一路径在两处重复定义导致不一致。
> ⚠️ 以上 Obsidian 端路径为缓存引用，方便日常查阅。唯一权威来源为 Obsidian 端 CLAUDE.md 的 §笔记库结构。启动时自动执行路径对齐校验（流程见跨端共享规范 §五），不一致自动修正。

## 环境信息

- **OS**：Windows 11 Home China
- **Python**：3.12.9
- **Git**：2.54.0
- **Shell**：bash（Git Bash）
- **编辑器**：VSCode + Obsidian
- **AI 模型**：DeepSeek V4 Pro（通过 Anthropic 兼容 API）
