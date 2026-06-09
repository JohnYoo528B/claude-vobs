---
date: 2026-06-09
type: spec
status: active
tags:
  - 系统设计
  - 记忆管理
---

# Memory Frontmatter 标准

> v4.5 起生效。定义所有 memory 文件（Obsidian + VSCode 两端）的 frontmatter 字段标准。

---

## 必填字段（根级）

所有 memory 文件 frontmatter 必须包含以下根级字段：

| 字段 | 类型 | 必填 | 说明 | 示例 |
|------|------|:--:|------|------|
| `name` | string | ✅ | 文件英文短名（kebab-case），与文件名一致（不含 .md） | `vault-root-directory` |
| `description` | string | ✅ | 一句话描述文件用途 | `Obsidian vault 根目录，所有笔记和知识管理的中心` |
| `scope` | enum | ✅ | 记忆层级：`global` / `project` / `user` | `project` |
| `last-reviewed` | string | ✅ | 最后审核时的系统版本号 | `v4.5` |
| `metadata` | object | ✅ | 元数据容器，含 `node_type` 等固定字段 | 见下方 |

### scope 三层定义

| 值 | 层级 | 含义 | 生命周期 |
|:--:|------|------|---------|
| `global` | L1 | 所有项目通用，常驻上下文 | 永久 |
| `project` | L2 | 单项目专属，按需加载 | 项目完结可清理 |
| `user` | L3 | 个人偏好，影响回答风格 | 长期，用户主动删除 |

### metadata 子字段

```yaml
metadata:
  node_type: memory          # 固定值
  originSessionId: <uuid>    # 创建时的 session ID
  # 以下按需：
  type: user                 # VSCode 端部分文件使用
  project: <项目名>          # scope=project 时必填
  tags: []                   # 可选标签
```

---

## 位置规则

- `scope` 和 `last-reviewed` **必须在根级**，不能嵌套在 `metadata` 内
- 这是 v4.5 的系统性约束——之前部分文件将 `last-reviewed` 放在 `metadata` 内，现已统一

---

## 版本号更新

- 每次「更新历史记录」时，review-creator Phase 7 自动将所有 memory 文件的 `last-reviewed` 更新为当前版本号
- 手动修改系统规范时，应在操作收尾中检查关联 memory 是否需要更新 `last-reviewed`

---

## 示例

### 标准 memory 文件 frontmatter（v4.5+）

```yaml
---
name: vault-root-directory
description: Obsidian vault 根目录，所有笔记和知识管理的中心
scope: project
last-reviewed: v4.5
metadata:
  node_type: memory
  originSessionId: 6c2e1eee-1614-4d20-9455-ecb414e19a57
---
```
