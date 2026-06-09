---
date: 2026-06-09
type: spec
status: active
tags:
  - system-design
  - architecture
---

# System Design Principles

> The full architectural picture of Claude VObS. Read this before modifying system rules.

---

## 1. System Overview

```
┌──────────────────────────────────────────────────┐
│            THREE TERMINALS                        │
│                                                   │
│  VSCode (Engineer)  Obsidian (Editor)  Phone (PA) │
│  Code / debug       Notes / archive     Capture   │
│  CLAUDE.md          CLAUDE.md           cc-connect│
│  code workspace\    vault\              → skill   │
│         │                │                │       │
│         └────────────────┼────────────────┘       │
│                          │                        │
│          THREE-LAYER MEMORY (~/.claude/)          │
│         global ──→ project ──→ user              │
│         (cross-project) (per-project) (personal)  │
│                          │                        │
│              EIGHT SKILLS                          │
│  quick-capture │ vault-search │ dev-log-creator   │
│  review-dispatcher → review-creator               │
│    archive │ draft-cleanup │ system-watch         │
└──────────────────────────────────────────────────┘
```

**Data flows**:

- VSCode writes code → `dev-log-creator` → Obsidian dev log
- Obsidian changes architecture → `review-dispatcher` → `review-creator` → system history
- Phone message → `quick-capture` / `vault-search` → Obsidian vault
- Chat archive → `archive` → Obsidian notes
- Inbox overflow → `draft-cleanup` → clean & categorize
- System health → `system-watch` → startup A-checks + B-class cron patrol
- Cross-system alignment via `project` frontmatter field + identical folder names

---

## 2. Vault Content Architecture

Beyond the system layer, the vault has its own organizational logic — based on **content maturity**:

| Layer | Folder | Content State | Examples |
|----|--------|---------|---------|
| Entry | `～草稿箱/` | Raw, unprocessed | Fleeting ideas, external links, phone captures |
| Processing | `1主题研究/` · `2日志与笔记/` | In progress | Deep analysis (think→write), process records (do→log) |
| Delivery | `3工作与产出/` | Complete | Deliverables ready to share |

Flow is one-way: Entry → Processing → Delivery.

### Architecture A: Inbox as Sole Entry Point

All external resources (papers, GitHub projects, articles) enter the vault through `～草稿箱/`. On ingest, the AI writes two frontmatter fields: `source` (original link) and `prerequisites` (concepts needed before reading). A state machine tracks digestion progress: `unread → active → archived`.

**Design principle**: There is a clear boundary between external information and internal knowledge. Crossing it requires human digestion.

### Self-Documenting Structure

Each of the four core folders contains a `README.md` — open any folder and the first file tells you "what this is for, what belongs here, what doesn't." Neither AI nor human needs to read CLAUDE.md to understand folder purposes.

The design principle externalized: **the structure IS the documentation**.

---

## 3. Core Design Decisions

### 3.1 Eight Decisions

| Decision | Content | Rationale |
|------|------|---------|
| **Role separation** | VSCode = Engineer / Obsidian = Editor / Phone = PA | Each terminal does what it's best at. Separate CLAUDE.md files reduce token waste |
| **Content maturity layers** | Inbox → Processing → Delivery | Organized by knowledge digestion stage. One-way flow. External info enters via inbox |
| **Self-documenting structure** | Each folder has its own README | The structure IS the documentation. Open folder → know what belongs |
| **Three-layer memory** | global → project → user | `scope` field distinguishes layers. Project-level memory cleanable on completion |
| **Cross-system alignment** | `project` field + identical folder names | Pure naming convention. No API, no database. Mechanical matching, no AI guessing |
| **Skill pipeline** | 8 Skills, natural language trigger routing | CLAUDE.md = routing table (single source of truth). SKILL.md = execution manual |
| **Versioned evolution** | Semantic versioning + archival preservation | Minor fixes merged into current version. Old archives untouched + mapping tables |
| **Single source of truth** | Each type of info has one authority | Trigger routing → CLAUDE.md, execution steps → SKILL.md, cross-terminal rules → cross-terminal spec |

### 3.2 Design Density

> Design density: **not how many files or lines of code. It's how many problems one design decision solves.**

The `project` field example — a single line of YAML frontmatter:

```yaml
project: 01-poker-game-20260606
```

This one line solves four problems simultaneously:

| # | Function | Explanation |
|:--:|------|------|
| 1 | **Cross-terminal correlation** | VSCode ↔ Obsidian without API or database |
| 2 | **Auto-routing logs** | New project logs never write to wrong folder |
| 3 | **Garbage-collectable memory** | Project-level memory identifiable and cleanable |
| 4 | **Cross-session traceability** | Always know which project a log belongs to |

---

## 4. Three-Layer Memory Architecture

The core problem: **AI starts every conversation from zero — it doesn't know who you are, what you're doing, or how you prefer things.**

```
Rules for all projects      → global
Rules for a specific project → project
Personal preferences        → user
```

Each layer tagged with `scope` frontmatter field. Loaded with priority: global → project → user. MEMORY.md uses L1/L2/L3 three-tier index grouping (equivalent to directory isolation with zero migration cost). All 27 memory files have `scope` + `last-reviewed` root-level standardization.

### Structural Isomorphism

| System | Layering Model |
|------|---------|
| Kubernetes | Cluster → Namespace → Pod |
| Unix config | `/etc/` → `~/.config/` → `./.env` |
| Claude VObS | global → project → user |

