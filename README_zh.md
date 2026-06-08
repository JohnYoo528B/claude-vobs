# Claude VObS

> **零编程基础的人，搭建的个人 AI 操作系统。**
> 一套规则，把 Claude Code 变成能同时管笔记、写代码、手机速记的三端助理。纯 Markdown，零行手写代码。

[![Version](https://img.shields.io/badge/version-v3.5-blue.svg)](https://github.com/JohnYoo528B/claude-vobs/releases)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

---

## 这是什么？

Claude VObS（**V**SCode + **Ob**sidian + **S**ystem）是一套个人 AI 操作系统。它不新建 App——只在 Claude Code、VSCode、Obsidian 之上叠加一层规则，让同一个 AI 在不同场景自动切换角色：

```
VSCode (工程师)          Obsidian (编辑)          手机 (秘书)
写代码 / 跑数据           管笔记 / 做归档          快速捕捉 / 搜索
     │                        │                      │
     └────────────────────────┼──────────────────────┘
                              │
              三层记忆（~/.claude/）
         global → project → user
                              │
              四个 Skill
  quick-capture │ vault-search │ dev-log-creator │ review-creator
```

### 能做什么？

这不是"笔记工具 + 代码工具拼在一起"。是**同一套系统、同一套记忆，在不同场景自动切换角色**：

```
文字笔记 ───────────→ 代码分析 ───────────→ vibe-coding
知识管理              数据处理               从零搭项目
日记 · 草稿           量化分析               游戏 · 工具
读书笔记              自动化脚本             Web 应用
```

从"记一下今天想到的点子"到"帮我写一个量化交易回测系统"——同一套记忆、同一个 AI。

### 为什么不用现成的？

<table>
<tr><th>方案</th><th>管笔记</th><th>写代码</th><th>持久记忆</th><th>自托管</th></tr>
<tr><td>Obsidian 本体</td><td>✅</td><td>❌</td><td>❌</td><td>✅</td></tr>
<tr><td>Obsidian + Copilot 插件</td><td>✅</td><td>❌</td><td>❌</td><td>✅</td></tr>
<tr><td>Claude Code 单用</td><td>❌</td><td>✅</td><td>❌</td><td>✅</td></tr>
<tr><td>Notion AI / Mem</td><td>✅</td><td>❌</td><td>⚠️ 平台内</td><td>❌ 云端</td></tr>
<tr><td><b>Claude VObS</b></td><td>✅</td><td>✅</td><td>✅ 三层</td><td>✅ 本地</td></tr>
</table>

核心区别：别的方案要么管笔记、要么写代码。VObS 两端都做，且 AI 记得住你是谁、在做什么、偏好什么风格——因为三层记忆跨会话、跨项目、跨端共享。

**搭建这个系统的人不会写代码。** 全程 vibe coding——用大白话描述需求，AI 负责实现。

---

## 前置条件

- **Claude Code**　✅ 必须 — 系统运行时。需 Anthropic 账号 + API Key（付费）或 Claude 订阅。→ [官方文档](https://docs.anthropic.com/en/docs/claude-code)
- **VSCode**　✅ 必须 — 代码端。→ [下载](https://code.visualstudio.com/)
- **Obsidian**　✅ 必须 — 笔记端。→ [下载](https://obsidian.md/)
- **Python 3.12**　✅ 必须 — vibe-coding 默认语言，跑项目代码必需。→ [下载](https://www.python.org/downloads/)
- **Git**　🟡 推荐 — 版本控制。Windows 用户装 Git for Windows（自带 Git Bash）。→ [下载](https://git-scm.com/downloads)

> 🧪 **自检**：VSCode 里打开 Claude Code 终端，说句话。AI 有回复 = 核心链路通。

---

## 快速开始

两条路，选一条：

| | 🅰️ 自动安装 | 🅱️ 手动安装 |
|---|---|---|
| 怎么做 | 对 Claude Code 说一句话 | 跟着 7 步走 |
| 耗时 | ~2 分钟 | 15–30 分钟 |
| 适合 | 不想折腾 | 想搞清楚每一步在干嘛 |

### 🅰️ 自动安装

1. 下载仓库（同上）
2. 在 VSCode 里打开这个文件夹
3. 打开 Claude Code 终端（`Ctrl+Shift+P` → "Claude Code: Open"）
4. 说：

> **"初始化 Claude VObS。我的 Obsidian vault 在 `[填你的 vault 路径]`。请自动完成全部部署：把两个 CLAUDE.md 分别放到代码区和 vault 根目录，把 skills 复制到 ~/.claude/skills/，全局替换所有占位符，在 vault 里建好文件夹结构，复制 settings.example.json 为 settings.json。每步完成后告诉我做了什么。"**

Claude Code 会自己读文件、理解上下文、处理路径差异。遇到问题会解释原因，不会静默失败。

---

### 🅱️ 手动安装

预计 15–30 分钟。

### 第一步：下载仓库

```bash
git clone https://github.com/JohnYoo528B/claude-vobs.git
```
或者：GitHub 页面点 "Code" → Download ZIP → 解压。

解压出来的文件夹就是你的**代码工作区**。

---

### 第二步：部署 CLAUDE.md

两个文件分别重命名并放到对应位置：

| 源文件 | 重命名为 | 放到 |
|--------|---------|------|
| 仓库中的 `CLAUDE_VSCode.md` | `CLAUDE.md` | 代码工作区根目录 |
| 仓库中的 `CLAUDE_Obsidian.md` | `CLAUDE.md` | Obsidian vault 根目录 |

> Obsidian vault 根目录 = 你放所有笔记的那个文件夹。还没建？打开 Obsidian → 创建新 vault。

---

### 第三步：安装 Skill

把仓库 `skills/` 下的 `.md` 文件复制到（3 个核心 + 1 个进阶）：

| 系统 | 路径 |
|------|------|
| Windows | `C:\Users\<用户名>\.claude\skills\` |
| macOS / Linux | `~/.claude/skills/` |

没有 `.claude` 或 `skills` 文件夹就新建。Windows 用户：`.claude` 是隐藏文件夹，地址栏直接输入路径回车即可。

最终结构：
```
~/.claude/skills/
├─ quick-capture.md       ← 核心：手机快速捕捉
├─ vault-search.md        ← 核心：手机搜索笔记
├─ dev-log-creator.md     ← 核心：自动生成开发日志
└─ review-creator.md      ← 进阶：系统回顾（初期可跳过，用到再装）
```

---

### 第四步：替换占位符

全局搜索 `<你的`，逐项替换：

- **`<你的代码工作区路径>`** → 代码工作区完整路径，如 `D:\MyCode\claude-vobs`
- **`<你的 Obsidian vault 路径>`** → Obsidian vault 完整路径，如 `E:\Notes\MyVault`

VSCode 里 `Ctrl+Shift+H` 全局替换。

---

### 第五步：创建 Obsidian 文件夹结构

在 Obsidian vault 里建以下文件夹（至少建前三个）：

```
你的 vault\
├─ _系统管理\
├─ 00草稿箱\
│   └─ 手机暂存\          ← 手机端用，可后建
├─ 0VibeCoding开发日志\
│   └─ _全局规范\
├─ 1学习笔记\
├─ 2工作文档\
├─ 3投资记录\
├─ 4创作灵感\
├─ 5其他知识\
└─ 模板\
```

---

### 第六步：配置 settings.json

1. 复制仓库的 `settings.example.json` 到 `C:\Users\<用户名>\.claude\`（Mac：`~/.claude/`）
2. 重命名为 `settings.json`
3. 替换 API Key：

```json
{
  "env": {
    "ANTHROPIC_API_KEY": "sk-ant-api03-你的Key"
  }
}
```

> API Key 在 [console.anthropic.com](https://console.anthropic.com/) 创建。Claude 订阅用户可跳过，直接用订阅额度。

---

### 第七步：验证

VSCode → File → Open Folder（选代码工作区）→ `Ctrl+Shift+P` → "Claude Code: Open" → 说：

**"新建项目 测试项目"**

AI 开始建文件夹 = 系统跑通。

---

## 手机端（可选）

手机端能做两件事：快速捕捉想法到 Obsidian 草稿箱、搜索 vault 里的笔记。

系统已内置手机端逻辑——`quick-capture` 和 `vault-search` 两个 Skill + 触发词路由。你只需要把手机消息桥接到 Claude Code。

<table>
<tr><th>方案</th><th>需要</th><th>适合</th></tr>
<tr><td>🅰️ <b>cc-connect + 微信</b></td><td>Node.js、电脑常开、微信 ClawBot</td><td>功能最全，微信里像聊天一样用</td></tr>
<tr><td>🅱️ <b>Claude 手机 App</b></td><td>Claude 订阅（Pro/Max）</td><td>最简单，无需额外配置</td></tr>
<tr><td>🅲 <b>先跳过</b></td><td>无</td><td>不影响 VSCode + Obsidian 核心功能</td></tr>
</table>

详细步骤 → [docs/phone-setup.md](docs/phone-setup.md)

---

## 仓库结构

```
.
├── README_zh.md             ← 你在这里
├── README.md                ← English version
├── LICENSE                  ← MIT
├── settings.example.json    ← settings.json 模板
├── CLAUDE_VSCode.md         ← VSCode 端规则
├── CLAUDE_Obsidian.md       ← Obsidian 端规则
├── skills/
│   ├── quick-capture.md     ← 核心 Skill：手机快速捕捉
│   ├── vault-search.md      ← 核心 Skill：手机搜索笔记
│   ├── dev-log-creator.md   ← 核心 Skill：自动生成开发日志
│   └── review-creator.md    ← 进阶 Skill：系统回顾
├── DESIGN_zh.md             ← 系统设计原则
├── DESIGN.md                ← System design (English)
├── docs/
│   ├── phone-setup.md       ← 手机端设置指南
│   └── phone-setup-en.md    ← Phone setup guide
└── 模板/
    └── 通用笔记模板.md
```

---

## 常见问题

### `~/.claude/` 在哪？

| Windows | Mac | Linux |
|---------|-----|-------|
| `C:\Users\<用户名>\.claude\` | `/Users/<用户名>/.claude/` | `/home/<用户名>/.claude/` |

不确定用户名？文件管理器地址栏输入 `%USERPROFILE%` 回车。

### API Key 怎么获取？

[console.anthropic.com](https://console.anthropic.com/) 注册 → 充值（最低 $5）→ API Keys → 创建。Claude 订阅用户不需要单独配 Key。

### 不写代码，Python 和 Git 还需要吗？

都需要。Python 是系统默认编程语言，即使你暂时不写代码，后续 vibe-coding 项目依赖它。Git 是版本管理基础，VSCode 集成依赖它。

### 装完了能做什么？

- **写代码**：VSCode 里说"新建项目 我的第一个项目"
- **记笔记**：Obsidian 里说"整理今天的对话成笔记"
- **做回顾**：说"更新历史记录"
- **手机暂存**（配好手机端后）：手机上发"记一下 明天买牛奶"

### 遇到问题？

1. 检查前置条件是否都满足
2. 确认 CLAUDE.md 放对位置、已重命名
3. 全局搜 `<你的` 确认占位符已全部替换
4. 确认 settings.json 中 API Key 正确
5. 还不行 → [GitHub Issues](https://github.com/JohnYoo528B/claude-vobs/issues)

---

## 核心设计

- **三端分工**：每端只做擅长的事，不做大而全
- **三层记忆**：global → project → user，项目完结自动清理
- **纯 Markdown + 命名约定**：无数据库、无 API，`project` 字段 + 同名文件夹对齐
- **四个 Skill**：自包含管道，自然语言触发。CLAUDE.md 路由，SKILL.md 执行
- **版本化演进**：旧版本不动，末尾对照表链接新旧概念

详见 [DESIGN_zh.md](DESIGN_zh.md)。

---

## 适合谁？

- **零编程基础** — 想要 AI 驱动的个人知识+代码管理系统
- **AI 玩家** — 找多端 AI 配置参考架构
- **系统设计爱好者** — 好奇非工程师设计的系统架构

---

## 灵感

每次打开新 AI 对话，它对你一无所知。解决方案不是做新 App——而是在 Claude Code、Obsidian、VSCode 上叠加一套约定。同一个 AI，不同场景，不同角色，同一套记忆。

---

## 许可证

MIT — [LICENSE](LICENSE)

---

*由 [@JohnYoo528B](https://github.com/JohnYoo528B) 搭建 —— 前导演，零编程基础，只用自然语言和 Claude Code。*
