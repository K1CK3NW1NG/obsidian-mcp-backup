# Deep-Dive: Claude Agents, Skills & Bots — Best Methods

**Vault:** [[../../ME]] | [[../../WORK]] | Related: [[../../04 - Skills/skills-index]] · [[../../05 - SuperSkills/client-onboarding]] · [[../../02 - Projects/Apex App]]

**Date:** 2026-03-27
**Previous sessions:** None

---

## Quick Summary

- **Agent Teams just dropped** (experimental, v2.1.32+) — multiple full Claude instances coordinating through a shared task list and direct inter-agent messaging; completely different from subagents
- **Skills got a major overhaul** — new SKILL.md frontmatter system, Skill Creator bundled skill, open AgentSkills.io standard (same files work in Cursor/Gemini CLI/Codex)
- **Scheduled Tasks = 24/7 autonomous agents** — not cron jobs; self-healing workflows with 3-layer memory (code correction + prompt evolution + JSON logs)
- **The community has moved from prompting to skill engineering** — description field quality is the #1 lever for reliable skill triggering
- **Pattern hierarchy**: Subagents (report back) → Agent Teams (collaborate directly) → Scheduled Tasks (run autonomously on timer) — each serves different use cases

---

## Key Findings

### Skills — Structure & Triggering

- Every SKILL.md needs two parts: YAML frontmatter (configuration) + markdown content (instructions)
- **Description field = trigger, not summary** — write it as "when should Claude fire this?" not "what does this skill do?"
- **Two skill types** (Nate Herk framework):
  - Capability Uplift: give Claude abilities it doesn't have natively
  - Encoded Preference: guide Claude to follow your specific workflow
- Key frontmatter fields to know:
  - `disable-model-invocation: true` — you control when it fires (use for deploys, commits, side effects)
  - `user-invocable: false` — background knowledge, not a slash command
  - `context: fork` — runs in isolated subagent (pair with `agent: Explore` for research skills)
  - `allowed-tools` — restrict what Claude can use when skill is active
  - `model` / `effort` — override per skill (use Haiku for cheap skills, Opus for complex ones)
  - `paths` — only activate when working on matching file patterns
- Shell injection: `` !`command` `` runs before Claude sees anything — outputs replace placeholder (preprocessing)
- Build a **Gotchas section** in every skill — Claude's known failure points, updated over time
- Keep SKILL.md under 500 lines — offload to supporting files
- Skill context budget: ~2% of context window; run `/context` if skills aren't loading

### Agent Teams vs. Subagents vs. Scheduled Tasks

**Subagents** (within a session, via Agent tool):
- Spawn for focused, isolated tasks where only the result matters
- Can run in background (`run_in_background: true`)
- Only report back to main agent — can't talk to each other
- `/batch` spawns one per task in git worktrees + each opens a PR (for large refactors)

**Agent Teams** (experimental, multiple sessions):
- Multiple full Claude instances sharing a task list + direct messaging
- Each teammate has own context window; loads CLAUDE.md, MCP, skills at spawn
- Best for: parallel research/review, multi-module features, competing hypothesis debugging, cross-layer changes
- Cost: ~3-4x tokens vs. single session
- Enable: `"CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"` in settings.json
- Start with 3-5 teammates; 5-6 tasks per teammate sweet spot
- Quality gates via hooks: TeammateIdle, TaskCreated, TaskCompleted (exit code 2 = block + feedback)
- Avoid file conflicts: each teammate must own different files
- Tip: use Sonnet for teammates, Opus for lead to cut costs

**Scheduled Tasks** (24/7 autonomous):
- Self-healing agentic workflows — NOT cron jobs
- 3-layer memory: code self-correction + prompt evolution + JSON log files
- Structure prompts: "read last-run.json, execute task, update status file"
- Desktop app must be running; missed runs catch up for 7 days
- New: Auto Dream subagent consolidates/prunes memory files automatically across sessions

### What the Community Is Building

- **Claude Code Game Studios** (48 agents, 36 slash commands, 8 hooks) — full game dev studio template
- **Paperclip** — "zero human company OS" — CEO/CTO/engineers/designers/marketers as AI agents
- **Claude-Swarm** — multi-agent dev team with roles, coordination, persistent memory
- **claudekit** — 20+ specialized subagents + auto-save checkpointing
- **Human-Taught Skills** (Cua) — record human demos as skills, agents replay with adaptation
- **Trail of Bits security skills** — 17 skills with decision trees, authoritative file paths, nested references

