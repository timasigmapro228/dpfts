# DataStore Guide

Roblox DataStores are for player progress that must survive sessions. They are not instant local variables. Treat them as slow, failure-prone storage and design around that.

## What To Save

Save durable progress:

- Coins.
- Level.
- Inventory.
- Settings.
- Owned items.
- Unlocks.
- Last reward claim time.

## What Not To Save

Do not save:

- Temporary round state.
- Character health.
- Position unless the game truly needs it.
- UI open/closed state.
- Values the server can recalculate.
- Huge tables of unnecessary detail.

## Rules

- Use `pcall` for every DataStore call.
- Validate loaded data before trusting it.
- Use default data for new or broken profiles.
- Use schema versioning.
- Save on `PlayerRemoving`.
- Use `BindToClose` for shutdown saves.
- Autosave occasionally, not constantly.
- Avoid too many requests.
- Prefer `UpdateAsync` when merging with existing stored data.

## UpdateAsync vs SetAsync

`SetAsync` overwrites the stored value. It is simple, but risky if two servers touch the same key.

`UpdateAsync` reads the latest value and writes a transformed value. It is safer than blind overwrites, but a serious production profile system should still add session locking if the same player can be loaded in more than one server.

For most player profile saves, prefer `UpdateAsync`.

## Example Profile

```lua
local DEFAULT_PROFILE = {
    SchemaVersion = 1,
    Coins = 0,
    Level = 1,
    Inventory = {},
    Settings = {
        MusicEnabled = true,
        SfxEnabled = true,
    },
}
```

## ServerScriptService/ProfileService

Place this Script in `ServerScriptService/ProfileService`.

```lua
local DataStoreService = game:GetService("DataStoreService")
local Players = game:GetService("Players")

local profileStore = DataStoreService:GetDataStore("PlayerProfiles_v1")
local profiles: {[Player]: any} = {}
local savingPlayers: {[Player]: boolean} = {}

local MAX_INVENTORY_ITEMS = 100
local MAX_ITEM_ID_LENGTH = 40

local DEFAULT_PROFILE = {
    SchemaVersion = 1,
    Coins = 0,
    Level = 1,
    Inventory = {},
    Settings = {
        MusicEnabled = true,
        SfxEnabled = true,
    },
}

local function deepCopy(value)
    if type(value) ~= "table" then
        return value
    end

    local copy = {}
    for key, child in pairs(value) do
        copy[key] = deepCopy(child)
    end
    return copy
end

local function validateProfile(data)
    if type(data) ~= "table" then
        return deepCopy(DEFAULT_PROFILE)
    end

    local profile = deepCopy(DEFAULT_PROFILE)

    if type(data.SchemaVersion) == "number" then
        profile.SchemaVersion = data.SchemaVersion
    end

    if type(data.Coins) == "number" and data.Coins >= 0 then
        profile.Coins = math.floor(data.Coins)
    end

    if type(data.Level) == "number" and data.Level >= 1 then
        profile.Level = math.floor(data.Level)
    end

    if type(data.Inventory) == "table" then
        local itemCount = 0

        for itemId, owned in pairs(data.Inventory) do
            if itemCount >= MAX_INVENTORY_ITEMS then
                break
            end

            if type(itemId) == "string" and #itemId <= MAX_ITEM_ID_LENGTH and owned == true then
                profile.Inventory[itemId] = true
                itemCount += 1
            end
        end
    end

    if type(data.Settings) == "table" then
        if type(data.Settings.MusicEnabled) == "boolean" then
            profile.Settings.MusicEnabled = data.Settings.MusicEnabled
        end
        if type(data.Settings.SfxEnabled) == "boolean" then
            profile.Settings.SfxEnabled = data.Settings.SfxEnabled
        end
    end

    return profile
end

local function getKey(player: Player): string
    return "Player_" .. player.UserId
end

local function createLeaderstats(player: Player, profile)
    local leaderstats = Instance.new("Folder")
    leaderstats.Name = "leaderstats"
    leaderstats.Parent = player

    local coins = Instance.new("IntValue")
    coins.Name = "Coins"
    coins.Value = profile.Coins
    coins.Parent = leaderstats

    local level = Instance.new("IntValue")
    level.Name = "Level"
    level.Value = profile.Level
    level.Parent = leaderstats
end

local function syncFromLeaderstats(player: Player)
    local profile = profiles[player]
    if not profile then
        return
    end

    local leaderstats = player:FindFirstChild("leaderstats")
    if not leaderstats then
        return
    end

    local coins = leaderstats:FindFirstChild("Coins")
    local level = leaderstats:FindFirstChild("Level")

    if coins and coins:IsA("IntValue") then
        profile.Coins = coins.Value
    end
    if level and level:IsA("IntValue") then
        profile.Level = level.Value
    end
end

local function loadProfile(player: Player)
    local key = getKey(player)
    local ok, result = pcall(function()
        return profileStore:GetAsync(key)
    end)

    if not ok then
        warn("Failed to load profile for", player.UserId, result)
        return deepCopy(DEFAULT_PROFILE)
    end

    return validateProfile(result)
end

local function saveProfile(player: Player)
    if savingPlayers[player] then
        local startedWaiting = os.clock()
        while savingPlayers[player] and os.clock() - startedWaiting < 5 do
            task.wait()
        end

        if savingPlayers[player] then
            warn("Timed out waiting for active save for", player.UserId)
            return
        end
    end

    local profile = profiles[player]
    if not profile then
        return
    end

    savingPlayers[player] = true
    syncFromLeaderstats(player)

    local key = getKey(player)
    local dataToSave = validateProfile(profile)

    local ok, result = pcall(function()
        profileStore:UpdateAsync(key, function(_storedProfile)
            return dataToSave
        end)
    end)

    savingPlayers[player] = nil

    if not ok then
        warn("Failed to save profile for", player.UserId, result)
    end
end

Players.PlayerAdded:Connect(function(player)
    local profile = loadProfile(player)
    profiles[player] = profile
    createLeaderstats(player, profile)
end)

Players.PlayerRemoving:Connect(function(player)
    saveProfile(player)
    profiles[player] = nil
    savingPlayers[player] = nil
end)

task.spawn(function()
    while true do
        task.wait(120)
        for _, player in ipairs(Players:GetPlayers()) do
            saveProfile(player)
        end
    end
end)

game:BindToClose(function()
    for _, player in ipairs(Players:GetPlayers()) do
        saveProfile(player)
    end
end)
```

## Schema Versioning

Keep `SchemaVersion` in the profile. When the data shape changes later, migrate old profiles:

```lua
if profile.SchemaVersion < 2 then
    profile.Settings.ShowDamageNumbers = true
    profile.SchemaVersion = 2
end
```

## Avoid Too Many Requests

- Do not save every coin pickup.
- Update in-memory profile during play.
- Autosave every 60 to 180 seconds.
- Save on leave and shutdown.
- Batch related changes into one profile.

## Testing Steps

- Enable Studio API access for DataStores in Game Settings.
- Join in Studio and confirm default Coins and Level appear.
- Change Coins during play and leave.
- Rejoin and confirm values load.
- Temporarily break the DataStore name and confirm the server warns instead of crashing.
- Test with two players.

## Common Failure Cases

- API access disabled in Studio.
- Saving too often and hitting request limits.
- Loaded data has the wrong type.
- Player leaves before profile finishes loading.
- Server shuts down before saves finish.
- Two scripts write different profile shapes to the same key.
- Client tries to edit saved values directly.
