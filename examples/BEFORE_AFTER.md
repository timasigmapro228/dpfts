# Before And After Examples

These examples show the kind of difference DPFTS is meant to create. The point is not fancy code. The point is server authority, clear placement, and fewer exploit-friendly mistakes.

## RemoteEvent Shop

### Bad AI Output

```lua
-- ServerScriptService/BadShop.server.lua
BuyItem.OnServerEvent:Connect(function(player, itemId, price)
    player.leaderstats.Coins.Value -= price
    giveItem(player, itemId)
end)
```

Problems:

- Client chooses `itemId`.
- Client chooses `price`.
- Negative prices can add coins.
- No ownership check.
- No profile loaded check.
- No server result back to UI.

### DPFTS-Style Output

```lua
-- ServerScriptService/ShopService.server.lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local ShopConfig = require(ReplicatedStorage:WaitForChild("ShopConfig"))
local Remotes = ReplicatedStorage:WaitForChild("Remotes")
local BuyItem = Remotes:WaitForChild("BuyItem")
local BuyResult = Remotes:WaitForChild("BuyResult")

local Profiles = {}

BuyItem.OnServerEvent:Connect(function(player: Player, itemId: unknown)
    if typeof(itemId) ~= "string" then
        BuyResult:FireClient(player, false, "Invalid item.")
        return
    end

    local item = ShopConfig.Items[itemId]
    if not item then
        BuyResult:FireClient(player, false, "Item does not exist.")
        return
    end

    local profile = Profiles[player]
    if not profile then
        BuyResult:FireClient(player, false, "Data is still loading.")
        return
    end

    if profile.Inventory[itemId] then
        BuyResult:FireClient(player, false, "Already owned.")
        return
    end

    if profile.Coins < item.Price then
        BuyResult:FireClient(player, false, "Not enough coins.")
        return
    end

    profile.Coins -= item.Price
    profile.Inventory[itemId] = true

    BuyResult:FireClient(player, true, itemId, profile.Coins)
end)
```

Better:

- Client requests only an item ID.
- Server owns price.
- Server owns currency.
- Server owns inventory.
- Server rejects invalid requests.
- UI receives a clear result.

## DataStore Save

### Bad AI Output

```lua
-- ServerScriptService/BadSave.server.lua
Players.PlayerRemoving:Connect(function(player)
    DataStore:SetAsync(player.UserId, player.leaderstats.Coins.Value)
end)
```

Problems:

- No `pcall`.
- Saves one number instead of a versioned profile.
- No validation.
- No default data.
- No `BindToClose`.
- Can lose errors silently if nobody checks Output.

### DPFTS-Style Output

```lua
-- ServerScriptService/ProfileStore.server.lua
local function saveProfile(player: Player)
    local profile = Profiles[player]
    if not profile then
        return
    end

    local key = `Player_{player.UserId}`
    local success, err = pcall(function()
        ProfileStore:UpdateAsync(key, function()
            return {
                SchemaVersion = 1,
                Coins = math.max(0, profile.Coins),
                Level = math.max(1, profile.Level),
                Inventory = profile.Inventory,
                Settings = profile.Settings,
            }
        end)
    end)

    if not success then
        warn(("[ProfileStore] Save failed for %s: %s"):format(player.Name, tostring(err)))
    end
end
```

Better:

- Save is protected by `pcall`.
- Data shape is explicit.
- Numeric values are clamped.
- Errors are visible during development.
- The pattern can be reused for `PlayerRemoving` and `BindToClose`.

## UI Purchase Feedback

### Bad AI Output

```lua
-- StarterGui/ShopGui/LocalScript
BuyButton.MouseButton1Click:Connect(function()
    BuyItem:FireServer("SpeedBoost", 100)
    StatusLabel.Text = "Purchased!"
end)
```

Problems:

- Client sends price.
- UI claims success before the server confirms.
- No disabled or loading state.
- No failure message.

### DPFTS-Style Output

```lua
-- StarterGui/ShopGui/LocalScript
local pending = false

BuyButton.MouseButton1Click:Connect(function()
    if pending then
        return
    end

    pending = true
    BuyButton.Active = false
    StatusLabel.Text = "Buying..."
    BuyItem:FireServer("SpeedBoost")
end)

BuyResult.OnClientEvent:Connect(function(success: boolean, message: string)
    pending = false
    BuyButton.Active = true
    StatusLabel.Text = message
end)
```

Better:

- Client sends intent only.
- UI waits for server result.
- Button cannot spam while pending.
- Failure can be shown clearly.
