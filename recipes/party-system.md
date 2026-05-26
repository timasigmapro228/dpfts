# Party System Recipe

This recipe builds a simple server-owned party system with invites, join, leave, and cleanup.

Parties should help friends play together. They should not become invite spam.

## Folder Structure

```text
ReplicatedStorage/
`-- Remotes/
    |-- InviteToParty (RemoteEvent)
    |-- RespondToPartyInvite (RemoteEvent)
    |-- LeaveParty (RemoteEvent)
    `-- PartyUpdated (RemoteEvent)

ServerScriptService/
`-- PartyService (Script)
```

## ServerScriptService/PartyService

```lua
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local remotes = ReplicatedStorage:WaitForChild("Remotes")
local inviteToParty = remotes:WaitForChild("InviteToParty")
local respondToPartyInvite = remotes:WaitForChild("RespondToPartyInvite")
local leaveParty = remotes:WaitForChild("LeaveParty")
local partyUpdated = remotes:WaitForChild("PartyUpdated")

local MAX_PARTY_SIZE = 4
local INVITE_COOLDOWN_SECONDS = 5

local partyByPlayer: { [Player]: { Leader: Player, Members: { Player } } } = {}
local pendingInviteByPlayer: { [Player]: Player } = {}
local lastInviteByPlayer: { [Player]: number } = {}

local function createParty(player: Player)
    local party = {
        Leader = player,
        Members = { player },
    }

    partyByPlayer[player] = party
    return party
end

local function sendParty(party)
    for _, member in ipairs(party.Members) do
        partyUpdated:FireClient(member, party)
    end
end

local function getParty(player: Player)
    return partyByPlayer[player] or createParty(player)
end

local function canInvite(player: Player): boolean
    local now = os.clock()
    local lastInvite = lastInviteByPlayer[player]

    if lastInvite and now - lastInvite < INVITE_COOLDOWN_SECONDS then
        return false
    end

    lastInviteByPlayer[player] = now
    return true
end

local function invite(player: Player, targetName: unknown): (boolean, string)
    if typeof(targetName) ~= "string" or #targetName > 20 then
        return false, "Invalid target"
    end

    if not canInvite(player) then
        return false, "Slow down"
    end

    local party = getParty(player)
    if party.Leader ~= player then
        return false, "Only leader can invite"
    end

    if #party.Members >= MAX_PARTY_SIZE then
        return false, "Party full"
    end

    local target = Players:FindFirstChild(targetName)
    if not target or not target:IsA("Player") or target == player then
        return false, "Target not found"
    end

    if partyByPlayer[target] then
        return false, "Target already in party"
    end

    pendingInviteByPlayer[target] = player
    partyUpdated:FireClient(target, {
        InviteFrom = player.Name,
    })

    return true, "Invite sent"
end

local function acceptInvite(player: Player): (boolean, string)
    local inviter = pendingInviteByPlayer[player]
    if not inviter or inviter.Parent ~= Players then
        pendingInviteByPlayer[player] = nil
        return false, "Invite expired"
    end

    local party = getParty(inviter)
    if #party.Members >= MAX_PARTY_SIZE then
        return false, "Party full"
    end

    table.insert(party.Members, player)
    partyByPlayer[player] = party
    pendingInviteByPlayer[player] = nil
    sendParty(party)

    return true, "Joined party"
end

local function removeFromParty(player: Player)
    local party = partyByPlayer[player]
    if not party then
        return
    end

    for index, member in ipairs(party.Members) do
        if member == player then
            table.remove(party.Members, index)
            break
        end
    end

    partyByPlayer[player] = nil

    if #party.Members == 0 then
        return
    end

    if party.Leader == player then
        party.Leader = party.Members[1]
    end

    sendParty(party)
end

inviteToParty.OnServerEvent:Connect(function(player, targetName)
    local ok, message = invite(player, targetName)
    inviteToParty:FireClient(player, ok, message)
end)

respondToPartyInvite.OnServerEvent:Connect(function(player, accepted)
    if accepted == true then
        local ok, message = acceptInvite(player)
        respondToPartyInvite:FireClient(player, ok, message)
    else
        pendingInviteByPlayer[player] = nil
        respondToPartyInvite:FireClient(player, true, "Invite declined")
    end
end)

leaveParty.OnServerEvent:Connect(function(player)
    removeFromParty(player)
end)

Players.PlayerRemoving:Connect(function(player)
    removeFromParty(player)
    pendingInviteByPlayer[player] = nil
    lastInviteByPlayer[player] = nil
end)
```

## Mistakes To Avoid

- Client decides party membership.
- No invite cooldown.
- Party does not clean up on leave.
- Non-leader can spam invites.
- Party size has no cap.
- UI forces players into parties without consent.
