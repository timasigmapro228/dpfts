# Repository Structure

DPFTS is intentionally plain Markdown. `SKILL.md` is the entry point. The other files are focused references the assistant should read only when relevant.

```text
dpfts-roblox/
|-- README.md
|-- SKILL.md
|-- CHANGELOG.md
|-- VERSIONING.md
|-- CONTRIBUTING.md
|-- STRUCTURE.md
|-- LICENSE
|-- assets/
|   `-- banner.png
|-- core/
|   |-- identity.md
|   |-- luau-style.md
|   |-- anti-ai-design.md
|   `-- design.md
|-- deep/
|   |-- architecture.md
|   |-- remote-events.md
|   |-- datastore.md
|   |-- security.md
|   |-- marketplace.md
|   |-- creator-patterns.md
|   |-- testing.md
|   |-- mobile-ui.md
|   |-- performance.md
|   |-- project-structure.md
|   |-- live-ops.md
|   |-- economy.md
|   |-- social-systems.md
|   |-- abuse-prevention.md
|   |-- debugging.md
|   |-- observability.md
|   |-- release-readiness.md
|   |-- review-checklists.md
|   |-- visual-assets.md
|   `-- ui.md
|-- recipes/
|   |-- shop.md
|   |-- inventory.md
|   |-- daily-rewards.md
|   |-- quests.md
|   |-- admin-commands.md
|   |-- save-slots.md
|   |-- hub-layout.md
|   |-- tutorial.md
|   |-- trading.md
|   |-- round-system.md
|   |-- codes.md
|   |-- pets.md
|   |-- party-system.md
|   |-- teleports.md
|   |-- badges.md
|   |-- bug-report-template.md
|   |-- release-checklist.md
|   |-- full-system-template.md
|   |-- security-audit.md
|   `-- leaderboard.md
|-- examples/
|   |-- BEFORE_AFTER.md
|   `-- PROMPTS.md
`-- .github/
    `-- ISSUE_TEMPLATE/
        |-- bug_report.md
        `-- feature_request.md
```

## Entry Point

- `SKILL.md`: main AI-facing behavior guide. It tells an assistant how to act, what to prioritize, when to read deeper files, and what never to produce.

## Core Guides

- `core/identity.md`: DPFTS personality, response modes, and refusal rules.
- `core/luau-style.md`: Luau coding standards for Roblox systems.
- `core/anti-ai-design.md`: rules for avoiding generic AI-generated Roblox game design.
- `core/design.md`: practical Roblox design guide for buttons, UI, maps, hubs, thumbnails, and visual clarity.

## Deep Guides

- `deep/architecture.md`: full-system Roblox architecture decisions and service boundaries.
- `deep/remote-events.md`: practical security guide for RemoteEvents and RemoteFunctions.
- `deep/datastore.md`: practical DataStore loading, validation, saving, and failure handling.
- `deep/security.md`: server authority, validation, permissions, rate limits, and exploit review.
- `deep/marketplace.md`: safe MarketplaceService patterns for gamepasses and developer products.
- `deep/creator-patterns.md`: Roblox creator patterns for retention, FTUE, social play, updates, and analytics.
- `deep/testing.md`: test passes for remotes, DataStores, UI, first sessions, and analytics.
- `deep/mobile-ui.md`: mobile-first UI placement, tap targets, HUDs, shops, and tutorial prompts.
- `deep/performance.md`: practical performance rules for server heartbeat, client FPS, remotes, memory, maps, and UI.
- `deep/project-structure.md`: Roblox folder structure, services, configs, remotes, and client/server ownership.
- `deep/live-ops.md`: updates, events, retention hooks, analytics, rollback thinking, and player communication.
- `deep/economy.md`: currencies, sources, sinks, pricing, progression pacing, and monetization boundaries.
- `deep/social-systems.md`: parties, invites, co-op loops, trading boundaries, social readability, and spam risk.
- `deep/abuse-prevention.md`: anti-spam, admin misuse prevention, text safety, economy abuse, and social abuse checks.
- `deep/debugging.md`: Roblox bug triage, reproduction, script placement checks, remote debugging, and failure isolation.
- `deep/observability.md`: useful logs, analytics events, release counters, and server-confirmed measurement.
- `deep/release-readiness.md`: pre-release checks for security, data, mobile, first sessions, performance, and rollback.
- `deep/review-checklists.md`: reusable review passes for security, data, UI, economy, performance, release readiness, and AI slop.
- `deep/visual-assets.md`: preferred icon source, Gvesster Free Icon Pack usage rules, Roblox import workflow, and icon UI guidance.
- `deep/ui.md`: Roblox UI rules for clear, mobile-friendly interfaces.

## Recipes

- `recipes/shop.md`: ready-to-use server-authoritative shop recipe.
- `recipes/inventory.md`: server-owned inventory and equip recipe.
- `recipes/daily-rewards.md`: server-authoritative daily reward recipe.
- `recipes/quests.md`: server-owned quest progress and reward recipe.
- `recipes/admin-commands.md`: allowlisted server-side admin command recipe.
- `recipes/save-slots.md`: simple validated DataStore save slot recipe.
- `recipes/hub-layout.md`: practical hub layout recipe for spawn, landmarks, shops, and social areas.
- `recipes/tutorial.md`: FTUE/tutorial recipe focused on first action, first reward, and drop-off points.
- `recipes/trading.md`: safe server-authoritative player trading recipe.
- `recipes/round-system.md`: lobby, intermission, match, and reward loop recipe.
- `recipes/codes.md`: one-time redeem code recipe.
- `recipes/pets.md`: server-owned pet ownership and equip recipe.
- `recipes/party-system.md`: safe party invites, join/leave, and membership recipe.
- `recipes/teleports.md`: validated zone and cross-place teleport recipe.
- `recipes/badges.md`: BadgeService milestone award recipe.
- `recipes/bug-report-template.md`: Roblox-specific bug report format for AI assistants and teammates.
- `recipes/release-checklist.md`: practical launch checklist for small Roblox games and updates.
- `recipes/full-system-template.md`: prompt and output structure for complete Roblox systems.
- `recipes/security-audit.md`: pre-release security audit recipe.
- `recipes/leaderboard.md`: simple leaderstats leaderboard recipe.
