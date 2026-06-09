# Claude VObS

> **零编程基础的人，搭建的个人 AI 操作系统。**
> 一套规则，把 Claude Code 变成能同时管笔记、写代码、手机速记的三端助理。纯 Markdown，零行手写代码。

[![Version](https://img.shields.io/badge/version-v4.3-blue.svg)](https://github.com/JohnYoo528B/claude-vobs/releases)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

---

## 🆕 v4.3 更新

- **7 个 Skill**：新增 archive（对话归档）、draft-cleanup（草稿箱清理）、system-watch（系统健康监控）
- **vault-search 重写**：双源并发搜索（vault 笔记 + 对话记忆）
- **开箱即用骨架**：四层文件夹自带 README，打开 vault 就知道每层放什么
- **自说明体系**：每个文件夹第一个文件解释「放什么、不放什么」
- **CLAUDE.md 瘦身**：420→376 行，为未来留出空间
- **跨端共享规范**：完整项目生命周期（新建→开发→完结→完结后维护）
- **0.5 发布节奏**：AI 不会再每小版本就催你发布——你说发就发

---

## 这是什么？

Claude VObS——**V** 和 **S** 取自 VSCode，**Ob** 取自 Obsidian——是一套个人 AI 操作系统。它不新建 App——只在 Claude Code、VSCode、Obsidian 之上叠加一层规则，让同一个 AI 在不同场景自动切换角色：

```
VSCode (工程师)          Obsidian (编辑)          手机 (秘书)
写代码 / 跑数据           管笔记 / 做归档          快速捕捉 / 搜索
     │                        │                      │
     └────────────────────────┼──────────────────────┘
                              │
              三层记忆（~/.claude/）
         global → project → user
                              │
              七个 Skill
  quick-capture │ vault-search │ dev-log-creator │ review-creator
         archive │ draft-cleanup │ system-watch
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

把仓库 `skills/` 下的所有 `.md` 文件复制到（7 个）：

| 系统 | 路径 |
|------|------|
| Windows | `C:\Users\<用户名>\.claude\skills\` |
| macOS / Linux | `~/.claude/skills/` |

没有 `.claude` 或 `skills` 文件夹就新建。Windows 用户：`.claude` 是隐藏文件夹，地址栏直接输入路径回车即可。

最终结构：
```
~/.claude/skills/
├─ quick-capture.md       ← 手机快速捕捉
├─ vault-search.md        ← 手机搜索笔记
├─ dev-log-creator.md     ← 自动生成开发日志
├─ review-creator.md      ← 系统回顾
├─ archive.md             ← 对话→笔记存档
├─ draft-cleanup.md       ← 草稿箱清理
└─ system-watch.md        ← 系统健康监控
```

---

### 第四步：替换占位符

全局搜索 `<你的`，逐项替换：

- **`<你的代码工作区路径>`** → 代码工作区完整路径，如 `D:\MyCode\claude-vobs`
- **`<你的 Obsidian vault 路径>`** → Obsidian vault 完整路径，如 `E:\Notes\MyVault`

VSCode 里 `Ctrl+Shift+H` 全局替换。

---

### 第五步：创建 Obsidian 文件夹结构

在 Obsidian vault 里建以下文件夹。仓库已自带各文件夹的 README.md（打开文件夹就能看到说明）：

```
你的 vault\
├─ CLAUDE.md                     ← 从仓库 CLAUDE_Obsidian.md 重命名
├─ _系统管理\                    ← 系统管理（设计规范、历史记录、健康监控）
├─ ～草稿箱\                     ← 统一入口（临时想法、外部资源、手机暂存）
│   ├─ README.md                 ← 已自带：解释什么丢进来
│   └─ 手机暂存\
├─ 1主题研究\                    ← 深读产出（想→写）
│   └─ README.md                 ← 已自带：判断标准和 MOC 用法
├─ 2日志与笔记\                  ← 过程记录（做→记）
│   └─ README.md                 ← 已自带：跟主题研究的区别
└─ 3工作与产出\                  ← 成品交付
    └─ README.md                 ← 已自带：什么时候放这里
```

> 四层架构的设计逻辑：从草稿箱（入口）→ 主题研究/日志（加工）→ 工作产出（交付）。每层一个 README，打开就知道放什么。

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
│   ├── quick-capture.md     ← 手机快速捕捉
│   ├── vault-search.md      ← 手机搜索笔记（双源并发）
│   ├── dev-log-creator.md   ← 自动生成开发日志
│   ├── review-creator.md    ← 系统回顾 + 版本历史
│   ├── archive.md           ← 对话→笔记存档
│   ├── draft-cleanup.md     ← 草稿箱清理
│   └── system-watch.md      ← 系统健康监控
├── DESIGN_zh.md             ← 系统设计原理
├── DESIGN.md                ← System design (English)
├── docs/
│   ├── phone-setup.md       ← 手机端设置指南
│   ├── phone-setup-en.md    ← Phone setup guide
│   └── 跨端共享规范.md       ← Obsidian↔VSCode 跨端规则
├── templates/
│   ├── 通用笔记模板.md       ← 新建笔记模板
│   └── MOC模板.md           ← 新建主题索引模板
├── ～草稿箱/                 ← 统一入口（自带 README）
│   └── README.md
├── 1主题研究/                ← 深读产出（自带 README + MOC 模板）
│   ├── README.md
│   └── MOC模板.md
├── 2日志与笔记/              ← 过程记录（自带 README）
│   └── README.md
└── 3工作与产出/              ← 成品交付（自带 README）
    └── README.md
```

---

## 常见问题

### 装完了能做什么？

- **写代码**：VSCode 里说"新建项目 我的第一个项目"
- **记笔记**：Obsidian 里说"整理今天的对话成笔记"
- **做回顾**：说"更新历史记录"
- **手机暂存**（配好手机端后）：手机上发"记一下 明天买牛奶"
- **清理草稿箱**：说"整理草稿箱"
- **搜索**：说"找一下 xxx"，同时搜索 vault 笔记和对话记忆
- **GitHub 发布**：说"发布 GitHub 新版本"

### "存一下"和"记忆一下"有什么区别？

"存" = 原文暂存到草稿箱（手机端快速捕捉），"记忆" = 写会话摘要到接力文件（下次对话自动引用）。两个字不交叉。

### 触发词一定要原样打吗？

不需要。说"帮我整理下对话""最近动了什么"这类自然表达，AI 按意图自动判断该走哪条流程。拿不准时会反问确认。

### 每个文件夹里的 README 是什么？

自说明体系——打开任意核心文件夹，第一个文件解释「这是什么、什么该放这里、什么不该」。四个 README（草稿箱 + 1主题研究 + 2日志与笔记 + 3工作与产出）形成完整的内容流引导。AI 和人都不需要读 CLAUDE.md 就能理解文件夹用途。

### 系统断了怎么办？

所有操作有统一错误处理：失败→等 2 秒→重试 1 次→写哨兵 + 告知。启动时 AI 自动检查哨兵和降级暂存。说"恢复未完成操作"重试失败项。Git 安全网在每次写操作前自动快照，可随时回滚。

### 项目完结怎么操作？

在 VSCode 端说"项目完结"。AI 自动跑验证→创建回顾总结→标记完结→更新记忆。完结后的小修（Bug 修复）直接改，AI 自动追加维护记录。详见 [docs/跨端共享规范.md](docs/跨端共享规范.md)。

### GitHub 发布怎么操作？

在 Obsidian 端说"发布 GitHub 新版本"。0.5 版本阈值是 AI 的提醒线（别催你），不是你的限制——你说发就发。

### 遇到问题？

1. 检查前置条件是否都满足
2. 确认 CLAUDE.md 放对位置、已重命名
3. 全局搜 `<你的` 确认占位符已全部替换
4. 确认 settings.json 中 API Key 正确
5. 还不行 → [GitHub Issues](https://github.com/JohnYoo528B/claude-vobs/issues)

---

## 核心设计

- **三端分工**：每端只做擅长的事——工程师（VSCode）、编辑（Obsidian）、秘书（手机）
- **三层内容架构**：草稿箱（入口）→ 主题研究/日志（加工）→ 工作产出（交付），按内容成熟度分层
- **自说明体系**：每个文件夹自带 README，结构本身就是文档
- **三层记忆**：global → project → user，项目完结自动清理
- **纯 Markdown + 命名约定**：无数据库、无 API，`project` 字段 + 同名文件夹对齐
- **七个 Skill**：可插拔执行管道，自然语言触发。CLAUDE.md 路由，SKILL.md 执行
- **版本化演进**：大版本（架构重组）+1，功能新增 +0.1，旧版原文不动 + 末尾对照表
- **容错四层**：吸收（重试）→ 适应（降级）→ 恢复（哨兵+Git回滚）→ 学习（启动自检）

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
