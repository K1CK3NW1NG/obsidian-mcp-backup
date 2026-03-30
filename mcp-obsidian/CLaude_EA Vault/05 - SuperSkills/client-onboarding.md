---
type: superskill
trigger: "run onboarding", "onboard this client", "start the pipeline"
phases: 4
status: active
---

# Client Onboarding Pipeline — SuperSkill

Master orchestrator for the full client onboarding pipeline. Triggered when a paying client is confirmed from a GHL survey submission.

## 4 Phases
1. **Research + theme.json** — Scrape business, extract brand, generate theme
2. **GHL Subaccount + Brand Assets** — Create GHL sub, generate 4 brand images
3. **Website Build** — Full HTML/CSS/JS website build
4. **Review + Deploy** — Evan approves → deploy to GitHub/Vercel

## Permission Gates (only 5 things need Evan's input)
1. Evan says "start onboarding"
2. Evan confirms list of new clients
3. Evan says "yes" to Phase 2
4. Evan says "yes" to Phase 3
5. Evan approves website design (Phase 3→4 gate)

Everything else executes automatically.

## Current Client
- [[../03 - Clients/Hernandez Roofing/onboarding-report]] — Phase 3 not started

## Related Skills
- [[../04 - Skills/research-client]]
- [[../04 - Skills/ghl-image-gen]]
- [[../04 - Skills/design]]
