---
name: review-dispatcher
description: 系统回顾统一入口——分流器+参数化引擎。四个入口（完整更新/微调/收工/自动提醒）合并为一个核心引擎的不同调用模式。
---

# 回顾分流器（Review Dispatcher）

## 适用场景

当用户表达「回顾系统状态」意图时——不管说的是"更新历史记录"还是"微调一下"还是"收工"——本 skill 负责判断走哪条路径、打包什么参数、然后交给 review-creator 执行。

**本 skill 是判断层，不执行具体的文件写入。** 写入由 review-creator 完成。

---

## 触发词路由

```
触发词 / 系统事件
    │
    ├─ "更新历史记录" / "全面回顾" / "写回顾" / "做回顾"
    │   └─ PATH_A: 完整更新
    │
    ├─ "微调一下" / "补一下历史记录" / "微调历史记录"
    │   └─ PATH_B: 微调
    │
    ├─ "结束" / "收工" / "全结束吧"
    │   └─ PATH_C: 收工（先判定再选参数）
    │
    └─ 系统启动事件
        └─ PATH_D: 自动提醒（仅提醒，不执行）
            逻辑由 system-watch 第 6 项执行，本 skill 不处理
```

---

## 一、PATH_A：完整更新

### 触发词

`更新历史记录` / `写历史记录` / `全面回顾` / `写回顾` / `做回顾`

### 参数包

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

### 执行流程

1. **告知用户**：「开始完整更新，先跑预检...」
2. **调用 review-creator**，传入参数包
3. review-creator 执行 Phase 0-7（全 8 Phase，含深度原理检查）
4. **不自动记忆**：完整更新后由用户决定是否记忆
5. 完成后告知产出的文件路径

### 边界情况

- 上次更新在 2 小时内 → 提醒用户「最近刚更新过」，仍执行（用户可能是修了东西要覆盖）
- VSCode 端不可访问 → check_vscode_side 降级为 false，记录哨兵

---

## 二、PATH_B：微调

### 触发词

`微调一下` / `补一下历史记录` / `微调历史记录`

### 参数包

```yaml
create_new_version: false
full_scan: false
check_vscode_side: true
mark_arch_consumed: true
audit_resilience: false
principle_check: "light"
auto_memory: false
downstream_propagate: false
```

### 执行流程

1. **先检查 VSCode 端变化**：
   - `ls -lt C:/Users/yrh/.claude/projects/e--6-CodeData/memory/` → 对比最新历史记录时间戳
   - 深读新文件 → 检查 arch-changes.jsonl 未消费条目
   - 有 VSCode 变化 → 一并纳入
2. **调用 review-creator**，传入参数包
3. review-creator 执行 Phase 0 + Phase 3（轻量原理检查） + Phase 6（追加模式）
4. 产出：追加到最新版本文件末尾 + 同步使用指南（不新建版本号、不新建文件）
5. 完成后告知追加了哪些内容

### 关键修正（v4.3 起）

⚠️ **微调也标记 arch-changes 为 consumed**。之前只有完整更新（PATH_A）标记，导致微调和收工路径下 arch-changes 累积不清理。现在所有路径统一消费。

### 边界情况

- 最新版本文件不存在 → 降级为 PATH_A（创建第一个版本）
- VSCode 端无变化 → 跳过 VSCode 检查段，直接处理 vault 侧变化
- 用户强制要求完整更新 → 切换为 PATH_A

---

## 三、PATH_C：收工

### 触发词

`结束` / `收工` / `全结束吧`

### 参数包（动态判定）

收工的参数包不固定——先用判定引擎确定路径，再映射为 PATH_A 或 PATH_B 的参数包。

**附加固定参数**：`auto_memory: true`（收工自动记忆，禁止再问）

### 判定引擎

```
Step 1: git diff --stat 取改动文件列表

Step 2: 查 [[量化判断标准]] §一「致命条件」（F1-F4）
    ├─ 命中 → PATH_A 完整更新 + auto_memory
    │
    ├─ 未命中 → Step 3
    │
Step 3: 查 [[量化判断标准]] §一「高权重条件」（H1-H2）
    ├─ ≥2 命中 → PATH_A 完整更新 + auto_memory
    ├─ 1 命中 → 问用户选 PATH_A 还是 PATH_B
    └─ 0 命中 → Step 4

Step 4: 查 [[量化判断标准]] §二「量级判定」
    ├─ 大改 → 建议 PATH_A + auto_memory
    ├─ 中改/小修 → PATH_B 微调 + auto_memory
    ├─ 无改动 + 无 arch-changes → 跳过更新，仅记忆
    └─ 拿不准 → PATH_B + auto_memory（标注）
```

