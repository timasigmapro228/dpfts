# Save Slots Recipe

This recipe shows a simple DataStore shape for multiple save slots.

Save slots are useful for tycoons, RPGs, build games, and experiments where players may want separate progress. Keep the number of slots small and validate the slot ID every time.

## What The System Does

- Allows a player to use slot `1`, `2`, or `3`.
- Loads a profile for the selected slot.
- Saves coins and level.
- Uses `pcall`.
- Validates loaded data.

## Data Shape

```lua
local DEFAULT_SLOT_DATA = {
    SchemaVersion = 1,
    Coins = 0,
    Level = 1,
}
```

Key format:

```text
Player_<UserId>_Slot_<SlotId>
```

## ServerScriptService/SaveSlotService

```lua
local DataStoreService = game:GetService("DataStoreService")
local Players = game:GetService("Players")

local saveStore = DataStoreService:GetDataStore("PlayerSaveSlots_v1")

local VALID_SLOTS = {
    [1] = true,
    [2] = true,
    [3] = true,
}

local DEFAULT_SLOT_DATA = {
    SchemaVersion = 1,
    Coins = 0,
    Level = 1,
}

local activeSlotByPlayer: { [Player]: number } = {}
local dataByPlayer: { [Player]: { SchemaVersion: number, Coins: number, Level: number } } = {}

local function copyDefault()
    return {
        SchemaVersion = DEFAULT_SLOT_DATA.SchemaVersion,
        Coins = DEFAULT_SLOT_DATA.Coins,
        Level = DEFAULT_SLOT_DATA.Level,
    }
end

local function validateSlotId(slotId: unknown): number?
    if typeof(slotId) ~= "number" then
        return nil
    end

    local safeSlotId = math.floor(slotId)
    if VALID_SLOTS[safeSlotId] then
        return safeSlotId
    end

    return nil
end

local function validateData(data)
    if type(data) ~= "table" then
        return copyDefault()
    end

    local clean = copyDefault()

    if type(data.SchemaVersion) == "number" then
        clean.SchemaVersion = data.SchemaVersion
    end

    if type(data.Coins) == "number" and data.Coins >= 0 then
        clean.Coins = math.floor(data.Coins)
    end

    if type(data.Level) == "number" and data.Level >= 1 then
        clean.Level = math.floor(data.Level)
    end

    return clean
end

local function getKey(player: Player, slotId: number): string
    return "Player_" .. player.UserId .. "_Slot_" .. slotId
end

local function loadSlot(player: Player, slotId: number)
    local key = getKey(player, slotId)

    local ok, result = pcall(function()
        return saveStore:GetAsync(key)
    end)

    if not ok then
        warn("Failed to load slot", player.UserId, slotId, result)
        return copyDefault()
    end

    return validateData(result)
end

local function saveSlot(player: Player)
    local slotId = activeSlotByPlayer[player]
    local data = dataByPlayer[player]

    if not slotId or not data then
        return
    end

    local key = getKey(player, slotId)
    local dataToSave = validateData(data)

    local ok, result = pcall(function()
        saveStore:UpdateAsync(key, function()
            return dataToSave
        end)
    end)

    if not ok then
        warn("Failed to save slot", player.UserId, slotId, result)
    end
end

local function selectSlot(player: Player, requestedSlotId: unknown): (boolean, string)
    local slotId = validateSlotId(requestedSlotId)
    if not slotId then
        return false, "Invalid slot"
    end

    saveSlot(player)

    activeSlotByPlayer[player] = slotId
    dataByPlayer[player] = loadSlot(player, slotId)

    return true, "Loaded slot " .. slotId
end

Players.PlayerAdded:Connect(function(player)
    selectSlot(player, 1)
end)

Players.PlayerRemoving:Connect(function(player)
    saveSlot(player)
    activeSlotByPlayer[player] = nil
    dataByPlayer[player] = nil
end)

game:BindToClose(function()
    for _, player in ipairs(Players:GetPlayers()) do
        saveSlot(player)
    end
end)
```

## How To Connect UI

Use a RemoteEvent such as `SelectSaveSlot`. The client sends only the requested slot number. The server validates the slot and loads it.

Never let the client send the full save data.

## Testing Steps

- Join and confirm slot 1 loads by default.
- Select slot 2 and confirm slot 1 saves first.
- Try slot 999 and confirm denial.
- Try string slot `"1"` and confirm denial unless you explicitly support strings.
- Rejoin and confirm slot data persists.
- Test leaving during slot switching.

## Mistakes To Avoid

- Unlimited client-selected slot IDs.
- Client sends profile data.
- Slot switch does not save current slot.
- Slot data has no schema version.
- Two systems write different shapes to the same slot key.
