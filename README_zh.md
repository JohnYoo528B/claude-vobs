# Claude VObS

> **零编程基础的人，搭建的个人 AI 操作系统。**
> AI 知识管理工具的自托管替代方案 — 基于 Claude Code + Obsidian，零行手写代码。

[![Version](https://img.shields.io/badge/version-v3.5-blue.svg)](https://github.com/JohnYoo528B/claude-vobs/releases)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

---

## 这是什么？

Claude VObS —— **V** 取自 VSCode，**Ob** 取自 Obsidian，**S** 是两端 s 的合并 —— 是一个个人 AI 操作系统：一套规则、工作流和约定，把 Claude Code 变成你的三端个人助理：

```
┌──────────────────────────────────────────────────┐
│                 三 端 分 工                        │
│                                                   │
│  VSCode (工程师)    Obsidian (编辑)    手机 (秘书)  │
│  写代码 / 调试      管笔记 / 归档      快速存取     │
│         │                │                │       │
│         └────────────────┼────────────────┘       │
│                          │                        │
│              三 层 记 忆（~/.claude/）              │
│         global ──→ project ──→ user              │
│         (全项目通用)  (单项目专属)  (个人偏好)       │
│                          │                        │
│              四 个 Skill                           │
│  quick-capture │ vault-search │ dev-log-creator   │
│                         │ review-creator          │
└──────────────────────────────────────────────────┘
```

每端通过各自的 `CLAUDE.md` 拥有不同的人格。它们共享三层记忆系统（全局规则 → 项目规则 → 个人偏好）和四个 Skill——用自然语言触发自动化工作流。没有 API 集成，没有数据库——一切靠纯 Markdown 文件 + 命名约定来同步。

**搭建这个系统的人不会写代码。** 全程 Vibe Coding——用大白话描述需求，AI 负责实现。

---

## 快速开始

1. **复制 CLAUDE.md** — 把 [CLAUDE_Obsidian.md](CLAUDE_Obsidian.md) 放到你的 Obsidian vault 根目录（重命名为 `CLAUDE.md`），[CLAUDE_VSCode.md](CLAUDE_VSCode.md) 放到你的代码工作区根目录（重命名为 `CLAUDE.md`）。
2. **安装四个 Skill** — 把 [skills/](skills/) 文件夹复制到 `~/.claude/skills/`。
3. **替换占位符** — 在所有文件中搜索 `<你的`，替换为你的实际路径。
4. **开始说话** — 在 VSCode Claude Code 里说"新建项目 我的第一个项目"，或在手机上发"记一下 今天想到的点子"。

> **手机端**：任何能跟 Claude Code 对话的渠道（Claude 手机 App、网页版、或第三方桥接）都是第三端。`quick-capture` 和 `vault-search` 专为手机场景设计。

---

## 仓库结构

```
.
├── README.md                  ← 英文版说明
├── README_zh.md               ← 你在这里（中文版）
├── LICENSE                    ← MIT 协议
├── .gitignore
├── CLAUDE_Obsidian.md         ← Obsidian 端规则（已脱敏）
├── CLAUDE_VSCode.md           ← VSCode 端规则（已脱敏）
├── skills/
│   ├── quick-capture.md       ← 手机端快速捕捉想法
│   ├── vault-search.md        ← 手机端搜索笔记
│   ├── dev-log-creator.md     ← 自动生成项目开发日志
│   └── review-creator.md      ← 生成系统回顾报告
├── DESIGN.md                   ← 系统设计原则（英文）
├── DESIGN_zh.md                ← 系统设计原则（中文）
└── 模板/                       ← 模板
    └── 通用笔记模板.md           ← 通用笔记模板
```

---

## 核心设计决策

| 决策 | 为什么这样设计 |
|------|---------------|
| **三端分工，不是一个 App** | 每端只做自己擅长的事。不做大而全的界面。 |
| **三层记忆** | 全局规则 → 项目规则 → 个人偏好。项目级记忆随项目完结自动清理。 |
| **纯 Markdown + 命名约定** | 不用数据库，不用 API。`project` frontmatter 字段 + 同名文件夹 = 完美跨端对齐。 |
| **四个 Skill，不是四十个** | 每个 Skill 是一个自包含的管道，用自然语言触发。CLAUDE.md 是路由表，SKILL.md 是执行手册。 |
| **版本化演进** | 每次架构变更都有记录。旧版本原文不动，末尾加对照表建立新旧概念链接。 |

更多内容见 [DESIGN_zh.md](DESIGN_zh.md)。

---

## 适合谁？

- **零编程基础的个人** — 想要一个 AI 驱动的个人知识管理系统
- **AI 玩家** — 寻找多端 AI 配置的参考架构
- **系统设计爱好者** — 好奇一个导演设计的软件架构长什么样

---

## 灵感来源

这个系统的起点很简单：每次打开新的 AI 对话，它对你一无所知。你得重新解释你是谁、在做什么、喜欢什么风格。

解决方案不是做一个新 App——而是在现有工具（Claude Code、Obsidian、VSCode）上叠加一套约定。关键在于：同一个 AI 可以在不同场景扮演不同角色，只要每个场景有清晰的规则。

最终浮现出来的是一个个人 AI 操作系统——不是工程师设计的，而是一个只想让 AI 记住自己的人设计的。

---

## 许可证

MIT — 详见 [LICENSE](LICENSE)。

---

*由 [@JohnYoo528B](https://github.com/JohnYoo528B) 搭建 —— 零编程基础的前导演，只用自然语言和 Claude Code。*

*基于 Claude Code + Obsidian 搭建，零行手写代码。*
