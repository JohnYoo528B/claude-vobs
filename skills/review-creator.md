---
name: review-creator
description: When the user says "更新历史记录" / "写历史记录" / "全面回顾" / "写回顾" / "做回顾" — generates a unified history record (architecture changes + time snapshot) in Obsidian.
---

# 历史记录创建器

## 适用场景

记录 vault 架构变更 + 资源快照到 `_系统管理/系统历史记录/`。触发词路由见 CLAUDE.md → "统一意图路由表"（权威来源）。代码级变更请用 dev-log-creator。

---

> 📍 检查点：开始前检查 `~/.claude/checkpoints/review-creator.json`，有有效断点则从此步继续。每步完成写入 checkpoint。

## 第一步：获取当前日期 + 版本号

```bash
date "+%Y %m %d %u %W"
```

读取 `_系统管理/系统历史记录/` 下各子文件夹（排除 `早期记录/`、`GitHub公开版/`），找到当前大版本系列文件夹（如 `v3系列版本/`），扫描其中的最新版本号。版本号递增规则：

| 变动 | 版本涨幅 |
|------|---------|
| 格局重组、不兼容变更 | 主版本 +1（如 2.x → 3.0） |
| 新增显著功能/内容 | 次版本 +0.1 |
| 小修小补 | 次版本 +0.1 |

📍 第一步完成 → checkpoint {step:1}

---

## 第二步：跑磁盘扫描命令

```bash
# 磁盘总览
df -h /c/ /e/ /d/

# 代码区
du -sh e:/6_CodeData/ e:/6_CodeData/*/ 2>/dev/null

# AI 运行数据
du -sh C:/Users/yrh/.claude/ C:/Users/yrh/.claude/projects/ C:/Users/yrh/.claude/file-history/ C:/Users/yrh/.claude/plans/ C:/Users/yrh/.claude/skills/ C:/Users/yrh/.claude/telemetry/ 2>/dev/null

# 知识库（Obsidian Vault）
du -sh E:/2_Notes/0GithubLibraryForObsidian/John-sDataForObsidian/ E:/2_Notes/0GithubLibraryForObsidian/John-sDataForObsidian/.obsidian/ E:/2_Notes/0GithubLibraryForObsidian/John-sDataForObsidian/.git/ E:/2_Notes/0GithubLibraryForObsidian/John-sDataForObsidian/1主题研究/ E:/2_Notes/0GithubLibraryForObsidian/John-sDataForObsidian/2日志与笔记/ E:/2_Notes/0GithubLibraryForObsidian/John-sDataForObsidian/3工作与产出/ E:/2_Notes/0GithubLibraryForObsidian/John-sDataForObsidian/_系统管理/系统历史记录/ 2>/dev/null
```

📍 第二步完成 → checkpoint {step:2}

---

## 第三步：统计 Token 用量

1. 读取 **VSCode 端** Token 日志：`C:\Users\yrh\.claude\projects\e--6-CodeData\token-usage.jsonl`
2. 读取 **Obsidian 端** Token 日志：`E:/2_Notes/0GithubLibraryForObsidian/John-sDataForObsidian/_系统管理/系统健康/token-usage.jsonl`
3. 两端汇总：筛选近期条目，统计会话数，汇总估算 token
4. 在「资源状态 → Token」表格中分两端展示（VSCode / Obsidian），再合计
5. 如果某端文件不存在或为空，标注"暂无数据（即日起开始追踪）"

📍 第三步完成 → checkpoint {step:3}

---

## 第四步：收集项目进展