### 执行流程

1. 运行 `git diff --stat` 取改动文件列表
2. 按判定引擎逐步判定
3. **呈现计划给用户确认**（含判定理由 + 将要执行的操作清单）
4. 用户确认后 → 调用 review-creator（PATH_A 或 PATH_B 参数包 + auto_memory）
5. review-creator Phase 7 自动执行记忆（会话总结 + 双写 + Token + 待办扫描）

### 与旧规则的关键差异

| 旧规则 | 新规则 |
|--------|--------|
| AI 「自问」是否触及原理 | 查「量化判断标准」致命/高权重条件——可执行判定 |
| AI 「自问」大改/小修 | 查「量化判断标准」文件数+规范文件数——量化规则 |
| 收工记忆要手动确认 | `auto_memory: true`——收工确认后自动记忆，不再问 |
| VSCode 变化检查散落在脚注 | 统一在 Phase 0 check_vscode_side 参数控制 |

### 边界情况

- 用户说「结束」但不想更新历史记录 → 尊重用户，跳过判定，仅记忆
- 无任何改动 → 「今天没有文件改动，直接记忆一下？」→ 确认后仅记忆
- 判定拿不准 → 呈现两个选项让用户选

---

## 四、PATH_D：自动提醒

### 触发

系统启动事件（由 system-watch 第 6 项执行）

### 逻辑

```
读 last-review.txt → 提取日期 → 算天数
    │
    ├─ 周一 + >7 天   → "📅 今天周一，距上次回顾已 N 天，要不要更新历史记录？"
    ├─ >14 天（任意天）→ "🕐 距上次系统回顾已 N 天，要不要更新历史记录？"
    └─ 1 号            → "📅 今天月初，要不要更新历史记录？说「更新历史记录」就行。"
```

### 注意

- **仅提醒，不自动执行。** 用户说「更新历史记录」后才进入 PATH_A
- 提醒逻辑的完整实现见 system-watch SKILL.md 第 6 项
- last-review.txt 位置：VSCode 端 `C:/Users/yrh/.claude/projects/e--6-CodeData/last-review.txt`（Obsidian 端可选同步副本）

---

## 五、参数接口规范

所有路径最终调用 review-creator 时，传入以下参数结构：

```yaml
# 版本控制
create_new_version: true|false     # true=新建版本文件，false=追加到最新版本
full_scan: true|false              # true=跑完整磁盘+Token+项目+记忆扫描

# 侧端感知
check_vscode_side: true|false      # true=检查 VSCode 端新变化

# 架构变更
mark_arch_consumed: true|false     # true=标记 arch-changes.jsonl 为 consumed
audit_resilience: true|false       # true=审计系统韧性（崩溃检测清单）

# 原理
principle_check: "deep"|"light"    # deep=完整原理门禁，light=仅检查致命条件

# 收尾
auto_memory: true|false            # true=自动执行记忆（双写+Token+待办）
downstream_propagate: true|false   # true=逐行检查下游传播表
```

### review-creator 调用方式

```
Skill: review-creator
Args: path=PATH_A 或 PATH_B, params=<上述参数包>
```

> review-creator 根据 `create_new_version` 决定 Phase 6 写入模式（新建 vs 追加），其他参数控制各 Phase 的 `if` 开关。

---

## 六、哨兵

| 断裂场景 | pipeline 标识 | recovery |
|---------|-------------|------|
| 判定引擎在 git diff 时 vault 不可访问 | `review-dispatcher` | "确认 vault 可访问后说「更新历史记录」或「收工」重试" |
| 调用 review-creator 失败 | `review` | "说「更新历史记录」重试" |

哨兵写入 `~/.claude/failed-ops.jsonl`。

---

## 七、修改记录

| 日期 | 变更 | 来源 |
|------|------|------|
| 2026-06-09 | 初始版本：统一四个入口为参数化引擎 | review-dispatcher 统一机制重构 |
