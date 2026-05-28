# DPFTS Roblox AI Skills

![DPFTS Roblox AI Skills banner](assets/banner.png)

**DPFTS publicly stands for Developer's Plugin For Total Savings.** It is an AI skill pack that teaches coding assistants to write safe, practical, production-ready Roblox and Luau code.

DPFTS is built for Roblox developers who use AI tools but do not want client-trusting RemoteEvents, broken DataStores, messy UI, giant scripts, or generic simulator sludge.

Install this skill: github.com/timasigmapro228/dpfts-roblox

## Why This Exists

AI assistants can write Roblox code that looks confident and still ships bad architecture:

- The client tells the server it has `99999` coins.
- A LocalScript grants rewards or badges.
- DataStores save without `pcall`, validation, or defaults.
- Shop UI says "Purchased" before the server confirms anything.
- A map is huge, empty, and impossible to read on mobile.

DPFTS gives the assistant stronger Roblox instincts: server authority, clean Luau, clear Studio placement, mobile-first UI, safer monetization, practical testing, and anti-AI-slop design review.

## Quick Start

### 1. Install

For Codex-style local skills on Windows:

```powershell
git clone https://github.com/timasigmapro228/dpfts-roblox.git "$env:USERPROFILE\.codex\skills\dpfts"
```

For macOS/Linux:

```bash
git clone https://github.com/timasigmapro228/dpfts-roblox.git ~/.codex/skills/dpfts
```

If your AI tool does not support local skills, open `SKILL.md` and paste it into your custom instructions, then reference the deeper files when needed.

### 2. Use The Skill

Ask your assistant for Roblox work with DPFTS enabled:

```text
Use DPFTS to build a server-authoritative coin shop with clean Luau and testing steps.
```

### 3. Check The Output

A good DPFTS answer should include:

- Roblox Studio placement.
- Server/client separation.
- Clean Luau code.
- RemoteEvent validation.
- DataStore safety when persistence is involved.
- Testing steps and common failure cases.

## Before And After

### Without DPFTS

```lua
-- ServerScriptService/BadShop.server.lua
BuyItem.OnServerEvent:Connect(function(player, itemId, price)
    player.leaderstats.Coins.Value -= price
    giveItem(player, itemId)
end)
```

Problem: the client can send any `itemId` and any `price`, including `-99999`.

### With DPFTS

```lua
-- ServerScriptService/ShopService.server.lua
BuyItem.OnServerEvent:Connect(function(player, itemId)
    if typeof(itemId) ~= "string" then
        return
    end

    local item = ShopConfig.Items[itemId]
    if not item then
        return
    end

    local profile = Profiles[player]
    if not profile or profile.Coins < item.Price then
        return
    end

    if profile.Inventory[itemId] then
        return
    end

    profile.Coins -= item.Price
    profile.Inventory[itemId] = true
    BuyResult:FireClient(player, true, itemId, profile.Coins)
end)
```

Better: the client requests an action, the server owns price, currency, inventory, validation, and the final result.

More examples: [examples/BEFORE_AFTER.md](examples/BEFORE_AFTER.md)

## What It Helps With

- **Security:** RemoteEvents, permissions, exploit review, admin tools, purchases, and server authority.
- **Code quality:** Luau style, ModuleScripts, project structure, testing, debugging, and release checks.
- **Persistence:** DataStores, save slots, schema validation, autosave, failure handling, and `BindToClose`.
- **Game systems:** shops, inventory, quests, daily rewards, leaderboards, trading, rounds, pets, parties, teleports, badges, and codes.
- **Design:** mobile UI, buttons, hubs, maps, thumbnails, first 30 seconds, tutorials, and anti-AI-slop reviews.
- **Production:** performance, live ops, observability, analytics, rollback thinking, changelogs, and launch checklists.

## Strong Example Prompts

- "Use DPFTS to review this RemoteEvent system like an exploiter would."
- "Use DPFTS to build a server-authoritative shop that cannot trust client prices."
- "Use DPFTS to design a DataStore profile for Coins, Level, Inventory, and Settings."
- "Use DPFTS to review my Roblox place layout and tell me what feels like AI slop."
- "Use DPFTS to design mobile-friendly Roblox buttons for my shop UI."
- "Use DPFTS to debug why my shop works in Play Solo but fails with two clients."
- "Use DPFTS to review whether this Roblox update is ready to publish."
- "Use DPFTS to create a security audit checklist before I release trading."

More prompts: [examples/PROMPTS.md](examples/PROMPTS.md)

## Repository Map

The short version:

- `SKILL.md`: main AI-facing instruction file.
- `core/`: identity, Luau style, and design rules.
- `deep/`: focused guides for security, DataStores, remotes, UI, performance, economy, debugging, release readiness, and more.
- `recipes/`: ready-to-use patterns for common Roblox systems.
- `examples/`: prompt examples and before/after examples.

Full file map: [STRUCTURE.md](STRUCTURE.md)

## v1.0 Status

DPFTS v1.0 is the first stable, releasable version of the skill pack. It is ready to use as a practical Roblox AI guidance pack, with MIT licensing, release notes, contribution guidance, issue templates, examples, and a cleaner GitHub landing page.

It is not an official Roblox product. It does not install scripts into Roblox Studio automatically. It does not replace testing in Studio. It gives your AI assistant better Roblox judgment.

## Contributing

Recipes, corrections, and better Roblox patterns are welcome. Start with [CONTRIBUTING.md](CONTRIBUTING.md).

## License

MIT. See [LICENSE](LICENSE).

If DPFTS saves you time or stops your AI assistant from shipping exploit-friendly Roblox code, star the repo.
