# Claude VObS

> **A personal AI operating system built by a non-programmer.**
> A set of rules that turns Claude Code into a three-terminal assistant — managing notes, writing code, and capturing ideas on the go. Pure Markdown, zero lines of hand-written code.

[![Version](https://img.shields.io/badge/version-v4.5-blue.svg)](https://github.com/JohnYoo528B/claude-vobs/releases)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

---

## 🆕 What's new in v4.5 (v4.4 + v4.5 cumulative)

- **8 Skills**: Added review-dispatcher — four review entry points merged into one unified engine
- **system-watch cron patrol**: A-class startup checks + B-class cron patrol every 2 days
- **3-layer memory standardization**: 27 memory files with full frontmatter coverage, L1/L2/L3 tiered index
- **Token estimation upgrade + model routing**: Input-turns×2000 + output-bytes×1.5, 3-tier task classification (LIGHT/STANDARD/HEAVY)
- **Downstream propagation table**: 22 "change X → update Y" rules consolidated into one table
- **Quantitative judgment criteria**: Fatal conditions + high-weight conditions + magnitude classification, replacing AI subjective judgment
- **Feedback loop closure**: All four open items from the v4.4 audit moved from "identified" to "implemented"

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

### What can it do?

It's not "a note tool + a code tool glued together." It's **one system, one memory, switching roles automatically**:

```
Text & notes ────────→ Code & analysis ────────→ Vibe-coding
Knowledge management    Data processing           Build full projects
Journal · Inbox        Quantitative analysis      Games · Tools
Reading notes          Automation scripts         Web apps
```

From "note down: buy milk tomorrow" to "build me a stock backtesting system" — same memory, same AI.

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
<tr><td>🅰️ <b>cc-connect + WeChat</b></td><td>Node.js, always-on PC, WeChat ClawBot</td><td>Full power, chat-like experience</td></tr>
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

- **Write code**: In VSCode, say "新建项目 my-first-project"
- **Take notes**: In Obsidian, say "organize today's chat into notes"
- **Review**: Say "更新历史记录" to generate a system review
- **Phone capture** (with phone setup): Text "note down: buy milk tomorrow"
- **Clean inbox**: Say "整理草稿箱"
- **Search**: Say "找一下 xxx" — searches both vault and chat memory simultaneously
- **GitHub release**: Say "发布 GitHub 新版本"

### What's the difference between "存一下" (quick save) and "记忆一下" (remember)?

"存" = save raw text to inbox (phone quick capture). "记忆" = write a session summary to the handoff file (next session auto-loads context). Completely different purposes.

### Do I need exact trigger words?

No. Natural language like "organize my chat" or "what changed recently" works fine. The AI matches intent, not keywords. It asks if it's unsure.

### What are the README files in each folder?

The self-documenting system — open any core folder and the first file tells you "what this is for, what belongs here, what doesn't." Four READMEs (inbox, deep study, logs, deliverables) form a complete content flow guide. Neither AI nor human needs to read CLAUDE.md to understand folder purposes.

### What if something breaks?

All operations have unified error handling: fail → wait 2s → retry once → write sentinel + notify. On startup, AI auto-checks sentinels and fallback storage. Say "恢复未完成操作" to retry failed operations. Git safety net snapshots before every write — instant rollback available.

### How do I complete a project?

In VSCode, say "项目完结". AI runs verification → creates summary → marks complete → updates memory. Post-completion fixes (bug patches) auto-append to maintenance log. See [docs/跨端共享规范.md](docs/跨端共享规范.md).

### How do I publish to GitHub?

In Obsidian, say "发布 GitHub 新版本". The 0.5 version threshold is the AI's reminder line (don't nag you) — not your restriction. You release when you want.

### Stuck?

1. Check all prerequisites are met
2. Confirm CLAUDE.md files are in the right places and renamed
3. Search for `<你的` — any placeholders left?
4. Verify API key in settings.json
5. Still stuck? → [GitHub Issues](https://github.com/JohnYoo528B/claude-vobs/issues)

---

## Key design decisions

- **Three terminals**: Each does what it's best at — Engineer (VSCode), Editor (Obsidian), Secretary (Phone)
- **Three-layer content architecture**: Inbox (entry) → Deep study / Logs (processing) → Deliverables (shipping), organized by content maturity
- **Self-documenting structure**: Each folder has its own README — the structure IS the documentation
- **Three-layer memory**: global → project → user. Auto-clean on project completion
- **Plain Markdown + conventions**: No database, no API. `project` field + identical folder names
- **Seven skills**: Pluggable pipelines, natural language triggers. CLAUDE.md routes, SKILL.md executes
- **Versioned evolution**: Major version (arch restructure) +1, feature +0.1, old versions untouched + mapping tables
- **Four-layer resilience**: Absorb (retry) → Adapt (degrade) → Recover (sentinel + git rollback) → Learn (startup self-check)

More in [DESIGN.md](DESIGN.md).

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
