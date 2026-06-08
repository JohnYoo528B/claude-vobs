# 手机端设置指南

## 你能用手机做什么？

配好手机端之后，你可以在手机上：

- **快速捕捉**：发"记一下 明天开会"→ 自动写入 Obsidian `00草稿箱/手机暂存/`
- **搜索笔记**：发"找一下 德州扑克"→ 返回 vault 里的相关笔记
- **批量暂存**：发"以上都存一下"→ 把最近没记的对话全部存下来

> 触发词不要求精确。系统会根据意图自动路由到正确的 Skill。

---

## 你已经有的设定

Claude VObS 已内置手机端逻辑，你不需要从头写任何东西：

| 已有设定 | 作用 |
|----------|------|
| `skills/quick-capture.md` | 手机快速捕捉的执行流程（原文照存、按 session 分文件） |
| `skills/vault-search.md` | 手机搜索笔记的执行流程（Grep 搜索、按结果数分级返回） |
| `CLAUDE_Obsidian.md` 中的触发词路由 | 识别"存/暂存/找/搜索"等自然语言并路由到正确 Skill |

你只需要把手机消息桥接到 Claude Code。其余全部自动处理。

---

## 三条路，选一条

### 🅰️ cc-connect + 微信（功能最全）

**效果**：在微信里像聊天一样用 Claude Code。发"记一下 xxx"就自动存到 Obsidian，发"找 xxx"就自动搜 vault。

**前置条件**：

| 条件 | 怎么检查 |
|------|---------|
| Node.js 已安装 | 终端输入 `node --version`，有版本号即可 |
| 电脑能常开不休眠 | cc-connect 进程需要持续运行 |
| 微信 + ClawBot 插件 | 微信版本 ≥ 8.0.70，iOS 用户可直接扫码授权（Android 视灰度情况） |

**安装步骤**：

```bash
# 1. 安装 cc-connect
npm install -g cc-connect@beta

# 2. 绑定微信
cc-connect weixin setup --project default
# 终端会出现二维码 → 手机微信扫码确认授权

# 3. 启动
cc-connect
```

**配置要点**：

编辑 `~/.cc-connect/config.toml`：
- `workDir`：指向你的代码工作区路径
- `agent`：设为 `claudecode`
- `allowedUsers`：填你的微信用户 ID（安全限制，只接受你的消息）

**验证**：微信发"记一下 测试消息"→ 检查 Obsidian `00草稿箱/手机暂存/` 是否有新文件。

**设为开机自启**（可选）：
```bash
cc-connect daemon install
```

> ⚠️ **注意**：cc-connect 是开源社区项目（[GitHub: chenhg5/cc-connect](https://github.com/chenhg5/cc-connect)），微信 iLink API 由腾讯提供，稳定性取决于腾讯的政策变化。电脑关机或断网时手机端不可用。

---

### 🅱️ Claude 手机 App（最简单）

**效果**：在 Claude 官方 App 里直接跟 Claude Code 对话。适合偶尔用手机查笔记、记想法的场景。

**前置条件**：

| 条件 | 怎么检查 |
|------|---------|
| Claude 订阅（Pro 或 Max） | 在 claude.ai 账号设置里查看 |

**使用方式**：

1. 在手机上下载 Claude 官方 App（iOS App Store / Android 待定）
2. 登录你的 Claude 账号
3. Claude Code 的 Remote Control 功能会自动同步你的项目上下文
4. 直接发消息即可

**局限**：
- 不自动路由到 Obsidian——手机端发的内容不会自动写入 vault
- 需要手动说明你在哪个项目、要做什么
- 快速捕捉和 vault 搜索不如方案 A 流畅

**适合**：不想折腾配置、偶尔手机用一下的用户。

---

### 🅲 先跳过

手机端不影响 VSCode + Obsidian 两端的核心功能。你可以在电脑上正常写代码、管笔记，随时回来看这篇指南配手机端。

---

## 常见问题

### 微信会被封号吗？

不会。cc-connect 走的是微信官方 iLink Bot API（2026 年开放），不是破解或外挂。合规使用不存在封号风险。

### 电脑必须一直开着？

方案 A 是的。cc-connect 在你的电脑上运行，电脑关机 = 微信消息收不到。如果做不到常开，用方案 B（Claude App）或只用 VSCode + Obsidian 两端。

### 消息安全吗？

方案 A 的消息经腾讯服务器中转，不是端到端加密。**不要通过微信发密码、API Key、银行卡号等敏感信息。**

### 装完没反应？

1. 确认 cc-connect 进程在运行（终端里有输出）
2. 确认微信已扫码授权
3. 确认 Claude Code 本身可用（在电脑终端直接输入 `claude` 测试）
