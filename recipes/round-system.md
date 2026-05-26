# Round System Recipe

This recipe builds a simple server-controlled round loop: lobby, intermission, match, rewards.

Round systems are common in minigames, PvP, obbies, and party games. The server must own the state.

## Folder Structure

```text
ReplicatedStorage/
`-- Remotes/
    `-- RoundStateChanged (RemoteEvent)

ServerScriptService/
`-- RoundService (Script)
```

## ServerScriptService/RoundService

```lua
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local roundStateChanged = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("RoundStateChanged")

local MIN_PLAYERS = 2
local INTERMISSION_SECONDS = 15
local ROUND_SECONDS = 90
local REWARD_COINS = 100

local roundState = "Waiting"

local function broadcastState(timeLeft: number)
    roundStateChanged:FireAllClients(roundState, timeLeft)
end

local function getCoins(player: Player): IntValue?
    local leaderstats = player:FindFirstChild("leaderstats")
    local coins = leaderstats and leaderstats:FindFirstChild("Coins")

    if coins and coins:IsA("IntValue") then
        return coins
    end

    return nil
end

local function getEligiblePlayers(): { Player }
    local eligible = {}

    for _, player in ipairs(Players:GetPlayers()) do
        if player.Character then
            table.insert(eligible, player)
        end
    end

    return eligible
end

local function rewardPlayers(playersInRound: { Player })
    for _, player in ipairs(playersInRound) do
        if player.Parent == Players then
            local coins = getCoins(player)
            if coins then
                coins.Value += REWARD_COINS
            end
        end
    end
end

local function countdown(seconds: number)
    for timeLeft = seconds, 0, -1 do
        broadcastState(timeLeft)
        task.wait(1)
    end
end

while true do
    roundState = "Waiting"
    broadcastState(0)

    while #Players:GetPlayers() < MIN_PLAYERS do
        task.wait(1)
    end

    roundState = "Intermission"
    countdown(INTERMISSION_SECONDS)

    local playersInRound = getEligiblePlayers()
    if #playersInRound < MIN_PLAYERS then
        continue
    end

    roundState = "InRound"
    countdown(ROUND_SECONDS)

    roundState = "Rewards"
    rewardPlayers(playersInRound)
    broadcastState(0)
    task.wait(5)
end
```

## StarterGui/RoundGui/LocalScript

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local roundStateChanged = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("RoundStateChanged")
local statusLabel = script.Parent:WaitForChild("StatusLabel")

roundStateChanged.OnClientEvent:Connect(function(state: string, timeLeft: number)
    if timeLeft > 0 then
        statusLabel.Text = state .. ": " .. timeLeft
    else
        statusLabel.Text = state
    end
end)
```

## Better Round Design

- Lobby should be safe and social.
- Intermission should be short.
- Match objective should be clear before start.
- Rewards should be server-calculated.
- Late joins should go to lobby or spectator mode.
- Leaving should not break the round.

## Mistakes To Avoid

- Client starts the round.
- Client chooses winners.
- Rewards granted from LocalScripts.
- Long intermissions with nothing to do.
- Lobby UI hides the actual match portal.
- No handling for players leaving mid-round.