扫描 `e:\6_CodeData\` 下所有项目文件夹，对每个项目记录：
- 文件夹名 + 大小
- 状态：14 天内修改过 → 活跃，否则 → 休眠，已标记完结 → 完结

📍 第四步完成 → checkpoint {step:4}

---

## 第五步：检查三层记忆健康

### 5a. L2 完结清理（现有逻辑）

1. 读取 `C:\Users\yrh\.claude\projects\e--6-CodeData\memory\MEMORY.md`
2. 找出所有标记 ✅（完结）的项目分组
3. 对每个完结项目，检查 `memory/` 下是否有 `scope: project` + `project: <该项目>` 的记忆文件
4. 如有，在后续"架构变更"表格中增加一行提醒：
   ```
   | L2 记忆清理 | <项目名> 有 N 条 project 级记忆 | 建议手动清理或升级为 global |
   ```

> ★ L2 记忆随项目完结可清理，防止记忆无限膨胀。如无完结项目或已全部清理，跳过本步。

### 5b. L1/L3 过时检测（v4.1 新增）

1. 扫描两端 memory 目录（VSCode: `C:\Users\yrh\.claude\projects\e--6-CodeData\memory/`，Obsidian: `C:\Users\yrh\.claude\projects\E--2-Notes-0GithubLibraryForObsidian-John-sDataForObsidian\memory/`）
2. 提取各 .md 文件的 `last-reviewed` frontmatter 字段
3. 与 `_系统管理/系统健康/VERSION.md` 当前版本号对比
4. 不一致的 → 在「健康信号」中列出：`🟡 N 个 memory 文件 last-reviewed ≠ vX.X：<文件名>`
5. 无 `last-reviewed` 字段的 → ⚪ 标注"待确认"，提醒用户补字段

> ★ 只报告不修改。过时提醒由 system-watch 启动时处理，本步做的是 review 时的汇总报告。

📍 第五步完成 → checkpoint {step:5}

---

## 第六步：对比上一次记录

读取 `_系统管理/系统历史记录/` 下当前大版本系列文件夹中的最近一次版本文件，提取资源简报各项数值，与本次对比，填入"较上次"列。

异动阈值（超过时在表格下方加一行说明）：

| 指标 | 阈值 | 归因方向 |
|------|------|---------|
| AI 数据总大小 | ±5MB | 标注增长/缩减最多的目录 |
| 代码区总大小 | ±100KB | 新增/删除项目，或源码大幅修改 |
| 知识库总大小 | ±1MB | 标注变化最大的目录 |
| Token 消耗 | ±20% | 标注主要消耗项目的变化 |
| 磁盘已用 | 不归因 | 只看健康（低于 20% 提醒），变化不管 |

📍 第六步完成 → checkpoint {step:6}

---

## 第七步：检查架构变更

### 7a. 读取架构变更管道（优先）

1. 读取 `C:\Users\yrh\.claude\projects\e--6-CodeData\arch-changes.jsonl`
2. 筛选 `consumed: false` 的条目
3. 将这些条目直接填入下方「架构变更」表格（summary → 条目列，before/after → 变更前/变更后列，impact → 原因列）
4. 如文件不存在或无未消费条目 → 继续 7b

### 7b. 对比 vault 实际状态（兜底）

1. 读取 `_系统管理/系统历史记录/` 下当前大版本系列文件夹中的最新版本文件
2. 对比 vault 实际状态（文件夹结构、命名规则、工作流程、skill、CLAUDE.md 等）
3. 找出 7a 未覆盖的差异，补入「架构变更」表格
4. 7a + 7b 均无变更 → 表格写"本期无架构变更"

### 7c. 标记已消费

所有条目处理完后，将 arch-changes.jsonl 中本次使用的条目标记为 `consumed: true`。

📍 第七步完成 → checkpoint {step:7}

---

## 第七步半：审计系统韧性

1. 读取 `_系统管理/系统健康/系统崩溃检测清单.md`
2. 逐项检查启用机制清单 + 四大能力的当前状态
3. 将检查结果填入各行的「状态」列（✅ 正常 / ⚠️ 需关注 / ❌ 已断裂）
4. ⚠️ 判定"活跃项目"时，**必须读取项目日志确认实际状态**（完结/活跃），不能只看磁盘扫描的修改时间
5. 更新底部「上次审计」日期和版本号
6. 如有 ❌ 项，在历史记录的「架构变更」表中增加对应条目

---

📍 第七步半完成 → checkpoint {step:7.5}

---

## 第八步：检查原理变更

1. 读取 `_系统管理/设计规范/核心设计规范/系统设计原理.md`
2. 对比本次架构变更表，逐条判定是否涉及核心设计思路的**实质变化**：
   - 文件夹改名/移动 → 不涉及
   - 文档合并/拆分 → 可能涉及（判定标准：核心概念的定义或关系是否改变）
   - 新增/删除系统性机制 → 可能涉及（判定标准：是否新增或废弃一条设计原则）
   - 命名规范变更 → 不涉及
   - 表面文件操作但实为系统性约束变更 → 实质变化（例：删除 `_外部参考/` 文件夹 + 确立草稿箱为唯一入口）
   - 跨端规则重定义 → 可能涉及
3. **有实质变化** → 将当前 `系统设计原理.md` 归档到 `原理旧版/系统设计原理-v<旧起始版本>-v<旧结束版本>.md`。结束版本 = 触发归档的新版本号（如 v3.7 时代原理被 v4.0 替代 → `v3.7-v4.0`）。写新版原理文件——适用版本标注 `v<新版本号> 起`（不写死结束版本），在变更处注明增量。在「架构变更」表中增加一行：

   | 原理版本化 | `<旧版范围（已闭合）>` | `v<新版本号> 起`，关键增量：<简述> |

4. **无实质变化** → 跳过，在「健康信号」中注明"原理无实质变化，不需出新版"

> ★ 判定铁律：核心设计原理（三层架构、单源权威、角色分化、设计密度、韧性工程等）的**定义、关系、或约束条件**发生变化 → 实质变化。表述优化、结构调整、示例补充 → 非实质变化，不触发新版。

📍 第八步完成 → checkpoint {step:8}

---

## 第九步：按统一模板写文件

### 文件命名

`v<版本号>版本-<YYYYMMDDHHMM>.md`（v 前缀 + 分钟精度）

示例：`v3.0版本-202606080255.md`

### 写入位置

```
E:/2_Notes/0GithubLibraryForObsidian/John-sDataForObsidian/_系统管理/系统历史记录/<当前大版本系列>/
```

### 统一模板

```markdown
---
date: YYYY-MM-DD
type: history
tags:
  - 类型/历史记录
