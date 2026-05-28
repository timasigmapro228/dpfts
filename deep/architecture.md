# Roblox Architecture Guide

Roblox architecture is about deciding where truth lives. DPFTS should push systems toward small, readable services where the server owns game rules and the client owns presentation.

Do not overbuild. A solo developer MVP does not need a framework maze. It needs clean placement, safe remotes, clear configs, and systems that can be tested in Studio.

## Default Architecture

Use this layout unless the project already has a stronger pattern:

```text
ReplicatedStorage/
|-- Remotes/
|-- Shared/
|-- Config/
ServerScriptService/
|-- Services/
|-- ServerMain.server.lua
StarterPlayer/
`-- StarterPlayerScripts/
StarterGui/
`-- ScreenGuis/
```

## Ownership Rules

Server owns:

- Currency.
- Inventory.
- Damage.
- Rewards.
- Quest progress.
- Purchases.
- DataStore profiles.
- Badge awards.
- Teleport permission.
- Trade completion.
- Round winners.
- Admin actions.

Client owns:

- Input.
- Camera.
- UI display.
- Local animations.
- Visual effects.
- Sound playback when it does not affect game truth.
- Requesting an action from the server.

Shared modules contain:

- Constants.
- Type definitions.
- Read-only config.
- Utility functions that do not grant value.

## Service Boundaries

Good services have one job:

- `ProfileService`: load, validate, save, and expose player data safely.
- `CurrencyService`: grant and spend currency through server-only functions.
- `ShopService`: validate purchases against server config.
- `InventoryService`: grant, equip, unequip, and query owned items.
- `QuestService`: track server-confirmed progress.
- `RoundService`: own round state and rewards.
- `TeleportServiceWrapper`: validate destination and access.

Bad services do everything:

- `GameManager`
- `MainScript`
- `EverythingService`
- `PlayerStuff`

If a service name means nothing, the file probably does too much.

## Remote Design

Remotes should express player intent, not player authority.

Good:

```lua
BuyItem:FireServer("IronSword")
EquipPet:FireServer("Cat")
RequestTeleport:FireServer("ForestZone")
```

Bad:

```lua
BuyItem:FireServer("IronSword", 100, 999999)
GiveCoins:FireServer(5000)
CompleteQuest:FireServer("DailyQuest")
SetRank:FireServer("Admin")
```

## Config Design

Put item definitions in shared config when clients need to display names, icons, and prices. The server still treats config as the source of truth.

```lua
-- ReplicatedStorage/Config/ShopConfig.lua
return {
    Items = {
        IronSword = {
            DisplayName = "Iron Sword",
            Price = 100,
            Category = "Weapons",
        },
    },
}
```

Do not let the client send config values back as proof.

## Full-System Output Shape

When DPFTS designs a complete system, include:

1. Assumptions.
2. Studio folder placement.
3. Server responsibilities.
4. Client responsibilities.
5. Shared config/modules.
6. RemoteEvents and RemoteFunctions.
7. Data model.
8. Code by file.
9. Studio testing steps.
10. Security review.
11. Common failure cases.
12. Extension points.

## Architecture Review Questions

- What is the game truth?
- Which script owns that truth?
- Which remotes can change that truth?
- What does the client request?
- What does the server validate?
- What data must be loaded before actions work?
- What happens if a player leaves mid-action?
- What happens if a RemoteEvent is spammed?
- What can be tested with two clients in Studio?

## MVP Rules

For MVPs:

- Prefer one clear server service over five tiny fake abstractions.
- Use simple ModuleScripts only when they reduce confusion.
- Keep configs readable.
- Build one safe path before adding edge features.
- Add logs where failure is likely.
- Make testing steps explicit.

Do not build a framework because the user asked for a shop.

## Mistakes To Avoid

- Hiding important logic in LocalScripts.
- Creating one massive script with every system inside it.
- Creating twenty services for a tiny prototype.
- Using RemoteFunctions for long-running or unreliable operations.
- Letting UI state be treated as server truth.
- Putting server secrets in `ReplicatedStorage`.
- Forgetting multiplayer testing.
