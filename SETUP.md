# Claude VObS 部署指南

> 纯步骤文档。不讲"为什么"，只讲"怎么做"。读完能装好。
> 想了解系统是什么 → [README_zh.md](README_zh.md)。装完了 → [FIRST-STEPS_zh.md](FIRST-STEPS_zh.md)。

---

## 前置条件

| 序号 | 你需要的东西 | 为什么需要 | 怎么获取 |
|:--:|------|---------|---------|
| 1 | **Claude Code** | AI 内核——系统的"大脑" | `npm install -g @anthropic-ai/claude-code`。需 Anthropic API Key → [console.anthropic.com](https://console.anthropic.com) |
| 2 | **Obsidian** | 笔记管理界面 | [obsidian.md](https://obsidian.md) 下载 |
| 3 | **VSCode** | 代码编辑和 Git 操作 | [code.visualstudio.com](https://code.visualstudio.com) 下载 |
| 4 | **Git** | 版本管理和克隆仓库 | `winget install Git.Git` 或 [git-scm.com](https://git-scm.com) |
| 5 | **Python 3.12** | vibe-coding 默认语言 | `winget install Python.Python.3` 或 [python.org](https://python.org) |
| 6 | **Node.js** | Claude Code 运行环境 | `winget install OpenJS.NodeJS` 或 [nodejs.org](https://nodejs.org) |

> 🧪 **自检**：装好 Claude Code 后，终端输入 `claude --version`，有输出就对了。

---

## 第一步：克隆仓库

```bash
git clone https://github.com/JohnYoo528B/claude-vobs.git
```

或者：GitHub 页面点 "Code" → Download ZIP → 解压。

解压/克隆出来的文件夹就是你的**代码工作区**。

---

## 第二步：部署 CLAUDE.md

两个文件分别重命名并放到对应位置：

| 源文件 | 重命名为 | 放到 |
|--------|---------|------|
| 仓库中的 `CLAUDE_VSCode.md` | `CLAUDE.md` | 代码工作区根目录 |
| 仓库中的 `CLAUDE_Obsidian.md` | `CLAUDE.md` | Obsidian vault 根目录 |

> Obsidian vault 根目录 = 你放所有笔记的那个文件夹。还没建？打开 Obsidian → 创建新 vault。

---

## 第三步：安装 Skills

把仓库 `skills/` 下的所有 `.md` 文件（8 个）复制到：

| 系统 | 路径 |
|------|------|
| Windows | `C:\Users\<用户名>\.claude\skills\` |
| macOS / Linux | `~/.claude/skills/` |

没有 `.claude` 或 `skills` 文件夹就新建。Windows 用户：`.claude` 是隐藏文件夹，地址栏直接输入路径回车即可。

最终结构：
```
~/.claude/skills/
├─ quick-capture.md       ← 快速捕捉想法
├─ vault-search.md        ← 全文搜索
├─ dev-log-creator.md     ← 自动生成开发日志
├─ review-dispatcher.md   ← 回顾统一入口（分流器）
├─ review-creator.md      ← 系统回顾执行引擎
├─ archive.md             ← 对话→笔记存档
├─ draft-cleanup.md       ← 草稿箱清理
└─ system-watch.md        ← 系统健康监控
```

---

## 第四步：替换路径占位符

全文搜索 `<你的`，逐项替换为你的实际路径：

| 占位符 | 替换为 | 例子 |
|--------|--------|------|
| `<你的 Obsidian vault 路径>` | 你的 Obsidian vault 完整路径 | `D:\Notes\MyVault` |
| `<你的 VSCode 代码区路径>` | 你的代码工作区完整路径 | `D:\Code\claude-vobs` |
| `<你的用户名>` | 你的 Windows 用户名 | `John` |

VSCode 里 `Ctrl+Shift+H` 全局替换。macOS 用 `Cmd+Shift+H`。

---

## 第五步：配置 settings.json

1. 复制仓库的 `settings.example.json` 到 `C:\Users\<用户名>\.claude\`（Mac：`~/.claude/`）
2. 重命名为 `settings.json`
3. 填入你的 API Key：

```json
{
  "env": {
    "ANTHROPIC_API_KEY": "sk-ant-api03-你的Key"
  }
}
```

> API Key 在 [console.anthropic.com](https://console.anthropic.com/) 创建。

---

## 第六步：建 Obsidian vault

1. 打开 Obsidian
2. 点击 "Open folder as vault"
3. 选择你的 vault 文件夹
4. 打开 设置 → 社区插件 → 关闭安全模式
5. 搜索安装 **Claudian** 插件（Obsidian 内的 Claude AI 对话插件）

仓库已自带文件夹结构：
```
你的 vault/
├─ CLAUDE.md                     ← 从 CLAUDE_Obsidian.md 重命名
├─ _系统管理/                    ← 系统管理（设计规范等）
│   └─ 设计规范/
│       ├─ 核心设计规范/           ← 核心规范文件
│       └─ _全局规范/             ← 全局行为规则
├─ ～草稿箱/                     ← 统一入口（临时想法、外部资源）
│   └─ README.md                 ← 已自带：解释什么丢进来
├─ 1主题研究/                    ← 深读产出
│   └─ README.md                 ← 已自带：判断标准和 MOC 用法
├─ 2日志与笔记/                  ← 过程记录
│   └─ README.md                 ← 已自带
└─ 3工作与产出/                  ← 成品交付
    └─ README.md                 ← 已自带
```

---

## 第七步：验证

打开终端，进入 vault 目录，输入：

```bash
claude
```

然后对 AI 说：**"启动检查"**

AI 回复系统健康状态（版本号、哨兵状态等）→ 部署成功 ✅

---

## 手机端配置（可选）

手机端能做两件事：快速捕捉想法到草稿箱、搜索 vault 里的笔记。

系统已内置手机端逻辑。你只需要把手机消息桥接到 Claude Code。

| 方案 | 需要 | 适合 |
|------|------|------|
| 🅰️ **cc-connect + 微信** | Node.js、电脑常开、微信 ClawBot | 功能最全 |
| 🅱️ **Claude 手机 App** | Claude 订阅（Pro/Max） | 最简单 |
| 🅲 **先跳过** | 无 | 不影响核心功能 |

详细步骤 → [docs/phone-setup.md](docs/phone-setup.md)

---

## 常见安装问题

### Claude Code 安装失败？

确认 Node.js 版本 ≥ 18。`node --version` 检查。版本太低 → 去 [nodejs.org](https://nodejs.org) 下载最新 LTS 版。

### 全局搜不到 `<你的`？

有些文件里的占位符可能已经被你之前的操作改了。确认 CLAUDE_Obsidian.md 和 CLAUDE_VSCode.md 中没有 `E:\` 或 `C:\Users\` 这样的硬编码路径残留即可。

### Obsidian 找不到 Claudian 插件？

确认已关闭安全模式（设置 → 社区插件 → 安全模式 → 关闭）。如果还是搜不到，检查 Obsidian 版本是否最新。

### 启动检查没反应？

1. 确认 CLAUDE.md 在 vault 根目录
2. 确认 `~/.claude/skills/` 下有 8 个 skill 文件
3. 终端输入 `claude` 看是否有错误信息
4. 还不行 → [GitHub Issues](https://github.com/JohnYoo528B/claude-vobs/issues)

---

> 📎 装完了？→ [FIRST-STEPS_zh.md](FIRST-STEPS_zh.md) 3 分钟上手
