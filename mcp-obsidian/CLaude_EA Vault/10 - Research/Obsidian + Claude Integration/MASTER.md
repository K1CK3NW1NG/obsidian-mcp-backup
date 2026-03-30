---
topic: Obsidian + Claude Code Integration
researched: 2026-03-25
phase: 3-complete
---

# Obsidian + Claude Code — Second Brain Research

**Vault:** [[../../ME]] | [[../../WORK]] | Related: [[../../00 - Brain/CLAUDE]] · [[../../02 - Projects/Apex App]]


## Summary
This vault IS the implementation. Claude Code and Obsidian share the same markdown files — no API wrappers, no sync tools needed for the core setup. The key files Claude reads every session are CLAUDE.md (instructions) and memory files (persistent context).

---

## Integration Tiers (3 Methods)

### Tier 1 — Terminal Native (Simplest)
Point Claude Code CLI at your Obsidian vault folder. Run `claude` from inside the vault.
- Zero setup beyond installing Claude Code
- Claude reads/writes vault files directly
- Use `CLAUDE.md` at vault root as the instruction file

### Tier 2 — MCP Server (Most Powerful)
Install an Obsidian plugin that acts as an MCP server. Claude gets vault access as a tool.

**Best MCP options:**
- **obsidian-claude-code-mcp** (iansinnott) — WebSocket + HTTP/SSE, file ops + Obsidian API access
- **obsidian-mcp-tools** (jacksteamdev) — Semantic search via Smart Connections + Templater integration
- **Nexus / Claudesidian MCP** — 2-tool architecture (getTools + useTools), ~95% fewer tokens vs 40+ individual tools

### Tier 3 — Embedded Plugin (Obsidian-Native)
Run Claude Code inside Obsidian's sidebar — no terminal switching.

**Best plugins:**
- **Claudian** (YishenTu) — Embeds Claude Code as AI collaborator, Skills system, updated March 24 2026
- **Agent Client** — ACP bridge for any AI agent, `@notename` syntax, session export to vault

---

## Vault Structure (Best Practice)

```
Vault/
├── 00 - Brain/          ← CLAUDE.md, Context, Memory, Rules
├── 01 - Decisions/      ← append-only log
├── 02 - Projects/       ← active workstreams
├── 03 - Clients/        ← client folders + reports
├── 04 - Skills/         ← skill index + summaries
├── 05 - SuperSkills/    ← multi-component systems
├── 06 - Brand/          ← brand identity files
├── 07 - Plans/          ← implementation plans
├── 08 - References/     ← SOPs, templates, color palettes
├── 09 - Templates/      ← session templates
└── 10 - Research/       ← deep-dive research output
```

---

## CLAUDE.md — The Most Important File

- Lives at vault root (or `00 - Brain/`)
- Loads automatically every Claude Code session
- Contains: vault purpose, naming conventions, active project context, behavioral rules, env var refs
- **Critical:** Update "Active Context" section before each session — goes stale fast
- Double duty: instructions for Claude AND a readable Obsidian note

---

## Essential Plugins for Claude Workflow

| Plugin | Purpose |
|--------|---------|
| **Dataview** | Query vault like a database — dashboards from frontmatter |
| **Tasks** | Task management with dates/filters — Claude writes, you see live |
| **Templater** | Auto-scaffold new notes in exact format Claude expects |
| **Smart Connections** | Semantic search (required for MCP-tools semantic search) |
| **Obsidian Git** | Auto-commit after each Claude session — undo layer |
| **Claudian** | Run Claude Code inside Obsidian sidebar |
| **Canvas** | Visual project maps Claude can reference via markdown |

---

## Backend Integrations (Multipliers)

| Integration | What it enables |
|-------------|----------------|
| GitHub CLI | Version control + issue/PR ops from Claude |
| Linear | Task management two-way sync |
| Slack | Team comms pull into vault |
| Telegram + Whisper | Voice-to-text → auto-filed vault notes |
| Firecrawl | Web scraping → markdown archiving in vault |
| Google Gemini Vision | PDF/image analysis without manual description |
| Obsidian Git | Multi-device sync + undo layer |

---

## Key Open-Source Repos

| Repo | What it is |
|------|-----------|
| [COG-second-brain](https://github.com/huytieu/COG-second-brain) | Self-evolving, 17 skills, multi-agent (Claude/Kiro/Gemini/Codex) |
| [claudesidian](https://github.com/heyitsnoah/claudesidian) | PARA starter kit + pre-built commands, Gemini Vision + Firecrawl |
| [claudian](https://github.com/YishenTu/claudian) | Embed Claude Code in Obsidian sidebar, Skills system |
| [obsidian-claude-code-mcp](https://github.com/iansinnott/obsidian-claude-code-mcp) | WebSocket MCP bridge |
| [obsidian-mcp-tools](https://github.com/jacksteamdev/obsidian-mcp-tools) | Semantic search + Templater MCP |
| [second-brain-gtd](https://github.com/sean-esk/second-brain-gtd) | GTD-style with Claude skills |

---

## Failure Modes to Design Around

- Never run Obsidian Sync + another sync service simultaneously (corruption risk)
- Scope file paths explicitly — large vaults hit context window limits fast
- CLAUDE.md goes stale if Active Context section isn't updated
- Never let Claude rewrite raw transcriptions (loss of nuance)
- No file locking in Obsidian — simultaneous writes can conflict
- Flatten folder hierarchy beyond 2 levels to reduce token overhead

---

## YouTube Resources

1. [How I Use Obsidian + Claude Code to Run My Life](https://www.youtube.com/watch?v=6MBq1paspVU) — Greg Isenberg | 212K views
2. [Let Claude Automate Your Obsidian Notes (MCP)](https://www.youtube.com/watch?v=VeTnndXyJQI) — Zen van Riel | 55K views
3. [Claude Code Turned Obsidian Into My Dream Second Brain](https://www.youtube.com/watch?v=2kbINqpluM0) — Mark Kashef | 49K views
4. [Claude Cowork + Obsidian Will Change How You Work Forever](https://www.youtube.com/watch?v=qo4YZvC1q5I) — Ben AI | 15K views
5. [Build Your AI Second Brain with Obsidian + Claude Code](https://www.youtube.com/watch?v=2mAGV7MQd04) — Noah Vincent | 12K views

## X Posts
1. [kepano (CEO Obsidian)](https://x.com/kepano/status/2010786788152070587) — 3-step setup
2. [Greg Isenberg](https://x.com/gregisenberg/status/2026036464287412412) — personal OS thread
3. [Om Patel](https://x.com/om_patel5/status/2033745489888149585) — vault as persistent brain
4. [Aakash Gupta](https://x.com/aakashgupta/status/2026887547813769325) — hot combo breakdown
5. [Hesamation](https://x.com/Hesamation/status/2026801420872093708) — kepano's official Claude skills
