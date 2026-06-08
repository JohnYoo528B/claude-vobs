---
name: dev-log-creator
description: Records code-level changes (new features, bug fixes, concepts learned) for a specific vibe-coding project.
---

# 开发日志创建器

触发词路由见 CLAUDE.md → "两个 skill 的分工" 表（权威来源）。本文件为格式与流程的唯一执行规范。

## 适用场景

记录项目代码本身的变更（新功能、修 Bug、学到的新概念）。vault 结构/流程变更请用 review-creator。

---

## 第〇步：project 字段校验（写入前必做）

每次写入日志前，先校验 frontmatter 的 `project` 字段与所在文件夹名一致：

1. 读取目标日志文件的 frontmatter，检查 `project` 字段
2. 将 `project` 值与日志文件所在的文件夹名比对：
   - **一致** → 继续写入
   - **不一致** → 以文件夹名为准自动修正，写入后告知用户"project 字段已从 `xxx` 修正为 `yyy`"
   - **缺失** → 自动补上，值 = 文件夹名
3. 校验通过后再继续下面的步骤

> **原则：文件夹名为准。** 文件夹是物理位置，用户主动决定；project 是跟跑的标签，写偏了就是 AI 的错。

---

## 第一步：先判断变更类型

收到"更新日志"后，先确定变更类型，选对应模板：

| 变更类型 | Callout | 触发场景 |
|----------|---------|---------|
| 🐛 修 Bug | `[!bug]` | 改错、修缺陷 |
| ✨ 新功能 | `[!important]` | 加功能、新需求 |
| 🔧 优化 | `[!abstract]` | 性能、token、格式改进 |
| 📌 里程碑 | `[!info]` | 项目启动、阶段节点 |
| ✅ 完结 | `[!success]` | 项目完结，必须记录用户发出完结指令的精确时间（到分钟） |

---

## 第二步：按模板写日志

### 日志格式

```markdown
## YYYY-MM-DD HH:MM

> [!type] 一句话标题

### 新增功能 / 遇到的问题 & 解决 / 优化内容
（按变更类型选对应小节标题）

- **问题**：xxx（表象）
  - **原因**：xxx（根因）
  - **解决**：xxx（怎么修）

### 代码变更
- 修改文件：`文件名`
  - 具体改动点

### 学到的新概念
- **术语**：一句话解释（可选）

### 验证结果 / 下一步计划
（可选）
```

### 三种模板示例

**修 Bug：**
```markdown
## 2026-06-07 14:30

> [!bug] 人类对战能看到 AI 手牌

### 遇到的问题 & 解决
- **问题**：控制台打印 AI 决策全过程
  - **原因**：_ai_decision() 调试日志未区分模式
  - **解决**：人类对手时重定向 stdout 全部吞掉

### 代码变更
- 修改文件：`texas_holdem_v8.py`
  - `_ai_decision()` — 新增 stdout 重定向逻辑
```

**新功能：**
```markdown
## 2026-06-06 03:00

> [!important] 新增：AI 对局叙述系统

### 新增功能
- 每局结束 AI 自动生成中文解说

### 实现方式
- 事件录制 → prompt 组装 → API 调用 → 输出叙述

### 代码变更
- 修改文件：`texas_holdem_v8.py`（+370 行）
  - 新增 `_record()`、`_generate_match_narrative()`、`_call_ai_api()` 等方法
```

**里程碑 / 完结：**
```markdown
## YYYY-MM-DD HH:MM

> [!success] ✅ 项目完结

### 完结确认
- **完结指令发出时间**：YYYY-MM-DD HH:MM（精确到分钟）
- **最终状态**：xxx
```

> 完结时 **必须** 记录用户发出完结指令的精确时间，用 `date "+%Y-%m-%d %H:%M"` 获取。

### 写入位置

```
<你的 Obsidian vault 路径>/0VibeCoding开发日志/<项目名>/<与源码同名>.md
```

### 写入规则

- **每个源码文件对应一个独立 .md，互不包含**
- 新条目追加到文件末尾，不覆盖旧内容
- 每次写入后更新 frontmatter 的 `updated` 日期
- **三段式必须完整**：问题（表象）→ 原因（根因）→ 解决（怎么修）
- 用 `date "+%Y-%m-%d %H:%M"` 获取精确时间戳

### 项目日志 frontmatter

```yaml
---
tags:
  - vibe-coding
  - 项目/<项目名>
  - 类型/开发日志
created: YYYY-MM-DD
updated: YYYY-MM-DD
project: <序号-项目名-日期>  # 与代码端文件夹名一致
---
```

---

## 三件事记住

1. **先判断变更类型再选模板** — bug `[!bug]`，新功能 `[!important]`，优化 `[!abstract]`，完结 `[!success]`
2. **区分代码变更和架构变更** — 代码变更用本 skill，架构/流程变更引导用户说"更新历史记录"
3. **三段式根因分析** — 问题（表象）、原因（根因）、解决（改动），缺一不可
4. **完结必须精确到分钟** — 用 `date "+%Y-%m-%d %H:%M"` 记录用户发出完结指令的时间
