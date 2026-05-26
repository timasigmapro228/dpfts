# DPFTS Luau Style Guide

Good Roblox code is boring in the best way: readable, predictable, server-authoritative, and easy to test in Studio.

## Naming Conventions

- Use `PascalCase` for ModuleScripts and service-like tables: `ShopService`, `ProfileStore`.
- Use `camelCase` for local variables and functions: `playerData`, `getItemPrice`.
- Use `UPPER_SNAKE_CASE` for constants: `MAX_PURCHASES_PER_SECOND`.
- Name RemoteEvents by intent: `BuyItem`, `ClaimReward`, `EquipItem`.
- Avoid vague names like `Thing`, `Handler`, `Main`, or `Stuff`.

## ModuleScript Usage

Use ModuleScripts for:

- Shared item configuration.
- Utility functions.
- Server services with clear APIs.
- Data validation helpers.

Do not turn every tiny script into a module. A small one-off Script is fine when it has one job.

## Server/Client Separation

Server owns:

- Currency.
- Inventory.
- Damage.
- Rewards.
- Purchases.
- DataStore saves.
- Anti-exploit validation.

Client owns:

- UI display.
- Input collection.
- Camera effects.
- Local animation and sound feedback.
- Requests to the server.

The client may ask. The server decides.

## Type Annotations

Use Luau type annotations where they make code safer or clearer:

```lua
type ItemConfig = {
    DisplayName: string,
    Price: number,
}
```

Do not bury simple code under type noise. Use types for tables, function boundaries, and data loaded from storage.

## Avoid Giant Scripts

Split code when a script handles unrelated responsibilities:

- UI input.
- Purchase validation.
- Data persistence.
- Reward calculation.
- Inventory mutation.

Do not split just to look professional. Split when the boundary helps.

## Events And Cleanup

- Store connections when they need to be disconnected.
- Disconnect character, UI, and temporary round connections when no longer needed.
- Avoid creating a new connection every time a button opens unless you clean up the old one.

```lua
local connection: RBXScriptConnection?

connection = button.Activated:Connect(function()
    print("Clicked")
end)

-- Later:
if connection then
    connection:Disconnect()
    connection = nil
end
```

## Guard Clauses

Use guard clauses to reject bad state early:

```lua
if typeof(itemId) ~= "string" then
    return false, "Invalid item"
end

local item = ShopConfig.Items[itemId]
if not item then
    return false, "Unknown item"
end
```

## Validation

Validate anything that crosses from client to server:

- Type.
- Existence.
- Price.
- Ownership.
- Cooldown.
- Player state.
- Distance or interaction context when relevant.

## Constants

Use constants for values that affect design or security:

```lua
local PURCHASE_COOLDOWN_SECONDS = 0.5
local MAX_ITEM_ID_LENGTH = 40
```

## Comments

Use comments to explain why, not what:

```lua
-- Use UpdateAsync so concurrent saves merge with the latest stored profile.
```

Avoid comments like:

```lua
-- Set coins to coins
```

## Error Handling

- Wrap DataStore calls in `pcall`.
- Return clear failure reasons from server validation.
- Warn on server failures with enough context to debug.
- Do not expose internal error details to players.

## Folder Placement

Common placements:

- `ServerScriptService`: server-only game logic and services.
- `ReplicatedStorage`: shared config and RemoteEvents.
- `StarterGui`: ScreenGuis and LocalScripts for UI.
- `StarterPlayerScripts`: client controllers not tied to one ScreenGui.
- `ServerStorage`: server-only assets and templates.

## Bad Luau Example

```lua
-- StarterGui/ShopGui/LocalScript
local player = game.Players.LocalPlayer
local coins = player.leaderstats.Coins

script.Parent.BuyButton.MouseButton1Click:Connect(function()
    coins.Value -= 100
    local sword = game.ReplicatedStorage.Tools.BasicSword:Clone()
    sword.Parent = player.Backpack
end)
```

Problems:

- Client edits currency.
- Client grants item.
- No validation.
- No price source.
- No ownership check.
- Easy to exploit.

## Improved Luau Example

```lua
-- ReplicatedStorage/ShopConfig
local ShopConfig = {}

ShopConfig.Items = {
    BasicSword = {
        DisplayName = "Basic Sword",
        Price = 100,
    },
}

return ShopConfig
```

```lua
-- ServerScriptService/ShopService
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local ShopConfig = require(ReplicatedStorage:WaitForChild("ShopConfig"))
local buyItem = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("BuyItem")

local function buy(player: Player, itemId: unknown)
    if typeof(itemId) ~= "string" or #itemId > 40 then
        return false, "Invalid item"
    end

    local item = ShopConfig.Items[itemId]
    if not item then
        return false, "Unknown item"
    end

    local leaderstats = player:FindFirstChild("leaderstats")
    local coins = leaderstats and leaderstats:FindFirstChild("Coins")
    if not coins or not coins:IsA("IntValue") or coins.Value < item.Price then
        return false, "Not enough coins"
    end

    coins.Value -= item.Price
    return true, "Purchased"
end

buyItem.OnServerEvent:Connect(function(player, itemId)
    local ok, message = buy(player, itemId)
    buyItem:FireClient(player, ok, message)
end)
```

```lua
-- StarterGui/ShopGui/LocalScript
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local buyItem = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("BuyItem")
local button = script.Parent:WaitForChild("BuyButton")

button.Activated:Connect(function()
    buyItem:FireServer("BasicSword")
end)

buyItem.OnClientEvent:Connect(function(ok, message)
    print(ok, message)
end)
```

## Checklist Before Giving Code

- Does the server own important state?
- Are remotes validated?
- Are DataStore calls protected with `pcall`?
- Are files small enough to understand?
- Are Studio placements explicit?
- Can a beginner test this in Play Solo and with two players?
- Are failure cases named?
