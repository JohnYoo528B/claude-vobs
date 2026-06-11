---
name: review-creator
description: When the user says "更新历史记录" / "写历史记录" / "全面回顾" / "写回顾" / "做回顾" — generates a unified history record (architecture changes + time snapshot) in Obsidian.
---

# 历史记录创建器（Review Creator）

## 适用场景

记录 vault 架构变更 + 资源快照到 `_系统管理/系统历史记录/`。触发词路由见 review-dispatcher SKILL.md（唯一入口）。代码级变更请用 dev-log-creator。

---

## 参数接口（review-dispatcher 传入）

本 skill 接收来自 review-dispatcher 的参数包，控制各 Phase 的执行开关。**参数由 dispatcher 打包，creator 不自行判断路径。**

```yaml
# 版本控制
create_new_version: true|false     # true=新建版本文件(Step 9-13完整)，false=追加到最新
full_scan: true|false              # true=跑 Step 1-6 全量扫描

# 侧端感知
check_vscode_side: true|false      # true=Phase 0 检查 VSCode 端新变化

# 架构变更
mark_arch_consumed: true|false     # true=Step 7c 标记 arch-changes consumed
audit_resilience: true|false       # true=Step 7.5 审计韧性

# 原理
principle_check: "deep"|"light"    # deep=Step 8 完整原理门禁，light=仅检查致命条件 F1-F4

# 收尾
auto_memory: true|false            # true=Phase 7 自动记忆（收工路径专用）
downstream_propagate: true|false   # true=Phase 7 逐行检查下游传播表
```

### 默认值（无参数时）

如果直接调用 review-creator（未通过 dispatcher），默认走 PATH_A 完整更新：

```yaml
create_new_version: true
full_scan: true
check_vscode_side: true
mark_arch_consumed: true
audit_resilience: true
principle_check: "deep"
auto_memory: false
downstream_propagate: true
```

---

## 执行阶段（8 Phase，映射原 15 步）

原 15 步按以下方式重组为 8 个 Phase。**每步的具体操作不变，只加 `if` 开关。**

```
Phase 0: 预检（所有路径）
  ├─ check_vscode_side → 检测 VSCode 端变化
  ├─ [所有路径] 读取 arch-changes.jsonl 未消费条目
  └─ [所有路径] 读取 VERSION.md 版本号

Phase 1: 全量扫描 [if full_scan]
  └─ 原 Step 1-5：日期+版本号 → 磁盘 → Token → 项目 → 记忆

Phase 2: 对比上次 [if full_scan]
  └─ 原 Step 6

Phase 3: 架构变更采集 [所有路径]
  ├─ mark_arch_consumed → 消费 arch-changes.jsonl
  ├─ [所有路径] git diff 兜底
  └─ ⚠️ 关键修正：微调(PATH_B)和收工(PATH_C)也标 consumed

Phase 4: 原理判定 [所有路径]
  ├─ principle_check="deep" → 原 Step 8 完整门禁 6 条
  └─ principle_check="light" → 快速自检（仅 F1-F4 致命条件）

Phase 5: 韧性审计 [if audit_resilience]
  └─ 原 Step 7.5

Phase 6: 写入产出
  ├─ create_new_version=true → 原 Step 9-13（新文件+新指南+归档+VERSION）
  └─ create_new_version=false → 追加到最新版本文件 + 同步使用指南

Phase 7: 收尾 [所有路径]
  ├─ 更新 last-review.txt（两端都写）
  ├─ downstream_propagate → 下游传播表逐行检查
  └─ auto_memory → 自动记忆（会话总结+双写+token记录）

Phase 8: 自动提醒 [PATH_D only]
  └─ 仅 system-watch 第 6 项执行，creator 不处理
```

---

> 📍 检查点：开始前检查 `~/.claude/checkpoints/review-creator.json`，有有效断点则从此步继续。每步完成写入 checkpoint。

---

## 反馈检查（自动，会1d 最小闭环）

> 每次执行前自动运行。不阻断主流程，失败静默跳过。

1. 读取 `_系统管理/系统健康/ai-output-tracker.md`，找 `Skill = review-creator` 的最新一条产出记录
2. 如有记录且该记录未被反馈检查过：
   - 跑 `git diff <基线commit> HEAD -- "<产出文件>"` 看用户是否修改了上次的历史记录/使用指南
   - 无修改 → 在 tracker 的「反馈检查日志」表记一行，跳过
   - 有修改 → 读 diff，分析修改模式 → 写入 `memory/feedback-review-creator.md` → tracker 记日志
3. 继续正常执行流程

---

## Phase 0：预检（所有路径）

### 0a. VSCode 端变化检测 `[if check_vscode_side]`

```bash
ls -lt C:/Users/yrh/.claude/projects/e--6-CodeData/memory/
```
对比最新历史记录/微调的时间戳，如有新文件 → 深读 → 纳入本次。

