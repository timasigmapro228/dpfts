# Shop System Recipe

This recipe builds a simple server-authoritative coin shop.

## What The System Does

- Shows a shop button in UI.
- Client requests to buy an item.
- Server validates the item, price, currency, ownership, and cooldown.
- Server validates item availability before changing currency.
- Server subtracts coins, records ownership, and rolls back if granting fails.
- Server sends a result back to the client.

This starter recipe uses `leaderstats.Coins` for currency and in-memory ownership. Connect it to a DataStore later for permanent inventory.

## Folder Structure In Roblox Studio

```text
ReplicatedStorage/
|-- ShopConfig (ModuleScript)
`-- Remotes/
    `-- BuyItem (RemoteEvent)

ServerScriptService/
`-- ShopService (Script)

StarterGui/
`-- ShopGui/
    |-- BuyButton (TextButton)
    |-- StatusLabel (TextLabel)
    `-- LocalScript
```

## Server-Authoritative Design

The client only sends `itemId`. The server owns:

- Price.
- Currency check.
- Ownership check.
- Purchase cooldown.
- Item granting.

Mutation rule:

- Validate everything first.
- Change currency and ownership together.
- If item granting can fail, roll back the currency and ownership changes.
- Do not clone/grant the item before the purchase state is ready.

## ReplicatedStorage/ShopConfig

Create a ModuleScript named `ShopConfig` in `ReplicatedStorage`.

```lua
local ShopConfig = {}

ShopConfig.Items = {
    BasicSword = {
        DisplayName = "Basic Sword",
        Price = 100,
        Unique = true,
    },

    SpeedBoost = {
        DisplayName = "Speed Boost",
        Price = 250,
        Unique = false,
    },
}

return ShopConfig
```

## ReplicatedStorage/Remotes/BuyItem

Create a Folder named `Remotes` in `ReplicatedStorage`.

Inside it, create a RemoteEvent named `BuyItem`.

## ServerScriptService/ShopService

Create a Script named `ShopService` in `ServerScriptService`.

```lua
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local ShopConfig = require(ReplicatedStorage:WaitForChild("ShopConfig"))
local buyItem = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("BuyItem")

local PURCHASE_COOLDOWN_SECONDS = 0.5

local lastPurchaseByPlayer: {[Player]: number} = {}
local ownedItemsByPlayer: {[Player]: {[string]: boolean}} = {}

local function getCoins(player: Player): IntValue?
    local leaderstats = player:FindFirstChild("leaderstats")
    if not leaderstats then
        return nil
    end

    local coins = leaderstats:FindFirstChild("Coins")
    if coins and coins:IsA("IntValue") then
        return coins
    end

    return nil
end

local function isRateLimited(player: Player): boolean
    local now = os.clock()
    local last = lastPurchaseByPlayer[player]

    if last and now - last < PURCHASE_COOLDOWN_SECONDS then
        return true
    end

    lastPurchaseByPlayer[player] = now
    return false
end

local function canGrantItem(itemId: string): boolean
    -- For real Tool shops, check ServerStorage here before mutating currency.
    -- This starter recipe keeps grants abstract, so every configured item is grantable.
    return ShopConfig.Items[itemId] ~= nil
end

local function grantItem(player: Player, itemId: string): boolean
    -- Grant the item here. For tools, clone from ServerStorage after validation.
    -- Return false if cloning or ownership mutation fails.
    return canGrantItem(itemId)
end

local function buy(player: Player, itemId: unknown): (boolean, string)
    if isRateLimited(player) then
        return false, "Slow down"
    end

    if typeof(itemId) ~= "string" or #itemId > 40 then
        return false, "Invalid item"
    end

    local item = ShopConfig.Items[itemId]
    if not item then
        return false, "Unknown item"
    end

    local owned = ownedItemsByPlayer[player]
    if not owned then
        owned = {}
        ownedItemsByPlayer[player] = owned
    end

    if item.Unique and owned[itemId] then
        return false, "Already owned"
    end

    local coins = getCoins(player)
    if not coins then
        return false, "Coins not ready"
    end

    if coins.Value < item.Price then
        return false, "Not enough coins"
    end

    if not canGrantItem(itemId) then
        return false, "Item unavailable"
    end

    coins.Value -= item.Price
    owned[itemId] = true

    if not grantItem(player, itemId) then
        coins.Value += item.Price
        owned[itemId] = nil
        return false, "Purchase failed"
    end

    return true, "Purchased " .. item.DisplayName
end

buyItem.OnServerEvent:Connect(function(player, itemId)
    local ok, message = buy(player, itemId)
    buyItem:FireClient(player, ok, message)
end)

Players.PlayerRemoving:Connect(function(player)
    lastPurchaseByPlayer[player] = nil
    ownedItemsByPlayer[player] = nil
end)
```

## StarterGui/ShopGui/LocalScript

Create a `ScreenGui` named `ShopGui` in `StarterGui`. Add a `TextButton` named `BuyButton`, a `TextLabel` named `StatusLabel`, and this LocalScript.

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local buyItem = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("BuyItem")

local gui = script.Parent
local buyButton = gui:WaitForChild("BuyButton")
local statusLabel = gui:WaitForChild("StatusLabel")

local selectedItemId = "BasicSword"
local isPending = false

local function setPending(pending: boolean)
    isPending = pending
    buyButton.Active = not pending
    buyButton.AutoButtonColor = not pending
    buyButton.Text = pending and "Buying..." or "Buy"
end

buyButton.Activated:Connect(function()
    if isPending then
        return
    end

    setPending(true)
    statusLabel.Text = ""
    buyItem:FireServer(selectedItemId)
end)

buyItem.OnClientEvent:Connect(function(ok: boolean, message: string)
    setPending(false)
    statusLabel.Text = message

    if ok then
        statusLabel.TextColor3 = Color3.fromRGB(80, 220, 120)
    else
        statusLabel.TextColor3 = Color3.fromRGB(255, 120, 120)
    end
end)
```

## How To Test

- Add `leaderstats.Coins` to the player or use the leaderboard recipe.
- Give yourself at least 100 coins.
- Click Buy and confirm coins decrease on the server.
- Confirm the item can be granted before coins are spent.
- Try buying with too few coins.
- Try buying the same unique item twice.
- Spam the Buy button and confirm cooldown response.
- Test with two players and confirm purchases do not affect each other.
- Temporarily make `grantItem` return `false` and confirm coins/ownership roll back.

## How To Extend

- Gamepasses later: use `MarketplaceService` on the server and keep Robux purchase checks separate from coin purchases.
- Limited items: add stock counts on the server and validate before purchase.
- Categories: add `Category` fields to `ShopConfig`.
- Owned items: store ownership in a DataStore profile instead of in-memory tables.
- Item granting: check the Tool exists in `ServerStorage` before mutation, then clone after currency/ownership is ready. Roll back if the grant fails.

## Mistakes To Avoid

- Client sends the price.
- Client subtracts coins.
- Client grants the Tool.
- Shop UI has no loading or failure state.
- Server does not check ownership.
- Server stores permanent ownership only in memory.
- RemoteEvent has no cooldown.
- Server grants the item before validating availability.
- Server deducts currency without rollback when the grant fails.