---

# v<版本号>版本 — 一句话标题

> 一句话概括。

## 架构变更

| 条目 | 变更前 | 变更后 | 原因 |
|------|--------|--------|------|
| xxx | xxx | xxx | xxx |

（无变更时写"本期无架构变更"）

---

## 📸 YYYY-MM-DD 历史快照

> 一句话概括这段时期。

### 做了什么

- 

### 卡点

- 

### 学到什么

- 

### 资源状态

#### 磁盘

| 盘符 | 已用 | 较上次 | 系统占用 | 较上次 |
|------|------|--------|---------|--------|
| C: | xxx GB | ±x GB | ~x GB | ±x MB |
| E: | xxx GB | ±x GB | ~x MB | ±x MB |
| D: | xxx GB | ±x GB | ~x MB | — |

> ★ 磁盘健康：正常 / ⚠️（某盘低于 20%）

#### AI 数据（~/.claude/）

| 目录 | 大小 | 较上次 |
|------|------|--------|
| projects/ | xx MB | ±x MB |
| file-history/ | xx MB | ±x MB |
| plans/ | xx KB | ±x KB |
| skills/ | xx KB | ±x KB |
| telemetry/ | xx KB | ±x KB |
| **合计** | **~xx MB** | **±x MB** |

> （总变化 >±5MB 时说明原因）

#### 代码区（e:\6_CodeData\）

| 项目 | 大小 | 较上次 | 状态 |
|------|------|--------|------|
| xxx | xx KB | ±x KB | 活跃 / 休眠 / 完结 |
| **合计** | **xx KB** | **±x KB** | |

> 状态判定：14 天内修改过 = 活跃，否则 = 休眠。完结项目标注完结。

#### 知识库（Vault）

| 目录 | 大小 | 较上次 |
|------|------|--------|
| .obsidian/ | xx MB | — |
| .git/ | xx MB | ±x MB |
| 1主题研究/ | xx KB | ±x KB |
| _系统管理/系统历史记录/ | xx KB | ±x KB |
| 2日志与笔记/VibeCoding开发日志/ | xx KB | ±x KB |
| 3工作与产出/ | xx KB | ±x KB |
| **合计** | **~xx MB** | **±x MB** |

> 工具链 ~920 MB，静态不追踪。（总变化 >±1MB 时说明原因）

#### Token

| 指标 | 本次 | 较上次 |
|------|------|--------|
| 会话数 | x 次 | ±x |
| 估算消耗 | ~xxx K | ±x% |
| 主要消耗项目 | xxx | |

> （消耗变化 >±20% 时说明原因）

### 下一步

- [ ] 

### 健康信号

各项正常。 / ⚠️ 异常项：（展开说明）