### 0b. arch-changes.jsonl 未消费条目 `[所有路径]`

读取 `C:\Users\yrh\.claude\projects\e--6-CodeData\arch-changes.jsonl`，筛选 `consumed: false` 条目。存在则读入，待 Phase 3 处理。

### 0c. 版本号读取 `[所有路径]`

读取 `_系统管理/系统健康/VERSION.md`，提取当前版本号。

### 0d. max_tokens 预估 `[所有路径]`（v4.5 新增）

1. 根据当前路径判定任务复杂度：
   - PATH_A（完整更新）→ HEAVY → max_tokens = 16000
   - PATH_B（微调/追加）→ STANDARD → max_tokens = 8000
   - PATH_C（收工）→ 根据量级判定结果选择
2. 预估本次产出文件大小，如 >100K tokens → 先告知用户确认
3. 设定对应 max_tokens 后继续

> 参考：[[模型路由规则]]

📍 Phase 0 完成 → checkpoint {phase:0}

---

## Phase 1：全量扫描 `[if full_scan]` → 原第一步至第五步

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

---

## Phase 3：架构变更采集 `[所有路径]`

## 第七步：检查架构变更

### 7a. 读取架构变更管道（优先）

1. 读取 `C:\Users\yrh\.claude\projects\e--6-CodeData\arch-changes.jsonl`
2. 筛选 `consumed: false` 的条目
3. 将这些条目直接填入下方「架构变更」表格（summary → 条目列，before/after → 变更前/变更后列，impact → 原因列）
4. 如文件不存在或无未消费条目 → 继续 7b

### 7b. 对比 vault 实际状态（兜底）

1. `[if full_scan]` 读取 `_系统管理/系统历史记录/` 下当前大版本系列文件夹中的最新版本文件
2. `[if full_scan]` 对比 vault 实际状态（文件夹结构、命名规则、工作流程、skill、CLAUDE.md 等）
3. `[所有路径]` `git diff --stat` 兜底——检查本次会话实际改了哪些文件
4. 找出 7a 未覆盖的差异，补入「架构变更」表格
5. 7a + 7b + git diff 均无变更 → 表格写"本期无架构变更"

### 7c. 标记已消费 `[if mark_arch_consumed]`

所有条目处理完后，将 arch-changes.jsonl 中本次使用的条目标记为 `consumed: true`。

> ⚠️ **关键修正（v4.3）**：微调(PATH_B)和收工(PATH_C)也设 `mark_arch_consumed: true`。之前仅完整更新标记，导致微调/收工路径下 arch-changes 累积不清理。

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

📍 Phase 5（第七步半）完成 → checkpoint {phase:5}

---

## Phase 4：原理判定 `[所有路径]`

## 第八步：检查原理变更

### 模式选择

| 参数 | 行为 |
|------|------|
| `principle_check="deep"` | 完整原理门禁：读原理文件 → 逐条判定实质变化 → 触发版本化流程（本步 1-4 全部执行） |
| `principle_check="light"` | 快速自检：仅检查 [[量化判断标准]] §一 致命条件 F1-F4（是否动了原理/跨端/数据流文件），是 → 在健康信号中标注「原理文件有改动」，但不触发版本化。告知用户「建议下次完整更新时检查原理」 |

### deep 模式完整流程

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

## Phase 6：写入产出

### 模式选择

| 参数 | 行为 | 产出 |
|------|------|------|
| `create_new_version=true` | 完整写入（原 Step 9-13） | 新版本文件 + 新使用指南 + 旧版归档 + VERSION 同步 |
| `create_new_version=false` | 追加模式 | 追加到最新版本文件末尾 + 同步使用指南（不新建版本号、不新建文件） |

---

### 6A. 新建模式 `[if create_new_version=true]` → 原第九至十三步

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

### 6B. 追加模式 `[if create_new_version=false]`

不新建版本文件和版本号。在最新版本文件末尾追加内容：

1. **读取最新版本文件**：`_系统管理/系统历史记录/<当前大版本系列>/` 下最新文件
2. **追加架构变更**：在「架构变更」表中追加新行（如原有"本期无架构变更"则替换）
3. **追加做了什么**：在「做了什么」列表追加新条目
4. **同步使用指南**：更新 `Claude VObS 使用指南-<版本号>.md`（版本号不变，仅内容微调）
   - 如功能速查表有新触发词 → 追加
   - 如工作流有变化 → 更新对应小节
   - 如目录结构变化 → 更新 §四 目录树
5. **不更新 VERSION.md**：版本号不变，不追加版本历史行
6. **不归档旧指南**：指南版本号不变，原地修改

📍 追加模式完成 → checkpoint {phase:6}

### 登记产出追踪器

写入完成后，在 `_系统管理/系统健康/ai-output-tracker.md` 的「产出记录」表追加（至少包含版本历史文件，使用指南有更新则也追加）：

