# MarketplaceService Guide

MarketplaceService is for Roblox purchases such as gamepasses and developer products. Treat every purchase as server-side business logic. The client can prompt a purchase, but it cannot prove ownership or grant rewards.

## Gamepasses vs Developer Products

Gamepasses:

- Usually one-time ownership.
- Checked with `UserOwnsGamePassAsync`.
- Good for permanent perks, VIP access, extra slots, and passes.

Developer products:

- Can be bought repeatedly.
- Granted through `ProcessReceipt`.
- Good for coin packs, consumables, boosts, and one-time reward purchases.

## Rules

- Prompt purchases from the client only as UI.
- Check ownership on the server.
- Grant gamepass perks on the server.
- Grant developer products only inside `ProcessReceipt`.
- Never trust a RemoteEvent that says a purchase succeeded.
- Never grant Robux products from a LocalScript.
- Make purchase UI honest about what the player receives.

## Folder Structure

```text
ReplicatedStorage/
|-- MarketplaceConfig (ModuleScript)
`-- Remotes/
    `-- RequestGamepassPerk (RemoteEvent)

ServerScriptService/
`-- MarketplaceServiceHandler (Script)

StarterGui/
`-- ShopGui/
    `-- LocalScript
```

## ReplicatedStorage/MarketplaceConfig

```lua
local MarketplaceConfig = {}

MarketplaceConfig.Gamepasses = {
    VIP = 123456789,
}

MarketplaceConfig.DeveloperProducts = {
    SmallCoinPack = {
        ProductId = 987654321,
        Coins = 500,
    },
}

return MarketplaceConfig
```

## Prompting A Gamepass Purchase

Place this LocalScript in `StarterGui/ShopGui/LocalScript`.

```lua
local MarketplaceService = game:GetService("MarketplaceService")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local MarketplaceConfig = require(ReplicatedStorage:WaitForChild("MarketplaceConfig"))
local player = Players.LocalPlayer

script.Parent.BuyVipButton.Activated:Connect(function()
    MarketplaceService:PromptGamePassPurchase(player, MarketplaceConfig.Gamepasses.VIP)
end)
```

This only opens the purchase prompt. It does not grant VIP.

## Server Gamepass Check

Place this Script in `ServerScriptService/MarketplaceServiceHandler`.

```lua
local MarketplaceService = game:GetService("MarketplaceService")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local MarketplaceConfig = require(ReplicatedStorage:WaitForChild("MarketplaceConfig"))

local function userOwnsGamepass(player: Player, gamepassId: number): boolean
    local ok, ownsPass = pcall(function()
        return MarketplaceService:UserOwnsGamePassAsync(player.UserId, gamepassId)
    end)

    if not ok then
        warn("Failed to check gamepass ownership for", player.UserId, gamepassId)
        return false
    end

    return ownsPass == true
end

local function applyVipPerks(player: Player)
    player:SetAttribute("IsVip", true)
end

Players.PlayerAdded:Connect(function(player)
    if userOwnsGamepass(player, MarketplaceConfig.Gamepasses.VIP) then
        applyVipPerks(player)
    end
end)

MarketplaceService.PromptGamePassPurchaseFinished:Connect(function(player, gamepassId, wasPurchased)
    if gamepassId ~= MarketplaceConfig.Gamepasses.VIP or not wasPurchased then
        return
    end

    if userOwnsGamepass(player, gamepassId) then
        applyVipPerks(player)
    end
end)
```

## Developer Product Receipt Pattern

Grant developer products in `ProcessReceipt`. Roblox may call this more than once, so make rewards idempotent when using DataStores.

```lua
local MarketplaceService = game:GetService("MarketplaceService")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local MarketplaceConfig = require(ReplicatedStorage:WaitForChild("MarketplaceConfig"))

local productById = {}
for _, product in pairs(MarketplaceConfig.DeveloperProducts) do
    productById[product.ProductId] = product
end

local function addCoins(player: Player, amount: number): boolean
    local leaderstats = player:FindFirstChild("leaderstats")
    local coins = leaderstats and leaderstats:FindFirstChild("Coins")

    if not coins or not coins:IsA("IntValue") then
        return false
    end

    coins.Value += amount
    return true
end

MarketplaceService.ProcessReceipt = function(receiptInfo)
    local player = Players:GetPlayerByUserId(receiptInfo.PlayerId)
    if not player then
        return Enum.ProductPurchaseDecision.NotProcessedYet
    end

    local product = productById[receiptInfo.ProductId]
    if not product then
        warn("Unknown developer product", receiptInfo.ProductId)
        return Enum.ProductPurchaseDecision.NotProcessedYet
    end

    if not addCoins(player, product.Coins) then
        return Enum.ProductPurchaseDecision.NotProcessedYet
    end

    return Enum.ProductPurchaseDecision.PurchaseGranted
end
```

For persistent currency, save the receipt ID in the player's profile before returning `PurchaseGranted`.

## UI Rules For Purchases

- Show what the player gets.
- Show whether the item is permanent or consumable.
- Do not fake urgency.
- Do not hide that a button costs Robux.
- Keep paid buttons visually distinct from free reward buttons.
- Do not put paid purchase buttons where players tap constantly.

## Testing Checklist

- Test purchase prompts in Studio with Roblox's testing flow.
- Confirm gamepass perks are applied on rejoin.
- Confirm canceling a prompt grants nothing.
- Confirm client RemoteEvents cannot grant paid rewards.
- Confirm developer products grant only through `ProcessReceipt`.
- Confirm unknown product IDs warn and do not grant rewards.

## Common Mistakes

- LocalScript grants VIP after prompt closes.
- RemoteEvent tells server "purchase succeeded."
- Developer product reward is granted before `ProcessReceipt`.
- Receipt handling does not return `NotProcessedYet` when player data is unavailable.
- Gamepass ownership check has no `pcall`.
- Paid UI looks the same as free claim UI.
