# Inventory Recipe

This recipe builds a simple server-authoritative inventory with item ownership and equip requests.

It is intentionally small. It does not include trading, persistence, item models, or rarity effects. Add those after the base ownership rules are safe.

## What The System Does

- Stores owned item IDs on the server.
- Lets the client request equipping an item.
- Server validates that the item exists and is owned.
- Server stores the equipped item.
- Server sends an inventory snapshot to the client.

## Folder Structure

```text
ReplicatedStorage/
|-- ItemConfig (ModuleScript)
`-- Remotes/
    |-- EquipItem (RemoteEvent)
    |-- RequestInventory (RemoteEvent)
    `-- InventoryUpdated (RemoteEvent)

ServerScriptService/
`-- InventoryService (Script)

StarterGui/
`-- InventoryGui/
    |-- EquipBasicSwordButton (TextButton)
    |-- StatusLabel (TextLabel)
    `-- LocalScript
```

## ReplicatedStorage/ItemConfig

```lua
local ItemConfig = {}

ItemConfig.Items = {
    BasicSword = {
        DisplayName = "Basic Sword",
        Slot = "Weapon",
    },
    RedTrail = {
        DisplayName = "Red Trail",
        Slot = "Trail",
    },
}

return ItemConfig
```

## ServerScriptService/InventoryService

```lua
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local ItemConfig = require(ReplicatedStorage:WaitForChild("ItemConfig"))
local remotes = ReplicatedStorage:WaitForChild("Remotes")
local equipItem = remotes:WaitForChild("EquipItem")
local requestInventory = remotes:WaitForChild("RequestInventory")
local inventoryUpdated = remotes:WaitForChild("InventoryUpdated")

local inventoriesByPlayer: { [Player]: { Owned: { [string]: boolean }, Equipped: { [string]: string } } } = {}

local function createStarterInventory()
    return {
        Owned = {
            BasicSword = true,
        },
        Equipped = {},
    }
end

local function makeSnapshot(player: Player)
    local inventory = inventoriesByPlayer[player]
    if not inventory then
        return nil
    end

    return {
        Owned = inventory.Owned,
        Equipped = inventory.Equipped,
    }
end

local function sendSnapshot(player: Player)
    local snapshot = makeSnapshot(player)
    if snapshot then
        inventoryUpdated:FireClient(player, snapshot)
    end
end

local function equip(player: Player, itemId: unknown): (boolean, string)
    if typeof(itemId) ~= "string" or #itemId > 40 then
        return false, "Invalid item"
    end

    local item = ItemConfig.Items[itemId]
    if not item then
        return false, "Unknown item"
    end

    local inventory = inventoriesByPlayer[player]
    if not inventory or not inventory.Owned[itemId] then
        return false, "Not owned"
    end

    inventory.Equipped[item.Slot] = itemId
    sendSnapshot(player)

    return true, "Equipped " .. item.DisplayName
end

Players.PlayerAdded:Connect(function(player)
    inventoriesByPlayer[player] = createStarterInventory()
end)

Players.PlayerRemoving:Connect(function(player)
    inventoriesByPlayer[player] = nil
end)

equipItem.OnServerEvent:Connect(function(player, itemId)
    local ok, message = equip(player, itemId)
    equipItem:FireClient(player, ok, message)
end)

requestInventory.OnServerEvent:Connect(function(player)
    sendSnapshot(player)
end)
```

## StarterGui/InventoryGui/LocalScript

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local remotes = ReplicatedStorage:WaitForChild("Remotes")
local equipItem = remotes:WaitForChild("EquipItem")
local requestInventory = remotes:WaitForChild("RequestInventory")
local inventoryUpdated = remotes:WaitForChild("InventoryUpdated")

local gui = script.Parent
local equipButton = gui:WaitForChild("EquipBasicSwordButton")
local statusLabel = gui:WaitForChild("StatusLabel")

local latestInventory = nil

equipButton.Activated:Connect(function()
    equipButton.Active = false
    statusLabel.Text = "Equipping..."
    equipItem:FireServer("BasicSword")
end)

equipItem.OnClientEvent:Connect(function(ok: boolean, message: string)
    equipButton.Active = true
    statusLabel.Text = message
end)

inventoryUpdated.OnClientEvent:Connect(function(snapshot)
    latestInventory = snapshot
    print("Inventory updated", latestInventory)
end)

requestInventory:FireServer()
```

## Connecting To DataStore Later

Save:

- `Owned` item IDs.
- `Equipped` slot item IDs.

Do not save:

- Item display names.
- Prices.
- Client UI state.

On load, validate saved item IDs against `ItemConfig` before using them.

## Testing Steps

- Create the remotes in `ReplicatedStorage/Remotes`.
- Join and confirm the server gives `BasicSword`.
- Confirm the client receives an inventory snapshot after it requests one.
- Click equip and confirm the server returns success.
- Change the client request to `RedTrail` and confirm the server returns `Not owned`.
- Send a fake item ID and confirm no equip happens.
- Test with two players and confirm inventories are separate.

## Mistakes To Avoid

- Client owns the inventory table.
- Client decides what is equipped.
- Saved inventory stores full item config copies.
- Equip RemoteEvent accepts any string without checking ownership.
- Item config lives only in a LocalScript.
- Trading is added before ownership is safe.
