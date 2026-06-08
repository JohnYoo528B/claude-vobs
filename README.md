# Claude VObS

> **A personal AI operating system built by a non-programmer.**
> A set of rules that turns Claude Code into a three-terminal assistant — managing notes, writing code, and capturing ideas on the go. Pure Markdown, zero lines of hand-written code.

[![Version](https://img.shields.io/badge/version-v3.5-blue.svg)](https://github.com/JohnYoo528B/claude-vobs/releases)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

---

## What is this?

Claude VObS (**V**SCode + **Ob**sidian + **S**ystem) is a personal AI operating system. It doesn't build a new app — it layers conventions on top of Claude Code, VSCode, and Obsidian so the same AI switches roles automatically based on context:

```
VSCode (Engineer)       Obsidian (Editor)        Phone (PA)
Write code / run data    Manage notes / archive   Quick capture / search
     │                        │                      │
     └────────────────────────┼──────────────────────┘
                              │
          Three-Layer Memory (~/.claude/)
         global → project → user
                              │
               Four Skills
  quick-capture │ vault-search │ dev-log-creator │ review-creator
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

Copy the `.md` files from the repo's `skills/` folder (3 core + 1 advanced):

| OS | Path |
|----|------|
| Windows | `C:\Users\<username>\.claude\skills\` |
| macOS / Linux | `~/.claude/skills/` |

Create `.claude` or `skills` folders if they don't exist. Windows: `.claude` is hidden — type the full path into the address bar.

Final structure:
```
~/.claude/skills/
├─ quick-capture.md       ← Core: phone quick capture
├─ vault-search.md        ← Core: phone vault search
├─ dev-log-creator.md     ← Core: auto-generate dev logs
└─ review-creator.md      ← Advanced: system review (can skip for now)
```

---

### Step 4: Replace placeholders

Search for `<你的` across all files and replace:

- **`<你的代码工作区路径>`** → full path to your code workspace, e.g. `D:\MyCode\claude-vobs`
- **`<你的 Obsidian vault 路径>`** → full path to your Obsidian vault, e.g. `E:\Notes\MyVault`

In VSCode: `Ctrl+Shift+H` for global find-and-replace.

---

### Step 5: Create Obsidian folder structure

Create these folders in your vault (at minimum the first three):

```
your-vault\
├─ _系统管理\
├─ 00草稿箱\
│   └─ 手机暂存\          ← for phone capture, can defer
├─ 0VibeCoding开发日志\
│   └─ _全局规范\
├─ 1学习笔记\
├─ 2工作文档\
├─ 3投资记录\
├─ 4创作灵感\
├─ 5其他知识\
└─ 模板\
```

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

> Get an API key at [console.anthropic.com](https://console.anthropic.com/). Claude subscription users can skip this — the subscription quota works directly.

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
│   ├── quick-capture.md     ← Core skill: phone capture
│   ├── vault-search.md      ← Core skill: phone search
│   ├── dev-log-creator.md   ← Core skill: dev logs
│   └── review-creator.md    ← Advanced skill: system review
├── DESIGN.md                ← System design principles
├── DESIGN_zh.md             ← System design (Chinese)
├── docs/
│   ├── phone-setup.md       ← Phone setup guide (Chinese)
│   └── phone-setup-en.md    ← Phone setup guide (English)
└── 模板/
    └── 通用笔记模板.md
```

---

## FAQ

### Where is `~/.claude/`?

| Windows | Mac | Linux |
|---------|-----|-------|
| `C:\Users\<username>\.claude\` | `/Users/<username>/.claude/` | `/home/<username>/.claude/` |

Not sure of your username? Type `%USERPROFILE%` into File Explorer's address bar.

### How do I get an API key?

[console.anthropic.com](https://console.anthropic.com/) → sign up → add funds ($5 min) → API Keys → create. Claude subscription users don't need a separate key.

### Do I really need Python and Git?

Yes, both. Python is the default language for vibe-coding — even if you don't write code today, your projects will need it. Git is the foundation for version control that VSCode integrates with.

### What can I do once set up?

- **Write code**: In VSCode, say "新建项目 my-first-project"
- **Take notes**: In Obsidian, say "organize today's chat into notes"
- **Review**: Say "更新历史记录" to generate a system review
- **Phone capture** (with phone setup): Text "note down: buy milk tomorrow"

### Stuck?

1. Check all prerequisites are met
2. Confirm CLAUDE.md files are in the right places and renamed
3. Search for `<你的` — any placeholders left?
4. Verify API key in settings.json
5. Still stuck? → [GitHub Issues](https://github.com/JohnYoo528B/claude-vobs/issues)

---

## Key design decisions

- **Three terminals**: Each does what it's best at. No bloated all-in-one.
- **Three-layer memory**: global → project → user. Auto-clean on project completion.
- **Plain Markdown + conventions**: No database, no API. `project` field + identical folder names.
- **Four skills**: Self-contained pipelines, natural language triggers. CLAUDE.md routes, SKILL.md executes.
- **Versioned evolution**: Old versions untouched; mapping tables bridge concepts.

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
