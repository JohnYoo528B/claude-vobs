# Phone Terminal Setup Guide

## What you can do from your phone

Once set up, you can:

- **Quick capture**: Text "note down: meeting tomorrow at 3" → auto-saved to Obsidian `00草稿箱/手机暂存/`
- **Search vault**: Text "find: texas holdem" → returns matching notes from your vault
- **Batch save**: Text "save all of the above" → captures all unreviewed messages

> Triggers don't need to be exact. The system routes by intent, not keyword matching.

---

## What you already have

Claude VObS includes phone-side logic out of the box. Nothing to write from scratch:

| Built-in component | What it does |
|--------------------|--------------|
| `skills/quick-capture.md` | Quick capture workflow (save verbatim, group by session) |
| `skills/vault-search.md` | Vault search workflow (Grep, graded response by result count) |
| Trigger routing in `CLAUDE_Obsidian.md` | Recognizes natural language like "note down / find / search" and routes to the right skill |

You only need a bridge from your phone to Claude Code. Everything else is handled.

---

## Three paths — pick yours

### 🅰️ cc-connect + WeChat (full power)

**Result**: Use Claude Code from WeChat like texting a friend. "Note down: X" saves to Obsidian. "Find: Y" searches your vault.

**Prerequisites**:

| Requirement | How to check |
|-------------|--------------|
| Node.js installed | Run `node --version` in terminal |
| Always-on computer | No sleep/hibernate — cc-connect needs continuous runtime |
| WeChat + ClawBot plugin | WeChat ≥ 8.0.70. iOS users can scan QR to authorize (Android depends on rollout) |

**Setup**:

```bash
# 1. Install cc-connect
npm install -g cc-connect@beta

# 2. Link WeChat
cc-connect weixin setup --project default
# A QR code appears → scan with WeChat to authorize

# 3. Start
cc-connect
```

**Configuration**:

Edit `~/.cc-connect/config.toml`:
- `workDir`: path to your code workspace
- `agent`: `claudecode`
- `allowedUsers`: your WeChat user ID (security — only accept your messages)

**Verify**: Send "note down: test message" via WeChat → check Obsidian `00草稿箱/手机暂存/` for a new file.

**Auto-start on boot** (optional):
```bash
cc-connect daemon install
```

> ⚠️ **Note**: cc-connect is an open-source community project ([GitHub: chenhg5/cc-connect](https://github.com/chenhg5/cc-connect)). WeChat iLink API is provided by Tencent; stability depends on Tencent's policies. Phone terminal is unavailable when your computer is off or offline.

---

### 🅱️ Claude mobile app (simplest)

**Result**: Talk to Claude Code from the official Claude app. Good for occasional vault searches and quick notes.

**Prerequisites**:

| Requirement | How to check |
|-------------|--------------|
| Claude subscription (Pro or Max) | Check your account at claude.ai |

**How to use**:

1. Download Claude app (iOS App Store; Android TBD)
2. Sign in with your Claude account
3. Claude Code's Remote Control syncs your project context automatically
4. Start chatting

**Limitations**:
- No automatic Obsidian routing — phone messages won't auto-save to your vault
- You need to manually specify project context
- Quick capture and vault search are less seamless than Path A

**Best for**: Users who want zero config and occasional phone access.

---

### 🅲 Skip for now

The phone terminal is entirely optional. VSCode + Obsidian work perfectly without it. Come back anytime.

---

## FAQ

### Will my WeChat account get banned?

No. cc-connect uses WeChat's official iLink Bot API (opened in 2026), not hacks or third-party plugins. Compliant usage is safe.

### Does my computer need to stay on?

For Path A, yes. cc-connect runs on your computer — if it's off, WeChat messages can't reach Claude Code. If keeping your PC on isn't practical, use Path B (Claude app) or stick with VSCode + Obsidian only.

### Is it secure?

Path A routes messages through Tencent's servers — not end-to-end encrypted. **Never send passwords, API keys, or financial details via WeChat.**

### Nothing happens after setup?

1. Check cc-connect is running (you should see log output in terminal)
2. Confirm WeChat authorization was completed (QR scan)
3. Test Claude Code directly: run `claude` in terminal and confirm it works
