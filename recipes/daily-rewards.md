# Daily Rewards Recipe

This recipe builds a simple server-authoritative daily reward claim.

It uses `os.time()` and in-memory state for clarity. For a real game, store `LastDailyReward` in the player's DataStore profile.

## What The System Does

- Client requests a daily reward.
- Server checks the last claim time.
- Server grants coins if the cooldown is ready.
- Server returns the next claim time.
- Client displays success or remaining time.

## Folder Structure

```text
ReplicatedStorage/
`-- Remotes/
    `-- ClaimDailyReward (RemoteEvent)

ServerScriptService/
`-- DailyRewardService (Script)

StarterGui/
`-- DailyRewardGui/
    |-- ClaimButton (TextButton)
    |-- StatusLabel (TextLabel)
    `-- LocalScript
```

## ServerScriptService/DailyRewardService

```lua
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local claimDailyReward = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("ClaimDailyReward")

local DAILY_COOLDOWN_SECONDS = 24 * 60 * 60
local DAILY_REWARD_COINS = 250

local lastClaimByPlayer: { [Player]: number } = {}
local lastRequestByPlayer: { [Player]: number } = {}

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
    local lastRequest = lastRequestByPlayer[player]

    if lastRequest and now - lastRequest < 1 then
        return false
    end

    lastRequestByPlayer[player] = now
    return true
end

local function claim(player: Player): (boolean, string, number)
    if not canRequest(player) then
        return false, "Slow down", 0
    end

    local now = os.time()
    local lastClaim = lastClaimByPlayer[player] or 0
    local nextClaimAt = lastClaim + DAILY_COOLDOWN_SECONDS

    if now < nextClaimAt then
        return false, "Reward not ready", nextClaimAt
    end

    local coins = getCoins(player)
    if not coins then
        return false, "Coins not ready", nextClaimAt
    end

    coins.Value += DAILY_REWARD_COINS
    lastClaimByPlayer[player] = now

    return true, "Claimed +" .. DAILY_REWARD_COINS .. " coins", now + DAILY_COOLDOWN_SECONDS
end

claimDailyReward.OnServerEvent:Connect(function(player)
    local ok, message, nextClaimAt = claim(player)
    claimDailyReward:FireClient(player, ok, message, nextClaimAt)
end)

Players.PlayerRemoving:Connect(function(player)
    lastClaimByPlayer[player] = nil
    lastRequestByPlayer[player] = nil
end)
```

## StarterGui/DailyRewardGui/LocalScript

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local claimDailyReward = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("ClaimDailyReward")

local gui = script.Parent
local claimButton = gui:WaitForChild("ClaimButton")
local statusLabel = gui:WaitForChild("StatusLabel")

local isPending = false

local function setPending(pending: boolean)
    isPending = pending
    claimButton.Active = not pending
    claimButton.AutoButtonColor = not pending
    claimButton.Text = pending and "Claiming..." or "Claim"
end

claimButton.Activated:Connect(function()
    if isPending then
        return
    end

    setPending(true)
    claimDailyReward:FireServer()
end)

claimDailyReward.OnClientEvent:Connect(function(ok: boolean, message: string, nextClaimAt: number)
    setPending(false)
    statusLabel.Text = message

    if not ok and nextClaimAt > os.time() then
        local secondsLeft = nextClaimAt - os.time()
        statusLabel.Text = "Come back in " .. math.ceil(secondsLeft / 3600) .. " hours"
    end
end)
```

## Connecting To DataStore Later

Add this to the player profile:

```lua
LastDailyReward = 0
```

On claim:

- Read `profile.LastDailyReward`.
- Validate the cooldown.
- Grant coins.
- Set `profile.LastDailyReward = os.time()`.
- Save through the normal profile save path.

Never let the client send `LastDailyReward`.

## Testing Steps

- Create `ClaimDailyReward` in `ReplicatedStorage/Remotes`.
- Use the leaderboard recipe so `Coins` exists.
- Click Claim and confirm coins increase on the server.
- Click again immediately and confirm the reward is denied.
- Temporarily reduce `DAILY_COOLDOWN_SECONDS` to 10 for testing.
- Test with two players and confirm cooldowns are separate.

## Mistakes To Avoid

- Client grants daily reward coins.
- Client sends the last claim time.
- Cooldown only exists in a LocalScript.
- Reward can be spammed by firing the RemoteEvent.
- DataStore saves every countdown tick.
- UI uses fake urgency or deceptive timers.
