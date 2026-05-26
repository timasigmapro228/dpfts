# Trading Recipe

Trading is one of the easiest systems to exploit or duplicate items in. Keep it server-authoritative, two-sided, and boring.

This recipe builds a simple safe trade flow. It does not include UI polish or DataStore persistence.

## What The System Does

- Player A sends a trade request.
- Player B accepts.
- Both players choose item IDs.
- Both players confirm.
- Server verifies ownership at the final moment.
- Server swaps ownership.

## Folder Structure

```text
ReplicatedStorage/
`-- Remotes/
    |-- TradeRequest (RemoteEvent)
    |-- TradeOfferItem (RemoteEvent)
    |-- TradeConfirm (RemoteEvent)
    `-- TradeUpdated (RemoteEvent)

ServerScriptService/
`-- TradingService (Script)
```

## ServerScriptService/TradingService

```lua
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local remotes = ReplicatedStorage:WaitForChild("Remotes")
local tradeRequest = remotes:WaitForChild("TradeRequest")
local tradeOfferItem = remotes:WaitForChild("TradeOfferItem")
local tradeConfirm = remotes:WaitForChild("TradeConfirm")
local tradeUpdated = remotes:WaitForChild("TradeUpdated")

local inventoriesByPlayer: { [Player]: { [string]: boolean } } = {}
local activeTradeByPlayer: { [Player]: any } = {}

local function ownsItem(player: Player, itemId: string): boolean
    local inventory = inventoriesByPlayer[player]
    return inventory ~= nil and inventory[itemId] == true
end

local function sendTradeUpdate(trade)
    tradeUpdated:FireClient(trade.PlayerA, trade)
    tradeUpdated:FireClient(trade.PlayerB, trade)
end

local function cancelTrade(player: Player)
    local trade = activeTradeByPlayer[player]
    if not trade then
        return
    end

    activeTradeByPlayer[trade.PlayerA] = nil
    activeTradeByPlayer[trade.PlayerB] = nil
end

local function startTrade(player: Player, targetName: unknown): (boolean, string)
    if typeof(targetName) ~= "string" or #targetName > 20 then
        return false, "Invalid target"
    end

    local target = Players:FindFirstChild(targetName)
    if not target or not target:IsA("Player") or target == player then
        return false, "Target not found"
    end

    if activeTradeByPlayer[player] or activeTradeByPlayer[target] then
        return false, "Player already trading"
    end

    local trade = {
        PlayerA = player,
        PlayerB = target,
        OfferA = nil,
        OfferB = nil,
        ConfirmedA = false,
        ConfirmedB = false,
    }

    activeTradeByPlayer[player] = trade
    activeTradeByPlayer[target] = trade
    sendTradeUpdate(trade)

    return true, "Trade started"
end

local function offerItem(player: Player, itemId: unknown): (boolean, string)
    if typeof(itemId) ~= "string" or #itemId > 40 then
        return false, "Invalid item"
    end

    local trade = activeTradeByPlayer[player]
    if not trade then
        return false, "No active trade"
    end

    if not ownsItem(player, itemId) then
        return false, "Item not owned"
    end

    if player == trade.PlayerA then
        trade.OfferA = itemId
        trade.ConfirmedA = false
        trade.ConfirmedB = false
    else
        trade.OfferB = itemId
        trade.ConfirmedA = false
        trade.ConfirmedB = false
    end

    sendTradeUpdate(trade)
    return true, "Offer updated"
end

local function finishTrade(trade): (boolean, string)
    local itemA = trade.OfferA
    local itemB = trade.OfferB

    if not itemA or not itemB then
        return false, "Both players must offer an item"
    end

    if not ownsItem(trade.PlayerA, itemA) or not ownsItem(trade.PlayerB, itemB) then
        return false, "Ownership changed"
    end

    inventoriesByPlayer[trade.PlayerA][itemA] = nil
    inventoriesByPlayer[trade.PlayerB][itemB] = nil
    inventoriesByPlayer[trade.PlayerA][itemB] = true
    inventoriesByPlayer[trade.PlayerB][itemA] = true

    activeTradeByPlayer[trade.PlayerA] = nil
    activeTradeByPlayer[trade.PlayerB] = nil

    return true, "Trade complete"
end

local function confirm(player: Player): (boolean, string)
    local trade = activeTradeByPlayer[player]
    if not trade then
        return false, "No active trade"
    end

    if player == trade.PlayerA then
        trade.ConfirmedA = true
    else
        trade.ConfirmedB = true
    end

    if trade.ConfirmedA and trade.ConfirmedB then
        return finishTrade(trade)
    end

    sendTradeUpdate(trade)
    return true, "Waiting for other player"
end

Players.PlayerAdded:Connect(function(player)
    inventoriesByPlayer[player] = {
        BasicSword = true,
    }
end)

Players.PlayerRemoving:Connect(function(player)
    cancelTrade(player)
    inventoriesByPlayer[player] = nil
end)

tradeRequest.OnServerEvent:Connect(function(player, targetName)
    local ok, message = startTrade(player, targetName)
    tradeRequest:FireClient(player, ok, message)
end)

tradeOfferItem.OnServerEvent:Connect(function(player, itemId)
    local ok, message = offerItem(player, itemId)
    tradeOfferItem:FireClient(player, ok, message)
end)

tradeConfirm.OnServerEvent:Connect(function(player)
    local ok, message = confirm(player)
    tradeConfirm:FireClient(player, ok, message)
end)
```

## Trading Rules

- Revalidate ownership at confirmation time.
- Reset confirmation when either offer changes.
- Cancel trades when a player leaves.
- Do not allow trading locked or equipped-only items unless explicitly supported.
- Save both inventories carefully if using DataStores.

## Mistakes To Avoid

- Client swaps inventory locally.
- Client decides trade is complete.
- Confirmation does not reset after offer changes.
- Item ownership is not rechecked at the final moment.
- Player can trade with themselves.
- Leaving during trade duplicates items.