---

*生成于 YYYY-MM-DD*
```

📍 第九步完成 → checkpoint {step:9}

---

## 第十步：更新使用指南

### 10a. 读取当前使用指南

找到 vault 根目录下 `Claude VObS 使用指南-<旧版本>.md`，读取全部内容。

### 10b. 更新内容

1. **版本号 + 日期**：frontmatter `date` 和 `**当前版本：X.X（YYYY-MM-DD）**` → 新版本号 + 今天日期
2. **功能速查表**：对比历史记录的「架构变更」表，如有新增触发词或功能，补入速查表
3. **工作流**：如有新增工作流（如新增系统机制），在 §二 新增小节
4. **FAQ**：如有新增概念或边界规则，在 §五 新增 FAQ 条目
5. **目录结构**：如文件夹有变动，更新 §四 的目录树
6. **文件名**：`Claude VObS 使用指南-<新版本>.md`

### 10c. 写入新文件

保存到 vault 根目录。

📍 第十步完成 → checkpoint {step:10}

---

## 第十一步：验证新版指南

### 11a. 完整性检查

- 功能速查表条目数 ≥ 旧版
- FAQ 条目数 ≥ 旧版
- §二 工作流覆盖所有功能速查表中的复杂操作

### 11b. 链接检查

- Grep `[[...]]` 提取新版指南中所有 Wiki 链接
- 逐一验证目标文件存在
- 断链当场修复

### 11c. 版本一致性检查

- 新版指南中所有版本引用（正文 + 目录树文件名）与新版本号一致
- 旧版本号残留 → 当场替换

📍 第十一步完成 → checkpoint {step:11}

---

## 第十二步：归档旧版使用指南

1. 在刚刚生成的历史记录文件末尾追加一节：

```markdown
## 附：旧版使用指南归档

<旧版使用指南全文，用 ``` 代码块包裹或直接粘贴>
```

2. 删除 vault 根目录下旧版使用指南文件
3. 确认：此刻 vault 中只有新版使用指南，旧版已完整归档到历史记录

> ⚠️ **铁律**：新指南创建在旧指南删除之前。确保 vault 任何时刻至少有一份使用指南。

📍 第十二步完成 → checkpoint {step:12}

---

## 第十三步：更新 VERSION.md

1. 读取 `E:/2_Notes/0GithubLibraryForObsidian/John-sDataForObsidian/_系统管理/系统健康/VERSION.md`
2. 更新 `**当前版本：v<旧版本>**` → `**当前版本：v<新版本>**`
3. 更新 frontmatter 的 `date` 为今天日期
4. 在版本历史表格顶部插入新行：`| v<新版本> | YYYY-MM-DD | <一句话概述> |`

> ⚠️ 这是版本号的**唯一权威来源**。改了这里，其他文件在各自的同步节点上自动对齐。

📍 第十三步完成 → checkpoint {step:13}

---

## 第十四步：更新追踪文件

```bash
echo "YYYY-MM-DD | 历史记录 | <文件名>" > C:/Users/yrh/.claude/projects/e--6-CodeData/last-review.txt
```

📍 第十四步完成 → checkpoint {step:14}

---

## 第十五步：告知用户

```
✅ 历史记录已生成：
  → Obsidian: _系统管理/系统历史记录/<当前大版本系列>/v<版本号>版本-YYYYMMDDHHMM.md
✅ 使用指南已更新：
  → Obsidian: Claude VObS 使用指南-<新版本>.md（旧版已归档）
✅ 版本号已同步：
  → _系统管理/系统健康/VERSION.md → v<新版本>
📊 磁盘：<总占用> / <剩余空间>
🔢 Token：约 <xxx> K
💾 磁盘状态：健康 / ⚠️ C盘低于 20%
```

📍 第十五步完成 → checkpoint {step:15}

---

## 三件事记住

1. **只有一个概念：历史记录** — 不再区分"周回顾""月回顾"，统一用"更新历史记录"触发
2. **磁盘数据当场跑命令获取，不存中间文件** — 每次数据是最新的
3. **Token 是估算不是精确值** — 偏差不可怕，趋势才重要
4. **历史记录 = 五件套** — 生成版本文件 → 更新使用指南 → 验证 → 归档旧版 → 同步 VERSION.md，一步不漏