| # | <时间> | review-creator | <版本历史文件路径> | `git rev-parse HEAD` |

---

## Phase 7：收尾 `[所有路径]`

### 14a. 更新追踪文件

```bash
echo "YYYY-MM-DD | 历史记录 | <文件名>" > C:/Users/yrh/.claude/projects/e--6-CodeData/last-review.txt
```

> 两端都写：VSCode 端（上述路径）+ Obsidian vault 可选副本（`_系统管理/系统健康/last-review.txt`）

📍 14a 完成 → checkpoint {step:14}

### 14b. 下游传播表检查 `[if downstream_propagate]`

1. 读取 `[[下游传播表]]`（`_系统管理/设计规范/核心设计规范/下游传播表.md`）
2. 对照本次改动类型，逐行检查命中项是否已执行
3. 未执行的 → 补执行
4. 因外部条件不适用（如 VSCode 仓库不可达）→ 标注原因并写入哨兵

📍 14b 完成 → checkpoint {step:14b}

### 14c. 自动记忆 `[if auto_memory]`

⚠️ 仅收工路径（PATH_C）设置 `auto_memory: true`。此时自动执行以下操作，**禁止再问「要不要记忆一下」**：

1. 1-3 句话总结本次会话做了什么
2. 扫描待办：Grep `status: (todo|doing)` + 读取 [[待办事项]] → 填入「未完成」列表
3. 去重检查：对比 `.claude/上次会话.md` + 当月 `模型对话记忆/<YYYY-MM>.md` 已有内容
4. 双写：`.claude/上次会话.md`（覆盖）+ `模型对话记忆/<YYYY-MM>.md`（追加）
5. 记录 Token：追加到 `_系统管理/系统健康/token-usage.jsonl`

> 详细记忆格式见 CLAUDE.md §「结束时」。

📍 14c 完成 → checkpoint {step:14c}

### 14d. 批量更新 memory last-reviewed `[if create_new_version]`（v4.5 新增）

当 `create_new_version=true` 时（即 PATH_A 新建版本），自动执行：

1. 扫描 Obsidian 端 memory：`C:\Users\yrh\.claude\projects\E--2-Notes-0GithubLibraryForObsidian-John-sDataForObsidian\memory\*.md`
2. 扫描 VSCode 端 memory：`C:\Users\yrh\.claude\projects\e--6-CodeData\memory\*.md`
3. 对两端所有 .md 文件（排除 MEMORY.md 和 memory-template.md），将 `last-reviewed` 字段更新为新版本号（如 `v4.5`）
4. 更新两端 MEMORY.md 的 `last-reviewed` 为新版本号
5. 完成后输出：`📝 两端 memory last-reviewed → v<新版本号>（N 个文件）`

> 只更新 `last-reviewed` 字段值，不修改其他字段或文件内容。

📍 14d 完成 → checkpoint {step:14d}

📍 Phase 7 完成 → checkpoint {phase:7}

---

## 第十五步：告知用户

**完整更新模式（PATH_A）**：
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

**追加模式（PATH_B / 微调）**：
```
✅ 微调已追加到最新版本文件：
  → _系统管理/系统历史记录/<系列>/v<版本号>版本-YYYYMMDDHHMM.md
✅ 使用指南已同步：
  → Claude VObS 使用指南-<版本号>.md（版本号不变）
📝 追加内容：<简述>
```

**收工模式（PATH_C，含 auto_memory）**：
```
（以上 PATH_A 或 PATH_B 的输出）
🧠 记忆已保存：
  → .claude/上次会话.md
  → _系统管理/模型对话记忆/<YYYY-MM>.md
🔢 Token 已记录
```

📍 第十五步完成 → checkpoint {step:15}

---

## 三件事记住

1. **只有一个概念：历史记录** — 不再区分"周回顾""月回顾"，统一用"更新历史记录"触发
2. **磁盘数据当场跑命令获取，不存中间文件** — 每次数据是最新的
3. **Token 是估算不是精确值** — 偏差不可怕，趋势才重要
4. **历史记录 = 五件套** — 生成版本文件 → 更新使用指南 → 验证 → 归档旧版 → 同步 VERSION.md，一步不漏
5. **v4.3 起：四个入口合并为一个引擎** — 统一入口在 review-dispatcher，review-creator 只做执行。各路径通过参数包控制行为，不各自维护一套逻辑
6. **v4.3 起：追加模式** — `create_new_version=false` 时追加到最新版本文件 + 同步使用指南，不新建版本号

---

## 关联文件

- 统一入口：`~/.claude/skills/review-dispatcher/SKILL.md`
- 下游传播表：`[[下游传播表]]`
- 量化判断标准：`[[量化判断标准]]`
- 版本更新维护规范：`[[版本更新维护规范]]`
