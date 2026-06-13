# Claude VObS

> **A personal AI operating system, built by a non-programmer.**
> A set of rules that turns Claude Code into a three-terminal assistant — managing notes, writing code, and capturing ideas on the go. Pure Markdown, zero lines of hand-written code.

[![Version](https://img.shields.io/badge/version-v5.0-blue.svg)](https://github.com/JohnYoo528B/claude-vobs/releases)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

---

## 🆕 What's new in v5.0 (v4.6-v5.0 cumulative)

v5.0 is the settlement of the v4.x era — not the version with the most features, but the one that riveted every joint.

**Six "skip=bug" hard constraints:** Startup checks, post-operation procedures, change tracking, output registration, sentinel checks, and confirmation mechanisms — all six core rules upgraded from "AI should do this" to "AI has no choice."

**Four-layer architecture:** Judgment Engine → Self-Organization → External Connection → Practical Interaction. Independently converged with Hermes Agent (170K GitHub stars) — same answer to the same problem, opposite implementation paths.

**cc-connect self-healing:** Mobile reliability upgraded from "pray it doesn't break" to "auto-detect + auto-recover."

**New documents:** SETUP.md (step-by-step deployment) + FIRST-STEPS.md (3-minute onboarding).

**v4.6-v4.9 highlights:** MCP browser control + Hermes benchmarking (v4.9), PC→phone notification scripting (v4.8), mobile notification routing (v4.7), README management refactor (v4.6).
- **Token estimation upgrade + model routing**: Input-turns×2000 + output-bytes×1.5, 3-tier task classification (LIGHT/STANDARD/HEAVY)
- **Downstream propagation table**: 22 "change X → update Y" rules consolidated into one table
- **Quantitative judgment criteria**: Fatal conditions + high-weight conditions + magnitude classification, replacing AI subjective judgment
- **Feedback loop closure**: All four open items from the v4.4 audit moved from "identified" to "implemented"

---

## Why I built this

I'm a former film director with zero programming background — can't write a single line of code, even Git was taught to me by AI.

My day bounces between three terminals: **Obsidian** for notes and knowledge, **VSCode** for AI-assisted coding and quantitative analysis, and my **phone** for capturing ideas on the go. Every AI conversation on each terminal started from scratch — no idea who I was, what I was working on, or how I preferred things done.

Every existing solution had the same flaw: **they optimize one point, not the whole system**. Note tools handle notes. Code tools handle code. None of them say: "same memory, same AI, different roles based on context."

I wasn't looking for a new app. I needed a set of conventions layered on top of the tools I already used — giving the AI different behavioral roles across three terminals, one shared memory, and intent-based routing from natural language to the right execution pipeline.

That's Claude VObS. Built entirely through vibe coding — describing what I want in plain language, letting AI handle everything. From v1.0 to v4.5, every iteration solved a real pain point: first "the AI doesn't remember me," then "phone captures don't connect to Obsidian," then "I don't know what actually changed when I wrap up a session."

---

## How I use it daily

This system isn't theoretical — it's used every day. Here's the rhythm:

### ☀️ Morning
Open Obsidian → AI runs 8 health checks automatically → tells me what needs attention. Unread resources? Reminded. Inbox piling up? Flagged.

### 📱 During the day
- **Phone capture**: Idea hits me on the street → text "note: 3 reasons to buy ETH tomorrow" via messaging app → lands in Obsidian inbox automatically
- **VSCode coding**: Say "new project poker strategy backtest" → AI scaffolds the project, creates a dev log. The log file appears in Obsidian simultaneously
- **Obsidian search**: Say "find my supply chain bottleneck analysis" → AI searches both vault notes and chat memory concurrently, presents results from both

### 🌙 Evening
- **Clean inbox**: Say "clean inbox" → AI analyzes each item, proposes classification, I confirm
- **Update history**: Say "update history" → AI scans all changes made today, generates a version record
- **Remember**: Say "remember" → AI saves the session summary to the handoff file, auto-loaded next session

### 📅 Weekly
**Wrap up**: Say "wrap up" → AI uses quantitative criteria to determine change magnitude, picks the right path, updates history + user guide + publishes to GitHub if warranted

Each scene takes one sentence. No memorized commands — natural language works.

---

## What is this?

Claude VObS — **V** and **S** from VSCode, **Ob** from Obsidian — is a personal AI operating system. It doesn't build a new app — it layers conventions on top of Claude Code, VSCode, and Obsidian so the same AI switches roles automatically based on context:

```
VSCode (Engineer)       Obsidian (Editor)        Phone (PA)
Write code / run data    Manage notes / archive   Quick capture / search
     │                        │                      │
     └────────────────────────┼──────────────────────┘
                              │
          Three-Layer Memory (~/.claude/)
         global → project → user
                              │
               Eight Skills
  quick-capture │ vault-search │ dev-log-creator
  review-dispatcher → review-creator
         archive │ draft-cleanup │ system-watch
```

### Why not just use existing tools?

<table>
<tr><th>Solution</th><th>Notes</th><th>Code</th><th>Memory</th><th>Self-hosted</th></tr>
<tr><td>Obsidian alone</td><td>✅</td><td>❌</td><td>❌</td><td>✅</td></tr>
<tr><td>Obsidian + Copilot</td><td>✅</td><td>❌</td><td>❌</td><td>✅</td></tr>
<tr><td>Claude Code alone</td><td>❌</td><td>✅</td><td>❌</td><td>✅</td></tr>
<tr><td>Notion AI / Mem</td><td>✅</td><td>❌</td><td>⚠️ in-platform</td><td>❌ cloud</td></tr>
<tr><td><b>Claude VObS</b></td><td>✅</td><td>✅</td><td>✅ 3-layer</td><td>✅ local</td></tr>
</table>

The core difference: other tools do notes OR code. VObS does both, and the AI remembers who you are, what you're working on, and how you like things done — because the three-layer memory persists across sessions, projects, and terminals.

**The person who built this can't code.** Everything was done via vibe coding — describing what they want in plain language and letting AI handle the implementation.

---

## Obsidian Vault Architecture

This is the heart of the system — not "how to install," but "what's the thinking behind it." Installation takes 2 minutes. Understanding the design lets you see why it works the way it does.

### 1. Three-Layer Content Architecture

```
Inbox/  ────────→  Study & Logs/  ────────→  Output/
(entry)            (processing)              (delivery)
```

Content is organized by **maturity**, not file type.

| Layer | Folder | What goes there | How to tell |
|-------|--------|-----------------|-------------|
| Entry | `～草稿箱/` (inbox) | Fleeting ideas, external links, phone captures | Not yet processed |
| Processing | `1主题研究/` (deep study) | Judgments, methods, frameworks after deep reading | "Think → write" output |
| Processing | `2日志与笔记/` (logs) | Trade records, dev logs, daily notes | "Do → log" output |
| Delivery | `3工作与产出/` (output) | Finished deliverables | Done, shippable |

**Flow is one-way**: content enters through the inbox → gets processed → becomes either your own analysis (deep study) or a finished deliverable (output). External materials (papers, GitHub projects, articles) all enter through the inbox. The AI won't auto-file external content into study folders — crossing that boundary requires human digestion.

### 2. MOC Mechanism

```
Physical location ≠ browsing entry point
```

Traditional note tools have a problem: notes filed in folders → one note, one folder → cross-domain content gets lost.

VObS solution: **folders handle storage, MOCs handle discovery**.

| Concept | Role | Example |
|---------|------|---------|
| Folder | Physical storage location | `Finance/strategy-backtests/` |
| MOC (Map of Content) | Browsing entry point | `Finance.md`, wiki-linking to notes in that folder |

A note about "quantitative strategies" lives physically in `Finance/` but gets linked from both `Finance.md` and `Tech&AI.md`. Cross-domain notes don't get swallowed by a single category.

MOC files are maintained by both human and AI. When the AI writes a note, it automatically checks whether to add it to existing MOCs, asking the user when unsure. When a new topic accumulates 3+ notes, a new MOC is created.

### 3. Skill Routing System

Eight Skills = eight independent execution pipelines. CLAUDE.md handles routing, SKILL.md handles execution.

```
User says natural language
      │
      ▼
  CLAUDE.md intent routing table (14 routes)
      │
      ▼
  Skill execution pipeline (each Skill is a standalone file, ~/.claude/skills/)
      │
      ▼
  Results written back to vault or shown to user
```

| Skill | You say | What happens |
|-------|---------|--------------|
| `quick-capture` | "note: buy milk tomorrow" | Raw text saved to inbox |
| `vault-search` | "find my supply chain analysis" | Concurrent search: vault + chat memory |
| `dev-log-creator` | "poker project write log" | Dev log auto-generated in Obsidian |
| `review-dispatcher` | "wrap up" / "update history" | Determines review path → calls review-creator |
| `review-creator` | (called by dispatcher) | Scans changes → generates history → updates user guide |
| `archive` | "organize chat into notes" | Chat → structured note in vault |
| `draft-cleanup` | "clean inbox" | Analyzes each draft → classify/merge/delete |
| `system-watch` | (runs on startup) | 8 health checks + B-class cron patrol |

**No exact trigger words needed.** Natural expressions like "organize my chat" or "what changed recently" work — the AI matches intent, not keywords.

### 4. Cross-Terminal Collaboration

Three terminals (VSCode, Obsidian, phone) share one memory and one set of rules.

```
VSCode CLAUDE.md ──── Defines "engineer" behavior
Obsidian CLAUDE.md ── Defines "editor" behavior
Shared ~/.claude/ ─── Three-layer memory + eight Skills
```

The key mechanism — **`project` field**:

```yaml
project: 01-poker-backtest-20260606
```

One YAML line solves four problems:
1. **Cross-terminal correlation**: VSCode project ↔ Obsidian log folder aligned by identical names, no API needed
2. **Auto-routed logs**: dev-log-creator never writes to the wrong folder
3. **Cleanable memory**: Project-level memory auto-cleaned on project completion
4. **Traceable across sessions**: Any log is traceable to its project at any time

### 5. Three-Layer Memory

```
L1 · Global — System rules shared across all projects
L2 · Project — Single-project context, loaded on demand, auto-cleaned on completion
L3 · User — Response style, tone, communication habits
```

Memory isn't "dump everything together." Content with different lifecycles lives in different layers:
- L1 stays in context forever (system rules are never deleted)
- L2 auto-reminds for cleanup on project completion (unused project memory doesn't consume tokens)
- L3 is independent of projects and system (switching projects doesn't change response style)

### 6. Fault Tolerance

```
Pre-op Git snapshot → Execute → Failed?
                              ├─→ Wait 2s → Retry once → Success ✅
                              └─→ Fail → Write sentinel + degrade →
                                     Next startup: "N unfinished operations need recovery"
```

Four resilience layers:

| Layer | What it does | Example |
|-------|-------------|---------|
| **Absorb** | Auto-retry on every operation failure | Write fails → wait 2s → retry |
| **Adapt** | Auto-degrade when vault is unreachable | Content saved to local fallback directory, migrated when vault recovers |
| **Recover** | Failures are recorded, rollback available | Sentinel file records cause + recovery command, Git snapshot enables instant rollback |
| **Learn** | Startup self-checks | 8 checks on every launch (sentinels, fallback, todos, health, git, deps, memory conflicts, path alignment) |

All 7 operation pipelines (history records, phone capture, session handoff, dev logs, chat archiving, GitHub release) share this one fault-tolerance rule set. No per-pipeline error handling needed.

---

## Prerequisites

- **Claude Code**　✅ Required — The runtime. Needs Anthropic account + API key (paid) or Claude subscription. → [Docs](https://docs.anthropic.com/en/docs/claude-code)
- **VSCode**　✅ Required — Code terminal. → [Download](https://code.visualstudio.com/)
- **Obsidian**　✅ Required — Notes terminal. → [Download](https://obsidian.md/)
- **Python 3.12**　✅ Required — Default language for vibe-coding projects. → [Download](https://www.python.org/downloads/)
- **Git**　🟡 Recommended — Version control. → [Download](https://git-scm.com/downloads)

> 🧪 **Quick check**: Open Claude Code in VSCode and say something. If the AI responds, you're good.

---

## Quick Start

Two paths, pick one:

| | 🅰️ Auto-install | 🅱️ Manual |
|---|---|---|
| How | One prompt to Claude Code | Follow 7 steps |
| Time | ~2 minutes | 15–30 minutes |
| Best for | Don't want to fiddle | Want to understand every step |

### 🅰️ Auto-install

1. Download the repo (see below)
2. Open this folder in VSCode
3. Open Claude Code terminal (`Ctrl+Shift+P` → "Claude Code: Open")
4. Say:

> **"Initialize Claude VObS. My Obsidian vault is at `[your vault path]`. Please complete the full deployment: rename and place the two CLAUDE.md files into the code workspace root and vault root, copy all skills to ~/.claude/skills/, replace all placeholders globally, create the folder structure in my vault, and copy settings.example.json to settings.json. Tell me what you did at each step."**

Claude Code will read the files, understand the context, and handle path differences automatically. If anything goes wrong, it will explain why — no silent failures.

---

### 🅱️ Manual setup

Estimated time: 15–30 minutes.

### Step 1: Get the repo

```bash
git clone https://github.com/JohnYoo528B/claude-vobs.git
```
Or: GitHub page → Code → Download ZIP → extract.

The extracted folder is your **code workspace**.

---

### Step 2: Deploy CLAUDE.md

Rename and place the two files:

| Source file | Rename to | Place in |
|-------------|-----------|----------|
| `CLAUDE_VSCode.md` (from repo) | `CLAUDE.md` | Code workspace root |
| `CLAUDE_Obsidian.md` (from repo) | `CLAUDE.md` | Obsidian vault root |

> Obsidian vault root = the folder where all your notes live. No vault yet? Open Obsidian → Create new vault.

---

### Step 3: Install skills

Copy all `.md` files from the repo's `skills/` folder (8 total):

| OS | Path |
|----|------|
| Windows | `C:\Users\<username>\.claude\skills\` |
| macOS / Linux | `~/.claude/skills/` |

Create `.claude` or `skills` folders if they don't exist. Windows: `.claude` is hidden — type the full path into the address bar.

Final structure:
```
~/.claude/skills/
├─ quick-capture.md       ← Phone quick capture
├─ vault-search.md        ← Phone vault search (dual-source)
├─ dev-log-creator.md     ← Auto-generate dev logs
├─ review-dispatcher.md   ← Review unified entry (router)
├─ review-creator.md      ← System review execution engine
├─ archive.md             ← Chat → note archiving
├─ draft-cleanup.md       ← Inbox cleanup
└─ system-watch.md        ← System health monitoring
```

---

### Step 4: Replace placeholders

Search for `<你的` across all files and replace:

- **`<你的代码工作区路径>`** → full path to your code workspace, e.g. `D:\MyCode\claude-vobs`
- **`<你的 Obsidian vault 路径>`** → full path to your Obsidian vault, e.g. `E:\Notes\MyVault`

In VSCode: `Ctrl+Shift+H` for global find-and-replace.

---

### Step 5: Create Obsidian folder structure

Create these folders in your vault. Each comes with a built-in README explaining what belongs there:

```
your-vault\
├─ CLAUDE.md                     ← Renamed from CLAUDE_Obsidian.md
├─ _系统管理\                    ← System management (specs, history, health)
├─ ～草稿箱\                     ← Unified inbox (ideas, external resources, phone captures)
│   ├─ README.md                 ← Built-in: explains what goes here
│   └─ 手机暂存\
├─ 1主题研究\                    ← Deep reading outputs (think → write)
│   └─ README.md                 ← Built-in: judgment criteria + MOC usage
├─ 2日志与笔记\                  ← Process records (do → log)
│   └─ README.md                 ← Built-in: difference from 1主题研究
└─ 3工作与产出\                  ← Deliverables (done → ship)
    └─ README.md                 ← Built-in: when to put things here
```

> The four-layer architecture: inbox (entry) → deep study / logs (processing) → deliverables (shipping). Content flows one way, each layer has its own README.

---

### Step 6: Configure settings.json

1. Copy the repo's `settings.example.json` to `C:\Users\<username>\.claude\` (Mac: `~/.claude/`)
2. Rename to `settings.json`
3. Replace the API key:

```json
{
  "env": {
    "ANTHROPIC_API_KEY": "sk-ant-api03-your-key-here"
  }
}
```

> Get an API key at [console.anthropic.com](https://console.anthropic.com/). Claude subscription users can skip this.

---

### Step 7: Verify

VSCode → File → Open Folder (select code workspace) → `Ctrl+Shift+P` → "Claude Code: Open" → say:

**"新建项目 测试项目"**

If the AI starts creating folders, the system is working.

---

## Phone terminal (optional)

The phone terminal does two things: capture quick thoughts to your Obsidian inbox, and search your vault on the go.

The system already has the phone logic built in — `quick-capture` and `vault-search` skills + trigger routing. You just need a bridge from your phone to Claude Code.

<table>
<tr><th>Path</th><th>Requires</th><th>Best for</th></tr>
<tr><td>🅰️ <b>cc-connect + messaging app</b></td><td>Node.js, always-on PC, messaging bot</td><td>Full power, chat-like experience</td></tr>
<tr><td>🅱️ <b>Claude mobile app</b></td><td>Claude subscription (Pro/Max)</td><td>Simplest, zero config</td></tr>
<tr><td>🅲 <b>Skip for now</b></td><td>Nothing</td><td>Doesn't affect core VSCode + Obsidian functionality</td></tr>
</table>

Detailed steps → [docs/phone-setup-en.md](docs/phone-setup-en.md)

---

## Repository structure

```
.
├── README.md                ← You are here
├── README_zh.md             ← Chinese version
├── LICENSE                  ← MIT
├── settings.example.json    ← Settings.json template
├── CLAUDE_VSCode.md         ← VSCode terminal rules
├── CLAUDE_Obsidian.md       ← Obsidian terminal rules
├── skills/
│   ├── quick-capture.md     ← Phone quick capture
│   ├── vault-search.md      ← Phone vault search (dual-source)
│   ├── dev-log-creator.md   ← Auto-generate dev logs
│   ├── review-dispatcher.md ← Review unified entry (router)
│   ├── review-creator.md    ← System review execution engine
│   ├── archive.md           ← Chat → note archiving
│   ├── draft-cleanup.md     ← Inbox cleanup
│   └── system-watch.md      ← System health monitoring
├── DESIGN.md                ← System design principles
├── DESIGN_zh.md             ← System design (Chinese)
├── docs/
│   ├── phone-setup.md       ← Phone setup guide (Chinese)
│   ├── phone-setup-en.md    ← Phone setup guide (English)
│   ├── 跨端共享规范.md       ← Cross-terminal spec
│   ├── 下游传播表.md         ← Change propagation rules
│   ├── 量化判断标准.md       ← Close-session decision engine
│   ├── memory-frontmatter-标准.md ← Memory field standard
│   └── 模型路由规则.md       ← 3-tier model routing
├── templates/
│   ├── 通用笔记模板.md       ← Note template
│   └── MOC模板.md           ← Topic index (MOC) template
├── ～草稿箱/                 ← Unified inbox (with README)
│   └── README.md
├── 1主题研究/                ← Deep reading (with README + MOC template)
│   ├── README.md
│   └── MOC模板.md
├── 2日志与笔记/              ← Process records (with README)
│   └── README.md
└── 3工作与产出/              ← Deliverables (with README)
    └── README.md
```

---

## FAQ

### What can I do once set up?

- **Write code**: In VSCode, say "new project my-first-project"
- **Take notes**: In Obsidian, say "organize today's chat into notes"
- **Review**: Say "update history" to generate a system review
- **Phone capture** (with phone setup): Text "note: buy milk tomorrow"
- **Clean inbox**: Say "clean inbox"
- **Search**: Say "find xxx" — searches both vault and chat memory simultaneously
- **GitHub release**: Say "publish GitHub release"

### What's the difference between "note down" and "remember"?

"Note down" = save raw text to inbox (phone capture). "Remember" = write session summary to handoff file (next session auto-loads). Completely different purposes.

### What if something breaks?

All operations have unified error handling: fail → wait 2s → retry once → write sentinel + notify. On startup, AI auto-checks sentinels and fallback storage. Say "recover unfinished operations" to retry. Git safety net snapshots before every write — instant rollback available.

### How do I complete a project?

In VSCode, say "project complete". AI runs verification → creates summary → marks complete → updates memory. Post-completion fixes auto-append to maintenance log. See [docs/跨端共享规范.md](docs/跨端共享规范.md).

### How do I publish to GitHub?

In Obsidian, say "publish GitHub release". The 0.5 version threshold is the AI's reminder line — not your restriction. You release when you want.

### Stuck?

1. Check all prerequisites are met
2. Confirm CLAUDE.md files are in the right places and renamed
3. Search for `<你的` — any placeholders left?
4. Verify API key in settings.json
5. Still stuck? → [GitHub Issues](https://github.com/JohnYoo528B/claude-vobs/issues)

---

## Key design decisions

- **Three terminals**: Engineer (VSCode) · Editor (Obsidian) · Secretary (Phone), each does what it's best at
- **Three-layer content**: Organized by maturity — inbox (entry) → processing → delivery, one-way flow
- **MOC mechanism**: Physical storage ≠ browsing entry, folders handle location, MOCs handle discovery
- **Skill routing**: CLAUDE.md routes, SKILL.md executes, natural language intent matching
- **Three-layer memory**: L1 global → L2 project → L3 user, tiered by lifecycle, auto-clean on project completion
- **Cross-terminal alignment**: `project` field solves correlation + log routing + memory cleanup + traceability in one line
- **Four-layer resilience**: Absorb (retry) → Adapt (degrade) → Recover (sentinel + git rollback) → Learn (startup self-check)
- **Versioned evolution**: Major version +1, feature +0.1, old versions untouched + mapping tables

More in [DESIGN.md](DESIGN.md) — each item above has expanded reasoning, boundary conditions, and trade-off analysis.

---

## Who is this for?

- **Non-coders** who want an AI-powered personal knowledge + code system
- **AI tinkerers** looking for a multi-terminal AI reference architecture
- **System design enthusiasts** curious about non-engineer-designed architecture

---

## Inspiration

Every new AI chat starts from zero — it knows nothing about you. The solution wasn't a new app. It was a set of conventions on top of Claude Code, Obsidian, and VSCode. Same AI, different contexts, different roles, one memory.

---

## License

MIT — [LICENSE](LICENSE)

---

*Built by [@JohnYoo528B](https://github.com/JohnYoo528B) — former film director, zero programming background, using only natural language and Claude Code.*