### New Features in Claude Code 2.x

- **Computer Use**: native mouse/keyboard/screenshot control for any desktop app
- **iMessage channel**: text Claude Code from your phone to trigger tasks remotely
- **Auto Dream**: background subagent that cleans up memory files across sessions
- **/btw command**: ask side questions without interrupting (experimental, buggy)
- **Skill Creator** (bundled): build, eval, optimize skills with guided workflow
- **/batch**: spawns parallel worktree agents for large codebase changes
- **1M context**: now available at extra cost (Opus 4.6: $10/$37.50/Mtok; Sonnet 4.6: $6/$22.50/Mtok)

### Open Standard & Ecosystem

- AgentSkills.io standard — same SKILL.md works across Claude Code, Cursor, Gemini CLI, Codex CLI, Antigravity IDE
- "Every company with technical docs will ship Skill packages — agents won't adopt your product without them"
- `npx skills add <package>` — npm-style skill installation
- Community skills repo: awesome-claude-code (github.com/hesreallyhim/awesome-claude-code)

---

## What Your References Covered
N/A — no references provided for this session

---

## What the Broad Search Added

Everything below came from YouTube, X/Twitter, official docs, GitHub, and community blogs:

- Official Agent Teams docs (complete architecture, hooks, limitations, display modes)
- Official Skills docs (full frontmatter reference, shell injection, bundled skills)
- Nate Herk's most recent video topics (2.0, Skills Creator, Executive Assistant, 10-hour course)
- Greg Isenberg's March 2026 video series (Paperclip, AI agents full course, OpenClaw)
- @bridgemindai real-time update tracking (Agent Teams launch, 1M context pricing, /btw)
- @juliangoldieseo practical automation builds (browser agent replacing employees, MCP setup guides)
- @sukh_saroy ambitious build documentation (47 micro-SaaS, Game Studios template, Paperclip)
- Community tools inventory (Ruflo, Claude-Swarm, claudekit, ccflare, Parry, Container Use)
- Context management warnings (70%/85%/90% degradation thresholds)

---

## Actionable Takeaways

- **Fix your skill descriptions first** — rewrite them as model triggers, not human-readable summaries
- **Add Gotchas sections** to every existing skill you have
- **Enable Agent Teams** for any task involving parallel domains (set env var in settings.json)
- **Use context: fork + agent: Explore** for research-heavy skills — isolated, no conversation history bleed
- **Structure scheduled tasks with JSON memory**: read last-run.json → execute → update status file
- **Set disable-model-invocation: true** for any skill with side effects (deploys, sends, commits)
- **Model per skill**: use Haiku for cheap/frequent skills, Sonnet for standard, Opus for complex reasoning skills
- **Paths field** for monorepos: scope skills to matching file patterns to avoid triggering on irrelevant work
- **Monitor context** at 70% — run /compact or open a fresh session; don't wait for hallucination symptoms
- **Watch Nate Herk's Skills Creator video** — native eval workflow for testing skill triggering reliability
- **Install ccflare** — track token usage per session/skill before costs spiral

---

## Ideas & Things to Build

- **Skill package for Apela One** — SKILL.md files for GHL workflows, client onboarding, ad creation; distribute via npm-style package to use across projects
- **Agent Team for client deliverables** — lead orchestrates: copy agent + automation-setup agent + QA agent working in parallel on a new client build
- **Scheduled Task for lead gen** — daily autonomous agent that checks GHL pipeline, drafts follow-up sequences, logs to JSON memory file
- **Browser agent for VA work** — Claude controlling Chrome to automate repetitive client research, form fills, data pulls
- **Human-Taught Skill** for recurring client processes — record once, Claude replays with adaptation on every new client
- **Skill for ad creation workflow** — trigger when Evan says "run ads", invoke the full ad-super skill chain with forked subagent for each platform
- **Parry hook** for security — add prompt injection scanner to Claude Code sessions that handle external data
- **Auto Dream setup** — let Claude maintain its own memory hygiene automatically
