# DPFTS

**Developer's Plugin For Total Savings**: a practical AI skill pack for Roblox developers.

DPFTS is a Markdown-based instruction pack for AI coding assistants. It helps them produce safer, cleaner, more Roblox-aware Luau code and design advice.

This is not an official Roblox product. This is not a normal Roblox Studio plugin yet. It is a GitHub skill pack made of instruction files.

DPFTS publicly stands for Developer's Plugin For Total Savings. Internally, it also means Don't Pay For This Shit.

## Who It Is For

- Beginners who need working Roblox systems without insecure shortcuts.
- Intermediate developers who want cleaner Luau and better architecture.
- Solo creators who need practical MVP systems.
- Small teams that want consistent AI-generated Roblox code and design reviews.

## Why It Exists

AI assistants often generate Roblox code that looks confident but breaks in real games:

- LocalScripts secretly controlling currency or rewards.
- RemoteEvents that trust whatever the client sends.
- DataStores without validation or `pcall`.
- Giant scripts that nobody wants to maintain.
- UI with ten random buttons fighting for attention.
- Maps that look large but play empty.

DPFTS gives the assistant a Roblox-specific spine: server authority, clear placement, clean Luau, practical UI, readable maps, and no fake complexity.

## Installation

Install this skill: github.com/timasigmapro228/dpfts

## What DPFTS Helps With

- Luau code quality.
- Roblox architecture.
- RemoteEvent and RemoteFunction security.
- Server-authoritative security reviews.
- DataStore safety.
- MarketplaceService purchases, gamepasses, and developer products.
- UI/UX clarity.
- Mobile-first button and screen design.
- Place, map, hub, thumbnail, and icon design.
- Avoiding bad AI-generated game design.
- Recipes for common systems like shops, leaderboards, inventory, and daily rewards.

## Repository Structure

```text
dpfts/
|-- README.md
|-- SKILL.md
|-- core/
|   |-- identity.md
|   |-- luau-style.md
|   |-- anti-ai-design.md
|   `-- design.md
|-- deep/
|   |-- remote-events.md
|   |-- datastore.md
|   |-- security.md
|   |-- marketplace.md
|   `-- ui.md
`-- recipes/
    |-- shop.md
    |-- inventory.md
    |-- daily-rewards.md
    `-- leaderboard.md
```

- `SKILL.md`: main AI-facing behavior guide.
- `core/identity.md`: DPFTS personality, response modes, and refusal rules.
- `core/luau-style.md`: Luau coding standards for Roblox systems.
- `core/anti-ai-design.md`: rules for avoiding generic AI-generated Roblox game design.
- `core/design.md`: practical Roblox design guide for buttons, UI, maps, hubs, thumbnails, and visual clarity.
- `deep/remote-events.md`: practical security guide for RemoteEvents and RemoteFunctions.
- `deep/datastore.md`: practical DataStore loading, validation, saving, and failure handling.
- `deep/security.md`: server authority, validation, permissions, rate limits, and exploit review.
- `deep/marketplace.md`: safe MarketplaceService patterns for gamepasses and developer products.
- `deep/ui.md`: Roblox UI rules for clear, mobile-friendly interfaces.
- `recipes/shop.md`: ready-to-use server-authoritative shop recipe.
- `recipes/inventory.md`: server-owned inventory and equip recipe.
- `recipes/daily-rewards.md`: server-authoritative daily reward recipe.
- `recipes/leaderboard.md`: simple leaderstats leaderboard recipe.

## v0.2 Scope

DPFTS v0.2 focuses on practical, security-first Roblox development guidance. It gives AI assistants clear rules and examples for common Roblox development work:

- Secure client/server separation.
- Practical Luau structure.
- Safe RemoteEvent patterns.
- Safe DataStore patterns.
- Server-side exploit review and validation patterns.
- MarketplaceService safety for gamepasses and developer products.
- Clear UI and button guidance.
- Useful place and map design advice.
- Recipes for shops, leaderboards, inventory, and daily rewards.

It does not install scripts into Roblox Studio automatically. It does not replace testing in Studio. It does not make your game design good by magic. It gives the assistant better instincts.

## Example Prompts

After installing DPFTS, try prompts like:

- "Use DPFTS to build a server-authoritative coin shop with clean Luau and testing steps."
- "Use DPFTS to review my RemoteEvent code for exploit risks."
- "Use DPFTS to design a safe DataStore profile system for Coins, Level, Inventory, and Settings."
- "Use DPFTS to improve this Roblox UI without adding button spam."
- "Use DPFTS to review my Roblox place layout and tell me what feels like AI slop."
- "Use DPFTS to design mobile-friendly Roblox buttons for my shop UI."
- "Use DPFTS to make this leaderboard simple now and DataStore-ready later."
- "Use DPFTS to review this RemoteEvent system like an exploiter would."
- "Use DPFTS to add a safe gamepass perk without trusting the client."
- "Use DPFTS to build a simple server-owned inventory with equip requests."
- "Use DPFTS to build a daily reward that cannot be spammed."

## License

MIT. See `LICENSE`.
