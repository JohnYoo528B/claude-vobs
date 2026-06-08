---
date: 2026-06-08
type: spec
status: done
tags:
  - system-design
  - architecture
---

# System Design Principles

> A comprehensive architectural overview of Claude VObS (Claude + VSCode + Obsidian + System). Read this before modifying system rules.

---

## 1. System Overview

```
┌──────────────────────────────────────────────────┐
│              THREE TERMINALS                       │
│                                                   │
│  VSCode (Engineer)  Obsidian (Editor)  Phone (PA) │
│  Write code          Manage notes       Quick I/O │
│  CLAUDE.md           CLAUDE.md          cc-connect│
│  workspace\          vault\             → skills  │
│         │                │                │       │
│         └────────────────┼────────────────┘       │
│                          │                        │
│          THREE-LAYER MEMORY ( ~/.claude/ )        │
│         global ──→ project ──→ user              │
│     (all projects)  (per project)  (preferences)  │
│                          │                        │
│               FOUR SKILLS                          │
│  quick-capture │ vault-search │ dev-log-creator   │
│                         │ review-creator          │
└──────────────────────────────────────────────────┘
```

**Data flow**:

- VSCode writes code → `dev-log-creator` → Obsidian dev log
- Obsidian changes architecture → `review-creator` → system history
- Phone sends message → `quick-capture` / `vault-search` → Obsidian vault
- Cross-system alignment via `project` frontmatter field + identical folder names

---

## 2. Core Design Decisions

### 2.1 Six Decisions

| Decision | Approach | Rationale |
|----------|----------|-----------|
| **Role differentiation** | VSCode = Engineer / Obsidian = Editor / Phone = PA | Each terminal does what it does best. CLAUDE.md files don't include each other, minimizing token usage |
| **Three-layer memory** | global → project → user | `scope` field distinguishes layers. Project-level memories are garbage-collected when projects complete |
| **Cross-system alignment** | `project` field + identical folder names | Pure naming convention — no API, no database. Mechanical matching, not AI guessing |
| **Skill pipeline** | 4 skills, natural-language trigger routing | CLAUDE.md is the routing table (single source of truth); SKILL.md is the execution manual. Triggers enumerated in one place only |
| **Versioned evolution** | Semantic versioning + immutable history | Minor patches merge into current version. Old archives untouched; mapping tables bridge old and new concepts |
| **Single source of authority** | One authority per information type | Trigger routing → CLAUDE.md. Procedures → SKILL.md. Specs → `_全局规范/`. No duplication |

### 2.2 Design Density

> Design density: **not how many files or lines of code. How many problems does each design decision solve?**

Take the `project` field — a single line of YAML frontmatter:

```yaml
project: 01-texas-holdem-20260606
```

One line solves four problems simultaneously:

| # | Function | Description |
|:--:|----------|-------------|
| 1 | **Cross-terminal association** | Links VSCode and Obsidian without API or database |
| 2 | **Auto-routing logs** | New project logs never land in the wrong folder |
| 3 | **Garbage-collectable memory** | Project-level memories become identifiable and cleanable |
| 4 | **Cross-session traceability** | Any log entry is traceable to its project at any time |

Low design density: write sync scripts, set up databases, maintain lookup tables — multiple mechanisms for one problem. High design density pursues "one mechanism, multiple outcomes."

---

## 3. Three-Layer Memory Architecture

### 3.1 Layering Logic

The core problem three-layer memory solves: **every AI conversation starts from zero — it doesn't know who you are, what you're working on, or how you like things done.**

The solution:

```
Rules for all projects     → global
Rules for a specific project → project
Personal habits and preferences → user
```

Each layer is marked by a `scope` frontmatter field. Loading follows global → project → user priority stacking.

### 3.2 Isomorphism with Industrial Systems

The three-layer memory mirrors established layered architectures:

| System | Layering Model |
|--------|---------------|
| Kubernetes | Cluster → Namespace → Pod |
| Unix configuration | `/etc/` → `~/.config/` → `./.env` |
| Claude VObS | global → project → user |

This is not coincidence. Any complex system that must handle "global constraints + local specialization + personal preference" converges on layering as the only stable solution. Claude VObS's three-layer model is an independent implementation of this universal pattern in the personal AI domain.

---

## 4. Five-Layer Logic Framework

### 4.1 The Framework

A complete system's logic extends beyond "how a person talks to it." It decomposes into five layers:

```
What users see ────→  Interaction logic (when to say what, what triggers what)
                     │
What the system does →  Business logic (what rules execute after triggers)
                     │
How data moves ────→  Data logic (where info lives, how it flows, who reads/writes)
                     │
What happens on failure → Fault logic (how to catch breaks, how to repair)
                     │
How the system grows → Evolution logic (how to record change, how to prevent decay)
```

The first three layers handle "normal operation"; the last two handle "failure and growth." Most personal systems only design the first three.

### 4.2 Claude VObS Layer-by-Layer Assessment

