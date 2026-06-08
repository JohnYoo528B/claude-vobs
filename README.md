# Claude VObS

> **A personal AI operating system built by a non-programmer.**
> Self-hosted alternative to AI knowledge management tools — powered by Claude Code, Obsidian, and zero coding experience.

[![Version](https://img.shields.io/badge/version-v3.5-blue.svg)](https://github.com/JohnYoo528B/claude-vobs/releases)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

---

## What is this?

Claude VObS — **V** from VSCode, **Ob** from Obsidian, **S** from the merged 's' of both — is a personal AI operating system: a set of rules, workflows, and conventions that turn Claude Code into a three-terminal personal assistant:

```
┌──────────────────────────────────────────────────┐
│               THREE TERMINALS                     │
│                                                   │
│  VSCode (Engineer)  Obsidian (Editor)  Phone (PA) │
│  Write code          Manage notes       Quick I/O │
│         │                │                │       │
│         └────────────────┼────────────────┘       │
│                          │                        │
│           THREE-LAYER MEMORY ( ~/.claude/ )       │
│         global ──→ project ──→ user              │
│                          │                        │
│               FOUR SKILLS                          │
│  quick-capture │ vault-search │ dev-log-creator   │
│                         │ review-creator          │
└──────────────────────────────────────────────────┘
```

Each terminal has its own personality via `CLAUDE.md`. They share a three-layer memory system (global → project → user preferences) and four skills that route natural language triggers to automated workflows. No API integration, no database — everything is plain Markdown files synchronized through naming conventions.

**The person who built this can't code.** Everything was done via vibe coding — describing what they want in plain language and letting AI handle the implementation.

---

## Quick Start

1. **Copy the CLAUDE.md files** — Drop [CLAUDE_Obsidian.md](CLAUDE_Obsidian.md) into your Obsidian vault root (rename to `CLAUDE.md`) and [CLAUDE_VSCode.md](CLAUDE_VSCode.md) into your code workspace root (rename to `CLAUDE.md`).
2. **Install the four skills** — Copy the [skills/](skills/) folder into `~/.claude/skills/`.
3. **Replace the placeholders** — Search for `<你的` across all files and replace with your actual paths.
4. **Start talking** — Say "新建项目 我的第一个项目" in VSCode Claude Code, or "记一下 今天想到的点子" on your phone.

> **Phone terminal**: Any channel that can reach Claude Code — Claude mobile app, web chat, or third-party bridges — serves as the third terminal. The `quick-capture` and `vault-search` skills are purpose-built for mobile use.

---

## Repository Structure

```
.
├── README.md                  ← You are here (English)
├── README_zh.md               ← Chinese version
├── LICENSE                    ← MIT
├── .gitignore
├── CLAUDE_Obsidian.md         ← Obsidian terminal rules (desensitized)
├── CLAUDE_VSCode.md           ← VSCode terminal rules (desensitized)
├── skills/
│   ├── quick-capture.md       ← Save fleeting thoughts from phone
│   ├── vault-search.md        ← Search your vault from phone
│   ├── dev-log-creator.md     ← Auto-generate dev logs for projects
│   └── review-creator.md      ← Generate system review reports
├── DESIGN.md                   ← System design principles (English)
├── DESIGN_zh.md                ← System design principles (Chinese)
└── 模板/                       ← Templates
    └── 通用笔记模板.md           ← Universal note template
```

---

## Key Design Decisions

| Decision | Why |
|----------|-----|
| **Three terminals, not one app** | Each terminal does what it's best at. No bloated all-in-one UI. |
| **Three-layer memory** | Global rules → Project rules → User preferences. Project memories auto-clean when a project completes. |
| **Plain Markdown + naming conventions** | No database, no API between terminals. `project` frontmatter field + identical folder names = perfect cross-terminal alignment. |
| **Four skills, not forty** | Each skill is a self-contained pipeline triggered by natural language. CLAUDE.md is the routing table; SKILL.md is the execution manual. |
| **Versioned evolution** | Every architecture change is recorded. Old versions stay untouched; a mapping table at the end bridges old and new concepts. |

Read more in [DESIGN.md](DESIGN.md).

---

## Who is this for?

- **Zero-coding individuals** who want an AI-powered personal knowledge management system
- **AI tinkerers** looking for a reference architecture for multi-terminal AI setups
- **System design enthusiasts** curious about what happens when a film director designs software architecture

---

## Inspiration

This system was born from a simple frustration: every time you open a new AI chat, it knows nothing about you. You have to re-explain who you are, what you're working on, and how you like things done.

The solution wasn't a new app — it was a set of conventions layered on top of existing tools (Claude Code, Obsidian, VSCode). The insight was that the same AI can play different roles in different contexts, as long as each context has clear rules.

What emerged is a personal AI operating system — designed not by an engineer, but by someone who just wanted their AI to remember.

---

## License

MIT — see [LICENSE](LICENSE).

---

*Built by [@JohnYoo528B](https://github.com/JohnYoo528B) — a former film director with zero programming background, using only natural language and Claude Code.*

*Built with Claude Code, Obsidian, and zero lines of hand-written code.*
