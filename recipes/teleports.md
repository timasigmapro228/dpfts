# Teleports Recipe

This recipe covers safe Roblox teleports for zones and places.

Teleporting is powerful. Validate where the player is going and why.

## Zone Teleport Inside Same Place

Use this for moving players to a zone, arena, lobby, or checkpoint inside the same place.

### Folder Structure

```text
ReplicatedStorage/
`-- Remotes/
    `-- RequestZoneTeleport (RemoteEvent)

Workspace/
`-- TeleportPoints/
    |-- Spawn
    `-- Arena

ServerScriptService/
`-- ZoneTeleportService (Script)
```

### ServerScriptService/ZoneTeleportService

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")

local requestZoneTeleport = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("RequestZoneTeleport")
local teleportPoints = Workspace:WaitForChild("TeleportPoints")

local ALLOWED_ZONES = {
    Spawn = true,
    Arena = true,
}

local function teleportPlayer(player: Player, zoneId: unknown): (boolean, string)
    if typeof(zoneId) ~= "string" or #zoneId > 30 then
        return false, "Invalid zone"
    end

    if not ALLOWED_ZONES[zoneId] then
        return false, "Zone locked"
    end

    local point = teleportPoints:FindFirstChild(zoneId)
    if not point or not point:IsA("BasePart") then
        return false, "Zone unavailable"
    end

    local character = player.Character
    local root = character and character:FindFirstChild("HumanoidRootPart")
    if not root or not root:IsA("BasePart") then
        return false, "Character not ready"
    end

    root.CFrame = point.CFrame + Vector3.new(0, 3, 0)
    return true, "Teleported"
end

requestZoneTeleport.OnServerEvent:Connect(function(player, zoneId)
    local ok, message = teleportPlayer(player, zoneId)
    requestZoneTeleport:FireClient(player, ok, message)
end)
```

## Cross-Place Teleport

Use `TeleportService` for moving players to another place in the same experience.

```lua
local TeleportService = game:GetService("TeleportService")

local TARGET_PLACE_ID = 123456789

local function teleportToMatch(player: Player)
    local ok, result = pcall(function()
        TeleportService:TeleportAsync(TARGET_PLACE_ID, { player })
    end)

    if not ok then
        warn("Teleport failed for", player.UserId, result)
    end
end
```

## Validation Rules

- Zone ID must be allowlisted.
- Locked zones must check progression.
- Match teleports must check party/match state.
- Do not let the client choose arbitrary place IDs.
- Do not teleport players into unsafe or paid-only areas without validation.

## Mistakes To Avoid

- Client sets `HumanoidRootPart.CFrame` for real progression teleports.
- Remote accepts any zone string.
- Client chooses destination place ID.
- No failure handling for `TeleportAsync`.
- Teleport points are hidden in random workspace folders.