Not a coincidence. All complex systems handling "global constraints + local specialization + personal preferences" converge on layering as the only stable solution.

---

## 5. Five-Layer Logic Depth

### 5.1 Framework

```
What users see ────→  Interaction logic (when to say what)
                      │
What system does ──→  Business logic (what rules execute)
                      │
How data flows ────→  Data logic (where info lives, how it moves)
                      │
What if it breaks ─→  Resilience logic (how to reconnect, how to fix)
                      │
How it grows ──────→  Evolution logic (how to record change, avoid decay)
```

### 5.2 Layer-by-layer Assessment

| Logic Layer | Rating | Notes |
|--------|:--:|------|
| Interaction | ⭐⭐⭐⭐ | 14 intent routes + fuzzy matching + 3-tier priority (v4.4: #3-#5 merged into unified entry). Natural language, not exact keywords |
| Business | ⭐⭐⭐⭐⭐ | 8 Skills with clear boundaries. Each pipeline has full execution steps, edge cases, error handling |
| Data | ⭐⭐⭐⭐⭐ | `project` field auto-validation; 3-layer memory with scope routing; dual-write session handoff; `arch-changes.jsonl` structured VSCode→Obsidian pipeline |
| **Resilience** | ⭐⭐⭐ | 8 pipeline sentinel coverage + degraded local cache + startup auto-check + Git safety net + B-class cron patrol. Auto-repair loop not yet closed |
| Evolution | ⭐⭐⭐⭐⭐ | Semantic versioning + archival preservation + `last-reviewed` staleness detection + periodic review reminders. Complete self-iteration loop |

---

## 6. Resilience Assessment

### 6.1 Four Capabilities

| Capability | Meaning | Level | Notes |
|------|------|:--:|------|
| Absorb | Can it not crash when things go wrong? | ⭐⭐⭐ | 8 pipelines with degradation paths. Vault inaccessible → local cache |
| Adapt | Can it switch paths after breaking? | ⭐⭐ | Degradation paths comprehensive, but post-switch decisions still manual |
| Recover | Can it fix itself after breaking? | ⭐⭐ | Sentinels + startup checks + Git rollback. Recovery still requires human trigger word |
| Learn | Can it not break the same way twice? | ⭐⭐⭐⭐ | Versioned history + `last-reviewed` detection + `system-watch` 5-monitor suite |

### 6.2 Open Feedback Loop Items

- **Active monitoring** ✅: `system-watch` A-class at startup + B-class cron patrol (every 2 days from v4.5), covering 7 checks: log silence, inbox backlog, broken links, memory redundancy, memory staleness, review reminders, cross-layer memory conflict detection
- **Auto-repair** 🟡: Low-risk problems could be auto-fixed + notified (v4.5 discussed, pending execution boundary decision)
- **Redundancy detection** ✅: Cross-layer memory conflict detection integrated into `system-watch` check #7 (from v4.5)
- **Staleness detection** ✅: `last-reviewed` version markers fully deployed across 27 memory files; dual-track detection via `system-watch` + `review-creator`
- **Token optimization** ✅: Estimation upgraded to input-turns×2000 + output-bytes×1.5; model routing 3-tier (LIGHT/STANDARD/HEAVY, from v4.5)

---

## 7. Project Lifecycle

Complete closed loop: Create → Develop → Complete → Post-completion Maintenance.

| Phase | VSCode Action | Obsidian Action |
|------|-------------|----------------|
| Create | Create project folder + source | Create log folder + first .md |
| Develop | Write code, say "提交" triggers commit+push+log | Receive log writes |
| Complete | Final verification + create summary | Archive project memory |
| Maintenance | Bug fixes, auto-append maintenance log | Maintenance records appended to summary |

Cross-terminal sync via `project` field + `arch-changes.jsonl` pipeline.

---

## 8. Evaluation Mode

Review reports can declare an `eval_mode` field in frontmatter:

| Mode | Meaning | Behavior |
|------|------|------|
| `framework-first` | System in framework-building phase, content output not the current focus | Content output tracked but not scored |
| (not declared) | Default full-dimension evaluation | All dimensions scored |

---

## 9. Design Constraints

Checklist before modifying system rules:

- [ ] **Single source of truth intact?** New content defined in only one place?
- [ ] **Both terminals need syncing?** Path references still correct after changes?
- [ ] **Archive principle**: Old versions untouched + mapping tables
- [ ] **Skill triggers**: Enumerated only in CLAUDE.md?
- [ ] **project field**: Values match on both terminals?
- [ ] **Cross-terminal trigger clarity**: "推送" ≠ "发布". Terminal ownership clear?

---

## 10. Known Limitations

| Limitation | Impact | Direction |
|------|------|---------|
| Resilience loop not closed | Pipeline breaks require human trigger words to recover. v4.5 added system-watch cron patrol for active detection, but auto-repair still pending execution boundary decision | Periodic auto-retry + escalation |
| Single-user design | No multi-user isolation or permissions | Not needed short-term; requires architecture upgrade for multi-user |
| Cross-terminal sync via naming | `project` field auto-corrected, but folder name mismatches still need manual fix | Extend auto-validation to folder names |
| Hard-coded paths | Manual placeholder replacement needed when switching machines | Consider config file for centralized path management |

---

> This system's design draws from cybernetics, resilience engineering, knowledge management, and human-computer interaction.
