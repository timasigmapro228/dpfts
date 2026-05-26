# RemoteEvents And RemoteFunctions

RemoteEvents and RemoteFunctions let the client and server communicate. They are necessary in Roblox, but they are also where many exploitable systems begin.

The rule is simple: the client may request; the server must validate.

## What RemoteEvents Are For

Use RemoteEvents for:

- UI button requests.
- Tool activation requests.
- Client input that needs server approval.
- Server updates back to one player or all players.
- Non-blocking communication.

Use RemoteFunctions sparingly for request/response flows that truly need an immediate return value. RemoteFunctions can yield, so bad use can make systems harder to debug.

## What Should Be Server-Side

Keep these on the server:

- Currency changes.
- Inventory changes.
- Damage.
- Rewards.
- Purchases.
- Cooldowns that matter.
- Ownership.
- Admin actions.
- DataStore writes.

## What Can Be Client-Side

The client can handle:

- Button clicks.
- UI opening and closing.
- Local loading states.
- Sound and animation feedback.
- Camera effects.
- Displaying server-approved results.

## Why Trusting The Client Is Dangerous

Exploiters can fire RemoteEvents manually with custom arguments. If the server accepts `FireServer("GiveMeCoins", 999999)`, the system is already broken.

Never treat a client argument as truth. Treat it as an untrusted request.

## Folder Structure

Create remotes in:

```text
ReplicatedStorage/
`-- Remotes/
    `-- BuyItem (RemoteEvent)
```

You can create this manually in Studio or with a short setup script.

```lua
-- ServerScriptService/CreateRemotes.server.lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local remotes = ReplicatedStorage:FindFirstChild("Remotes")
if not remotes then
    remotes = Instance.new("Folder")
    remotes.Name = "Remotes"
    remotes.Parent = ReplicatedStorage
end

if not remotes:FindFirstChild("BuyItem") then
    local buyItem = Instance.new("RemoteEvent")
    buyItem.Name = "BuyItem"
    buyItem.Parent = remotes
end
```

## Safe RemoteEvent Pattern

1. Client sends the smallest request possible.
2. Server checks type and value.
3. Server looks up config itself.
4. Server checks player state.
5. Server applies the result.
6. Server sends success/failure feedback.

## Unsafe RemoteEvent Pattern

```lua
-- Bad: client sends price and server trusts it
BuyItem:FireServer("BasicSword", 1)
```

```lua
-- Bad: ServerScriptService
BuyItem.OnServerEvent:Connect(function(player, itemId, price)
    player.leaderstats.Coins.Value -= price
    giveItem(player, itemId)
end)
```

Problems:

- Client controls price.
- No item existence check.
- No ownership check.
- No currency validation.
- No rate limiting.

## Validation Rules

Validate:

- Argument types with `typeof`.
- String length for IDs.
- Item existence in server/shared config.
- Player has enough currency.
- Player does not already own unique item.
- Player is allowed to perform the action now.
- Rate limit is not exceeded.
- The request matches the player's current state.

## Rate Limiting Concept

Rate limiting prevents spam. It does not replace validation.

```lua
local lastRequestByPlayer: {[Player]: number} = {}
local COOLDOWN_SECONDS = 0.5

local function isRateLimited(player: Player): boolean
    local now = os.clock()
    local lastRequest = lastRequestByPlayer[player]

    if lastRequest and now - lastRequest < COOLDOWN_SECONDS then
        return true
    end

    lastRequestByPlayer[player] = now
    return false
end
```

Clean up:

```lua
game.Players.PlayerRemoving:Connect(function(player)
    lastRequestByPlayer[player] = nil
end)
```

## Example: Buying An Item

The client requests buying an item. The server validates price, currency, item existence, ownership, and then updates data.

### ReplicatedStorage/ShopConfig

```lua
local ShopConfig = {}

ShopConfig.Items = {
    BasicSword = {
        DisplayName = "Basic Sword",
        Price = 100,
        Unique = true,
    },
}

return ShopConfig
```

### ServerScriptService/ShopService

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

local function canRequest(player: Player): boolean
    local now = os.clock()
    local last = lastPurchaseByPlayer[player]

    if last and now - last < PURCHASE_COOLDOWN_SECONDS then
        return false
    end

    lastPurchaseByPlayer[player] = now
    return true
end

local function buy(player: Player, itemId: unknown): (boolean, string)
    if not canRequest(player) then
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
    if not coins or coins.Value < item.Price then
        return false, "Not enough coins"
    end

    coins.Value -= item.Price
    owned[itemId] = true

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

### StarterGui/ShopGui/LocalScript

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local buyItem = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("BuyItem")
local buyButton = script.Parent:WaitForChild("BuyButton")
local statusLabel = script.Parent:WaitForChild("StatusLabel")

local isPending = false

buyButton.Activated:Connect(function()
    if isPending then
        return
    end

    isPending = true
    buyButton.Active = false
    statusLabel.Text = "Buying..."

    buyItem:FireServer("BasicSword")
end)

buyItem.OnClientEvent:Connect(function(ok: boolean, message: string)
    isPending = false
    buyButton.Active = true
    statusLabel.Text = message
end)
```

## Testing Checklist

- Try buying with enough coins.
- Try buying without enough coins.
- Try buying the same unique item twice.
- Try firing the remote quickly.
- Try sending a bad item ID.
- Test with two players.
- Confirm only the server changes coins and ownership.

## Common Mistakes

- Client sends the price.
- Client sends the reward amount.
- Server never checks ownership.
- Server assumes leaderstats exist.
- RemoteEvent has no rate limit.
- RemoteFunction used for everything.
- Server returns internal error details to the client.
