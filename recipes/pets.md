# Pets Recipe

This recipe builds a simple server-owned pet system with ownership and equip state.

Pets are social status, progression, and cosmetics. Keep ownership on the server. Do not let the client invent pets.

## Folder Structure

```text
ReplicatedStorage/
|-- PetConfig (ModuleScript)
`-- Remotes/
    |-- EquipPet (RemoteEvent)
    `-- PetsUpdated (RemoteEvent)

ServerScriptService/
`-- PetService (Script)
```

## ReplicatedStorage/PetConfig

```lua
local PetConfig = {}

PetConfig.Pets = {
    StarterCat = {
        DisplayName = "Starter Cat",
        Rarity = "Common",
    },
    BlueDragon = {
        DisplayName = "Blue Dragon",
        Rarity = "Rare",
    },
}

return PetConfig
```

## ServerScriptService/PetService

```lua
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local PetConfig = require(ReplicatedStorage:WaitForChild("PetConfig"))
local remotes = ReplicatedStorage:WaitForChild("Remotes")
local equipPet = remotes:WaitForChild("EquipPet")
local petsUpdated = remotes:WaitForChild("PetsUpdated")

local petsByPlayer: { [Player]: { Owned: { [string]: boolean }, Equipped: string? } } = {}

local function createStarterPets()
    return {
        Owned = {
            StarterCat = true,
        },
        Equipped = nil,
    }
end

local function sendPets(player: Player)
    local pets = petsByPlayer[player]
    if pets then
        petsUpdated:FireClient(player, pets)
    end
end

local function equip(player: Player, petId: unknown): (boolean, string)
    if typeof(petId) ~= "string" or #petId > 40 then
        return false, "Invalid pet"
    end

    local pet = PetConfig.Pets[petId]
    if not pet then
        return false, "Unknown pet"
    end

    local pets = petsByPlayer[player]
    if not pets or not pets.Owned[petId] then
        return false, "Pet not owned"
    end

    pets.Equipped = petId
    sendPets(player)

    return true, "Equipped " .. pet.DisplayName
end

Players.PlayerAdded:Connect(function(player)
    petsByPlayer[player] = createStarterPets()
    sendPets(player)
end)

Players.PlayerRemoving:Connect(function(player)
    petsByPlayer[player] = nil
end)

equipPet.OnServerEvent:Connect(function(player, petId)
    local ok, message = equip(player, petId)
    equipPet:FireClient(player, ok, message)
end)
```

## Client UI Hook

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local remotes = ReplicatedStorage:WaitForChild("Remotes")
local equipPet = remotes:WaitForChild("EquipPet")
local petsUpdated = remotes:WaitForChild("PetsUpdated")

script.Parent.EquipStarterCatButton.Activated:Connect(function()
    equipPet:FireServer("StarterCat")
end)

petsUpdated.OnClientEvent:Connect(function(pets)
    print("Pets updated", pets)
end)
```

## Follow Visuals

Pet following should be visual. Keep ownership server-side, but clients can render smooth local pet movement from replicated equipped state or server-approved snapshots.

Do not move dozens of pets with heavy server physics if simple client visuals work.

## DataStore Notes

Save:

- Owned pet IDs.
- Equipped pet ID.

Do not save:

- Display names.
- Rarity text.
- Client-only positions.

Validate saved pet IDs against `PetConfig`.

## Mistakes To Avoid

- Client grants rare pets.
- Client chooses pet stats.
- Pet ownership is only in UI.
- Pet follow physics overloads the server.
- Duplicate pets appear because ownership is stored as a loose array without IDs.
