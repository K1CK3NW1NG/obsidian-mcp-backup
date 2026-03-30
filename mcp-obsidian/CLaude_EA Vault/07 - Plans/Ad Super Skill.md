---
type: plan
created: 2026-03-21
status: complete
spec: docs/superpowers/specs/2026-03-21-ad-super-skill-design.md
---

# Ad Super Skill — Implementation Plan

Build a 5-skill Meta ad management ecosystem — standalone Meta skill + ad-manager, ad-creator, and ad-researcher sub-skills under a super skill router.

## Architecture
- Skill-driven orchestration with a CLI layer
- SKILL.md files tell Claude what to do and which scripts to run
- Node.js CLI scripts handle all Meta Graph API calls

## Tech Stack
- Node.js, axios, dotenv, Jest, nock
- Meta Graph API v18+
- Meta Ad Library API (public)

## Skills Built
- `meta` — standalone full-access Meta skill
- `ad-super` — super skill router
- `ad-manager` — campaign management
- `ad-creator` — ad creative generation
- `ad-researcher` — competitor/library research

## Related
- [[../04 - Skills/ad-super]]
- [[../04 - Skills/meta]]
- [[../02 - Projects/Meta Ad Creation]]
