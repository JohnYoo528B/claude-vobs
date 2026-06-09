# Claude VObS

> **零编程基础的人，搭建的个人 AI 操作系统。**
> 一套规则，把 Claude Code 变成能同时管笔记、写代码、手机速记的三端助理。纯 Markdown，零行手写代码。

[![Version](https://img.shields.io/badge/version-v4.5-blue.svg)](https://github.com/JohnYoo528B/claude-vobs/releases)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

---

## 🆕 v4.5 更新（v4.4 + v4.5 累积）

- **8 个 Skill**：新增 review-dispatcher（回顾统一入口），四个回顾入口合并为一个引擎
- **system-watch 定时巡检**：A 类启动时检测 + B 类 cron 每 2 天自动巡检
- **三层记忆标准化**：27 个 memory 文件 frontmatter 全覆盖，L1/L2/L3 三层分组索引
- **Token 估算升级 + 模型路由**：输入轮数×2000 + 输出字节×1.5，三档任务分级（LIGHT/STANDARD/HEAVY）
- **下游传播表**：22 条"改 X → 更新 Y"规则收拢到一张表
- **量化判断标准**：致命条件 + 高权重条件 + 量级判定，替换 AI 主观判断
- **反馈闭环关闭**：v4.4 评审报告揭示的四项开放项全部从"已识别"推进到"已实现"

---

## 我为什么做这个

我是一名前导演，零编程基础——不会写任何代码，连 Git 都是 AI 教的。

我的日常在三端之间切换：**Obsidian** 管笔记和知识，**VSCode** 让 AI 帮我写代码做量化分析，**手机**随时捕捉想法。每端的 AI 对话都像第一次见面——不知道我是谁、在做什么、习惯什么。

所有现成方案都有一个问题：**它们做的是单点优化，不是系统整合**。笔记工具只管笔记，代码助手只管代码。没有一个是"同一套记忆、同一个 AI，在不同场景自动切换角色"。

我不是在找一个新 App。我需要的是给已有的工具叠加一套规则——让 AI 在三端拥有不同的角色、共享同一套记忆、按自然语言意图自动路由到正确的执行管道。

于是有了 Claude VObS。全程 vibe coding——用大白话描述想要什么，AI 负责实现。从 v1.0 到 v4.5，每一次演进都在解决一个真实痛点：第一次是"AI 记不住我"，第二次是"手机存的跟 Obisidian 不连通"，第三次是"收了工不知道到底改了哪些要同步"。

---

## 我每天怎么用

这套系统不是为了好看——是每天真的在用。以下是一个典型的使用节奏：

### ☀️ 早上
打开 Obsidian → AI 自动跑健康检查（8 项）→ 告诉我有什么要注意。有未读资源？提醒我处理。有草稿积压？提醒我清理。

### 📱 白天
- **手机存一下**：路上想到点子 → 微信发 `存一下 明天买 ETH 的理由有 3 个` → 自动写入 Obsidian 草稿箱
- **VSCode 写代码**：说 `新建项目 德州扑克策略回测` → AI 建项目骨架、写开发日志，Obsidian 端同步出现日志文件
- **Obsidian 搜索**：说 `找我之前写的供应链瓶颈分析` → AI 同时搜 vault 笔记和对话记忆，两端结果一起呈现

### 🌙 晚上
- **整理草稿箱**：说 `整理草稿箱` → AI 逐条分析、判断归类，我确认后执行
- **更新历史记录**：说 `更新历史记录` → AI 自动扫描今天所有改动，生成版本更新记录
- **记忆一下**：说 `记忆一下` → AI 把今天做了什么存到接力文件，下次启动自动读

### 📅 每周
**收工**：说 `收工` → AI 用量化标准判定改动量级，选择对应路径，更新历史记录 + 使用指南 + GitHub 发布（该发的发）

每个场景只用一句话触发。不用记命令——说自然的汉语就行。

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
              八个 Skill
  quick-capture │ vault-search │ dev-log-creator
  review-dispatcher → review-creator
         archive │ draft-cleanup │ system-watch
```

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

## Obsidian Vault 架构全景

这是整个系统最核心的部分——不是"怎么安装"，而是"背后在想什么"。安装只要 2 分钟，理解这些设计思路才能知道它为什么这样工作。

### 1. 三层内容架构

```
～草稿箱/  ────→  1主题研究/  ────→  3工作与产出/
（入口）        2日志与笔记/       （交付）
                  （加工）
```

vault 里的内容按**成熟度**分层，不是按文件类型分。

| 层 | 文件夹 | 放了什么 | 判断标准 |
|----|--------|---------|---------|
| 入口层 | `～草稿箱/` | 临时想法、外部链接、手机暂存 | 还没消化 |
| 加工层 | `1主题研究/` | 深读后的判断、方法论、框架 | 「想→写」的产物 |
| 加工层 | `2日志与笔记/` | 交易记录、开发日志、每日笔记 | 「做→记」的产物 |
| 交付层 | `3工作与产出/` | 成品、可对外交付的内容 | 做完了 |

**流向是单向的**：一条信息从草稿箱出发 → 经过消化加工 → 最终变成自己的判断（主题研究）或成品（工作产出）。外部资料（论文、GitHub 项目、他人文章）统一从草稿箱进入 vault。AI 不会自动归档外部资料——跨越边界需要人的消化。

### 2. MOC 机制

```
物理存放 ≠ 浏览入口
```

传统笔记工具的问题：笔记分类放文件夹 → 一篇笔记只能属于一个文件夹 → 跨领域的内容找不到了。

VObS 的解决方案：**文件夹管放，MOC 管找**。

| 概念 | 作用 | 例子 |
|------|------|------|
| 文件夹 | 物理存放位置 | `金融/策略复盘/` |
| MOC（主题索引） | 浏览入口 | `金融.md`，里面 Wiki-link 到这个文件夹下的笔记 |

一篇关于"量化策略"的笔记，物理上放在 `金融/`，但同时链到 `金融.md` 和 `科技与AI.md` 两个 MOC。跨领域笔记不会被单一分类吃掉。

MOC 文件是人+AI 共同维护的。AI 写入笔记时自动判断是否需要加入现有 MOC，拿不准时问用户。新增主题方向积累到 3 篇以上时，新建 MOC。

### 3. Skill 路由系统

八个 Skill = 八个独立的执行管道。CLAUDE.md 做路由，SKILL.md 做执行。

```
用户说自然语言
      │
      ▼
  CLAUDE.md 意图路由表（14 条路线）
      │
      ▼
  Skill 执行管道（每个 Skill 一个独立文件，~/.claude/skills/）
      │
      ▼
  结果写回 vault 或反馈给用户
```

| Skill | 你说了什么 | 做了什么 |
|-------|-----------|---------|
| `quick-capture` | "存一下 明天买牛奶" | 原文照存到草稿箱 |
| `vault-search` | "找我之前写的供应链分析" | vault + 对话记忆并发搜索 |
| `dev-log-creator` | "德州扑克项目写日志" | 自动生成开发日志到 Obsidian |
| `review-dispatcher` | "收工" / "更新历史记录" | 判定回顾路径 → 调用 review-creator |
| `review-creator` | （由 dispatcher 调用） | 扫描改动 → 生成历史记录 → 更新使用指南 |
| `archive` | "把对话整理成笔记" | 对话 → 结构化笔记存入 vault |
| `draft-cleanup` | "整理草稿箱" | 逐条分析草稿 → 归类/合并/删除 |
| `system-watch` | （启动时自动跑） | 8 项健康检查 + B 类 cron 定时巡检 |

**不需要精确匹配触发词**。说"帮我整理下对话""最近动了什么"这类自然表达，AI 按意图自动判断该走哪条流程。

### 4. 跨端协作

三端（VSCode、Obsidian、手机）共享同一套记忆和规则。

```
VSCode 端 CLAUDE.md ──── 定义"工程师"行为
Obsidian 端 CLAUDE.md ── 定义"编辑"行为
共享 ~/.claude/ ───────── 三层记忆 + 八个 Skill
```

关键机制——**`project` 字段**：

```yaml
project: 01-德州扑克-20260606
```

一行 YAML，同时解决四个问题：
1. **跨端关联**：VSCode 项目和 Obsidian 日志通过同名文件夹对齐，无需 API
2. **日志自动归位**：dev-log-creator 不会写错文件夹
3. **记忆可清理**：项目完结后 project 级 memory 可自动清理
4. **跨会话可追溯**：任何时候知道某日志属于哪个项目

### 5. 三层记忆

```
L1 · Global（全局通用）   → 所有项目通用的系统规则
L2 · Project（项目专属）  → 单项目上下文，按需加载，完结后自动清理
L3 · User（个人偏好）     → 回答风格、语气、沟通惯例
```

记忆不是"所有内容堆一起"。不同生命周期的内容分层存放：
- L1 常驻上下文（系统规则永远不会删）
- L2 项目完结自动提醒清理（不用的项目记忆不占 token）
- L3 个人偏好独立于项目和系统（换项目不影响回答风格）

### 6. 容错机制

```
操作前 Git 快照 → 执行 → 失败？
                          ├─→ 等 2 秒 → 重试 1 次 → 成功 ✅
                          └─→ 失败 → 写哨兵 + 降级到本地 →
                                 下次启动自动提醒"有 N 个未完成操作"
```

四层韧性：

| 层 | 做了什么 | 例子 |
|----|---------|------|
| **吸收** | 每个操作失败自动重试 | 写入失败 → 等 2s → 再试一次 |
| **适应** | vault 不可访问时自动降级 | 内容暂存到本地 fallback 目录，等恢复后迁移 |
| **恢复** | 失败有记录有回滚 | 哨兵文件记下失败原因和恢复指令，Git 快照可随时回滚 |
| **学习** | 启动时自动检查 | 每次启动跑 8 项自检（哨兵/降级/待办/健康/Git/依赖/记忆冲突/路径对齐） |

所有 7 条操作流水线（历史记录、手机捕捉、会话接力、开发日志、对话归档、GitHub 发布）统一走这套容错规则。不需要每条流水线单独处理错误。

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

把仓库 `skills/` 下的所有 `.md` 文件复制到（8 个）：

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
├─ review-dispatcher.md   ← 回顾统一入口（分流器）
├─ review-creator.md      ← 系统回顾执行引擎
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
│   ├── review-dispatcher.md ← 回顾统一入口（分流器）
│   ├── review-creator.md    ← 系统回顾执行引擎
│   ├── archive.md           ← 对话→笔记存档
│   ├── draft-cleanup.md     ← 草稿箱清理
│   └── system-watch.md      ← 系统健康监控
├── DESIGN_zh.md             ← 系统设计原理
├── DESIGN.md                ← System design (English)
├── docs/
│   ├── phone-setup.md       ← 手机端设置指南
│   ├── phone-setup-en.md    ← Phone setup guide
│   ├── 跨端共享规范.md       ← Obsidian↔VSCode 跨端规则
│   ├── 下游传播表.md         ← 改 X → 更新 Y 规则
│   ├── 量化判断标准.md       ← 收工判定引擎
│   ├── memory-frontmatter-标准.md ← Memory 字段规范
│   └── 模型路由规则.md       ← 三档任务分级
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

### 系统断了怎么办？

所有操作有统一错误处理：失败→等 2 秒→重试 1 次→写哨兵 + 告知。启动时 AI 自动检查哨兵和降级暂存。说"恢复未完成操作"重试失败项。Git 安全网在每次写操作前自动快照，可随时回滚。

### 项目完结怎么操作？

在 VSCode 端说"项目完结"。AI 自动跑验证→创建回顾总结→标记完结→更新记忆。完结后的小修（Bug 修复）直接改，AI 自动追加维护记录。详见 [docs/跨端共享规范.md](docs/跨端共享规范.md)。

### 如何发布到 GitHub？

在 Obsidian 端说"发布 GitHub 新版本"。0.5 版本阈值是 AI 的提醒线（别催你），不是你的限制——你说发就发。

### 遇到问题？

1. 检查前置条件是否都满足
2. 确认 CLAUDE.md 放对位置、已重命名
3. 全局搜 `<你的` 确认占位符已全部替换
4. 确认 settings.json 中 API Key 正确
5. 还不行 → [GitHub Issues](https://github.com/JohnYoo528B/claude-vobs/issues)

---

## 核心设计

- **三端分工**：工程师（VSCode）· 编辑（Obsidian）· 秘书（手机），每端只做擅长的事
- **三层内容架构**：按内容成熟度分层，草稿箱（入口）→ 加工层 → 交付层，单向流动
- **MOC 机制**：物理存放 ≠ 浏览入口，文件夹管放 MOC 管找
- **Skill 路由**：CLAUDE.md 做路由，SKILL.md 做执行，自然语言意图匹配
- **三层记忆**：L1 global → L2 project → L3 user，按生命周期分层，项目完结自动清理
- **跨端对齐**：`project` 字段一句解决跨端关联 + 日志归位 + 记忆清理 + 可追溯
- **容错四层**：吸收（重试）→ 适应（降级）→ 恢复（哨兵+Git回滚）→ 学习（启动自检）
- **版本化演进**：大版本 +1，功能 +0.1，旧版原文不动 + 末尾对照表

详见 [DESIGN_zh.md](DESIGN_zh.md)——以上每一项的背后原理、边界条件、决策取舍都有展开。

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
