# Roblox Project Structure Guide

Good structure makes Roblox projects easier to extend and harder to accidentally exploit. DPFTS should explain where scripts go, what each layer owns, and how files communicate.

## Default Structure

```text
ReplicatedStorage/
|-- Remotes/
|-- Config/
|-- Shared/

ServerScriptService/
|-- Services/
|-- ServerModules/

ServerStorage/
|-- Assets/
|-- Tools/

StarterGui/
|-- ScreenGuis/

StarterPlayer/
`-- StarterPlayerScripts/
```

Use the exact structure only when it helps. Small games can stay smaller.

## Responsibilities

`ReplicatedStorage`:

- RemoteEvents and RemoteFunctions.
- Shared config safe for clients to read.
- Shared utility modules.
- Never store secrets here.

`ServerScriptService`:

- Server game logic.
- Services.
- Validation.
- Rewards.
- DataStore logic.
- Marketplace receipt handling.

`ServerStorage`:

- Server-only tools.
- Templates.
- Assets clients should not clone directly.

`StarterGui`:

- ScreenGuis.
- UI LocalScripts tied to a specific GUI.

`StarterPlayerScripts`:

- Client controllers.
- Input handling.
- Camera logic.
- Client systems not tied to one ScreenGui.

## Service Pattern

Use services when a system has real server responsibility.

Example:

```text
ServerScriptService/
`-- Services/
    |-- ShopService (Script or ModuleScript)
    |-- InventoryService
    `-- QuestService
```

Good service boundaries:

- Shop validates purchases.
- Inventory owns item ownership/equip.
- Quest owns quest progress/rewards.
- Data owns load/save.

Bad service boundaries:

- `Main`.
- `Handler`.
- `EverythingService`.
- One 2,000-line script that owns UI, data, combat, and purchases.

## Config Modules

Put shared config in `ReplicatedStorage/Config` when clients can safely read it.

```lua
local Items = {
    BasicSword = {
        DisplayName = "Basic Sword",
        Price = 100,
    },
}

return Items
```

Clients can read display name and price for UI. The server still validates with the same config.

## Remotes

Name remotes by intent:

- `BuyItem`
- `EquipItem`
- `ClaimQuestReward`
- `SelectSaveSlot`

Avoid vague remotes:

- `Event`
- `Action`
- `Update`
- `DoThing`

## Client Controllers

Use `StarterPlayerScripts` for client systems like:

- InputController.
- CameraController.
- SoundController.
- UIController for cross-screen UI routing.

Keep important game truth off the client.

## Folder Review Checklist

- Can a beginner place each file in Studio?
- Is important logic server-side?
- Are configs shared only when safe?
- Are remotes grouped in `ReplicatedStorage/Remotes`?
- Are server-only assets in `ServerStorage`?
- Is UI logic close to the ScreenGui it controls?
- Is there any giant script doing unrelated jobs?

## Mistakes To Avoid

- Putting DataStore code in LocalScripts.
- Storing admin lists in ReplicatedStorage.
- Letting clients clone paid tools from ReplicatedStorage.
- Naming every script `Main`.
- Creating folders because they look professional, not because they clarify ownership.
