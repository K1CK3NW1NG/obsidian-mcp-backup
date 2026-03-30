---
status: on-hold
type: project
blocker: Google Drive MCP setup
last-updated: 2026-03-25
---

# Meta Ad Creation

← [[../WORK]]


Build a full Meta (Facebook Ads) ad set for Apela One LLC.

## Status
On hold — awaiting Google Drive MCP setup at desktop.

## Full Ad Set Structure
- 3 video ads — hooks + scripts outlined
- 10 static ads — 5 groups of 2 for A/B testing

## Toolchain
- Image generation: Gemini
- Skills: `ad-super`, `banner-design`, `ghl-image-gen`, `design`, `brand`

## Blocker — How to Unblock
1. Enable Google Drive API in Google Cloud Console
2. Create OAuth 2.0 Desktop credentials → download JSON
3. Run: `npx @modelcontextprotocol/server-gdrive` and authenticate
4. Update `.mcp.json` with the server config
5. Restart Claude Code

## Related
- [[../04 - Skills/ad-super]]
- [[../00 - Brain/Memory/MEMORY]]
