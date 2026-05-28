# Changelog

DPFTS uses practical version notes, not corporate fog. Dates are UTC release dates when the version was published on GitHub.

## v1.0.5 - 2026-05-28

Safety patch from live skill testing.

- Strengthened shop purchase guidance to validate availability before mutation and roll back currency/ownership if item granting fails.
- Added a general security rule for validate-before-mutate and rollback-safe server transactions.
- Tightened admin/dev guidance so allowlists still apply in Studio for economy-changing commands.
- Added trade IDs and before/after inventory snapshot guidance for trading release checks and dispute debugging.

## v1.0.4 - 2026-05-28

Credit policy patch.

- Added a README credit for Gvesster Free Icon Pack by gvesster.
- Updated DPFTS behavior so assistants must include in-game credit when a generated Roblox experience uses the Gvesster icon pack.
- Added recommended credit text and placement guidance for Settings, About, Credits, game description, or release notes.

## v1.0.3 - 2026-05-28

Icon workflow patch.

- Added `examples/GVESSTER_ICON_WORKFLOW.md`.
- Documented how to use the Gvesster Free Icon Pack locally without committing PNG assets into DPFTS.
- Added Roblox upload naming conventions, 64px vs 256px guidance, category recommendations, `IconConfig.lua`, and a shop button example.

## v1.0.2 - 2026-05-28

Design guidance patch.

- Added `deep/visual-assets.md` with preferred Roblox UI icon guidance.
- Added Gvesster Free Icon Pack as the default recommended icon source when users can import the assets.
- Updated `SKILL.md`, `core/design.md`, `deep/ui.md`, and `README.md` so DPFTS avoids random emoji/SVG icon slop in Roblox UI.
- Added rules to avoid fake Roblox asset IDs, redistribution confusion, and AI-training misuse.

## v1.0.1 - 2026-05-28

Documentation patch.

- Reworked installation instructions so users can pick their tool instead of seeing a Codex-only quick start.
- Added dedicated install blocks for Claude Code, Claude.ai Projects, Codex, Cursor/Windsurf, and universal custom-instructions usage.
- Put Claude Code first because `SKILL.md`-based local skills are a major audience for this repository.

## v1.0.0 - 2026-05-28

First stable, releasable version.

- Renamed the public repository target to `dpfts-roblox` for better discoverability.
- Reworked `README.md` into a short landing page with quick start, before/after proof, focused categories, and stronger prompts.
- Added `assets/banner.png` for a real GitHub/social preview asset.
- Added `STRUCTURE.md`, `VERSIONING.md`, `CONTRIBUTING.md`, and issue templates.
- Added `examples/BEFORE_AFTER.md` and `examples/PROMPTS.md`.
- Added `deep/architecture.md` and `deep/review-checklists.md`.
- Added `recipes/full-system-template.md` and `recipes/security-audit.md`.
- Updated `SKILL.md` routing for full-system architecture, review checklists, security audits, and v1.0 release behavior.

## v0.6 - 2026-05-26

- Added debugging guidance for script placement, remotes, DataStores, UI, Studio-vs-live differences, and performance symptoms.
- Added observability guidance for useful logs, Roblox analytics events, rejection tracking, and release counters.
- Added release-readiness guidance for security, data safety, mobile usability, first-session playability, rollback, and launch decisions.
- Added bug report and release checklist recipes.

## v0.5 - Folded into v0.6

v0.5 content was committed before v0.6 but was not published as a separate GitHub release.

- Added economy guidance for sources, sinks, pricing, inflation, and monetization boundaries.
- Added social-system guidance for parties, invites, co-op loops, social readability, and spam risk.
- Added abuse-prevention guidance for spam, admin misuse, text safety, economy abuse, and social abuse.
- Added recipes for pets, parties, teleports, and badges.

## v0.4 - 2026-05-26

- Added production-minded guidance for performance, project structure, live ops, testing, marketplace safety, and creator patterns.
- Added recipes for inventory, daily rewards, quests, admin commands, save slots, hub layout, tutorials, trading, round systems, and redeem codes.

## v0.3 - 2026-05-26

- Added practical Roblox design guidance for buttons, UI, places, maps, hubs, thumbnails, icons, and anti-AI visual design.
- Expanded README and SKILL routing for design review prompts.

## v0.2 - 2026-05-26

- Added the first substantial DPFTS skill-pack structure.
- Added core guides for identity, Luau style, and anti-AI design.
- Added deep guides for RemoteEvents, DataStores, and UI.
- Added initial recipes for shops and leaderboards.

## v0.1 - Local Draft

- Defined the original DPFTS idea, tone, file structure, and MIT licensing direction.