| Layer | Score | Assessment |
|-------|:-----:|------------|
| Interaction | ⭐⭐⭐⭐ | Trigger words + intent matching table + boundary handling form a three-tier routing system. Lacks fuzzy semantic matching and proactive awareness |
| Business | ⭐⭐⭐⭐⭐ | Four skills with clean boundaries; each pipeline includes complete procedures, edge cases, and error handling |
| Data | ⭐⭐⭐⭐⭐ | `project` field auto-validated and auto-corrected before every write (Step 0 mandatory check). Three-layer memory with scope-based management. Session handoff via dual-write (temporary + permanent) for redundancy |
| **Fault** | ⭐⭐⭐ | 5 pipelines with full sentinel tracking + degraded local cache + startup auto-detection. But the auto-repair loop remains open — recovery requires manual trigger |
| Evolution | ⭐⭐⭐⭐⭐ | Semantic versioning + immutable archives + mapping tables + self-review checklist + periodic reminders form a complete self-iteration loop |

### 4.3 Cross-Disciplinary Position

Claude VObS doesn't belong to any single existing discipline. It spans four domains simultaneously:

```
        Systems Architecture → Five-layer logic design
                │
        Cybernetics    → Sensors + feedback loops
                │
        Knowledge Management → Three-layer memory + cross-system alignment
                │
        Human-Computer Interaction → Three terminals, three collaboration modes
```

The closest academic description: **Cybernetic Architecture for Human-AI Collaborative Systems**.

---

## 5. Resilience Assessment

### 5.1 Four Capabilities of Resilience Engineering

| Capability | Meaning | Level | Notes |
|------------|---------|:-----:|-------|
| Absorb | Can it take a hit without crashing? | ⭐⭐⭐ | All 5 pipelines have degradation paths. Vault unavailable → auto-switch to local cache, zero data loss |
| Adapt | Can it switch paths when something breaks? | ⭐⭐ | Degradation paths are comprehensive, but switching still requires human decision |
| Recover | Can it fix itself after a crash? | ⭐⭐ | Sentinel files fully track breakpoints + startup auto-detection, but recovery still requires manual trigger |
| Learn | Can it avoid repeating failures? | ⭐⭐⭐⭐ | Versioned history + root cause analysis template + self-review checklist. Rules upgrade after each failure |

**Core gap**: The sensor network is complete (startup health checks + review-creator periodic scans + sentinel auto-detection), but the feedback loop is missing its final link — automatic retry / auto-repair. This is the bottleneck between "passive response" and "self-correction."

### 5.2 Supplementary Review Frameworks

| Framework | Core Question | Claude VObS Status |
|-----------|---------------|:---:|
| **Cybernetics** | Can the system self-correct? | Sensor network complete; missing automatic feedback actuator |
| **Resilience Engineering** | How does it perform under stress? | Degradation paths comprehensive (Absorb ⭐⭐⭐); self-healing not yet achieved (Recover ⭐⭐) |
| **Second-Order Thinking** | Is the review mechanism biased? | AI self-review has inherent blind spots |
| **Minimalism** | How much can be removed and still work? | Irreducible core = CLAUDE.md + project field + three-layer memory |

---

## 6. The Three Elements of an AI System

A true AI system — not merely "a tool that uses AI" — must satisfy three conditions:

| Element | Meaning | Claude VObS |
|---------|---------|:-----------:|
| **Memory** | Can remember, layer, and retrieve | ✅ Three-layer memory + conversation archiving |
| **Boundaries** | Knows what it can and cannot do | ✅ Three-terminal behavioral constitution (CLAUDE.md constraints) |
| **Feedback** | Knows whether it did the right thing | ⚠️ Sensors present (review-creator); missing automatic loop |

---

## 7. Design Constraints

Checklist before modifying system rules:

- [ ] **Single source of authority intact?** Does new content appear exactly once in CLAUDE.md and once in SKILL.md? Never a third time
- [ ] **Cross-terminal sync needed?** If Obsidian-side specs changed, are VSCode-side reference paths still correct?
- [ ] **History archive principle**: Old version files untouched; mapping table at the end bridges old and new concepts
- [ ] **Skill triggers**: Are triggers enumerated only in CLAUDE.md? SKILL.md contains usage descriptions only
- [ ] **project field**: Do both terminals share the same `project` value for new projects?
- [ ] **Global spec docs**: Are new specs placed in `_全局规范/` rather than scattered?

---

## 8. Known Limitations

| Limitation | Impact | Direction |
|------------|--------|-----------|
| Open fault loop | Pipeline breaks require manual "recover" trigger; no self-healing | Scheduled auto-retry + failure escalation |
| Single-user design | No multi-user isolation or permission model | Not needed short-term; requires architectural upgrade for multi-user |
| Naming convention dependency | `project` field is auto-corrected (Step 0 check), but folder name mismatches still need manual intervention | Extend auto-validation to folder name level |
| No fuzzy intent matching | Triggers rely on keyword matching; similar expressions may route incorrectly | Introduce semantic intent classification |
| Hardcoded paths | Switching machines or users requires manual placeholder replacement | Consider centralized path configuration |

---

> This system's design draws on the intersection of systems architecture, cybernetics, knowledge management, and human-computer interaction.
