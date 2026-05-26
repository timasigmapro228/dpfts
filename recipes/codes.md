# Redeem Codes Recipe

Codes are common in Roblox games, but they are easy to make badly. A safe code system is server-authoritative, one-time per player, and DataStore-ready.

## What The System Does

- Client sends a code string.
- Server normalizes and validates it.
- Server checks whether the code exists.
- Server checks whether the player already redeemed it.
- Server grants reward.
- Server records the claim.

## Folder Structure

```text
ReplicatedStorage/
|-- CodeConfig (ModuleScript)
`-- Remotes/
    `-- RedeemCode (RemoteEvent)

ServerScriptService/
`-- CodeService (Script)

StarterGui/
`-- CodesGui/
    |-- CodeBox (TextBox)
    |-- RedeemButton (TextButton)
    |-- StatusLabel (TextLabel)
    `-- LocalScript
```

## ReplicatedStorage/CodeConfig

```lua
local CodeConfig = {}

CodeConfig.Codes = {
    RELEASE = {
        Coins = 500,
    },
    WELCOME = {
        Coins = 250,
    },
}

return CodeConfig
```

## ServerScriptService/CodeService

```lua
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local CodeConfig = require(ReplicatedStorage:WaitForChild("CodeConfig"))
local redeemCode = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("RedeemCode")

local redeemedByPlayer: { [Player]: { [string]: boolean } } = {}
local lastRedeemByPlayer: { [Player]: number } = {}

local function normalizeCode(code: string): string
    return string.upper(string.gsub(code, "%s+", ""))
end

local function getCoins(player: Player): IntValue?
    local leaderstats = player:FindFirstChild("leaderstats")
    local coins = leaderstats and leaderstats:FindFirstChild("Coins")

    if coins and coins:IsA("IntValue") then
        return coins
    end

    return nil
end

local function canRedeemNow(player: Player): boolean
    local now = os.clock()
    local lastRedeem = lastRedeemByPlayer[player]

    if lastRedeem and now - lastRedeem < 1 then
        return false
    end

    lastRedeemByPlayer[player] = now
    return true
end

local function redeem(player: Player, rawCode: unknown): (boolean, string)
    if not canRedeemNow(player) then
        return false, "Slow down"
    end

    if typeof(rawCode) ~= "string" or #rawCode > 40 then
        return false, "Invalid code"
    end

    local code = normalizeCode(rawCode)
    local reward = CodeConfig.Codes[code]
    if not reward then
        return false, "Unknown code"
    end

    local redeemed = redeemedByPlayer[player]
    if not redeemed then
        redeemed = {}
        redeemedByPlayer[player] = redeemed
    end

    if redeemed[code] then
        return false, "Already redeemed"
    end

    local coins = getCoins(player)
    if not coins then
        return false, "Coins not ready"
    end

    redeemed[code] = true
    coins.Value += reward.Coins

    return true, "Redeemed +" .. reward.Coins .. " coins"
end

Players.PlayerRemoving:Connect(function(player)
    redeemedByPlayer[player] = nil
    lastRedeemByPlayer[player] = nil
end)

redeemCode.OnServerEvent:Connect(function(player, rawCode)
    local ok, message = redeem(player, rawCode)
    redeemCode:FireClient(player, ok, message)
end)
```

## StarterGui/CodesGui/LocalScript

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local redeemCode = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("RedeemCode")

local gui = script.Parent
local codeBox = gui:WaitForChild("CodeBox")
local redeemButton = gui:WaitForChild("RedeemButton")
local statusLabel = gui:WaitForChild("StatusLabel")

local isPending = false

redeemButton.Activated:Connect(function()
    if isPending then
        return
    end

    isPending = true
    redeemButton.Active = false
    statusLabel.Text = "Checking..."
    redeemCode:FireServer(codeBox.Text)
end)

redeemCode.OnClientEvent:Connect(function(ok: boolean, message: string)
    isPending = false
    redeemButton.Active = true
    statusLabel.Text = message
end)
```

## DataStore-Ready Notes

Save redeemed codes as a dictionary:

```lua
RedeemedCodes = {
    RELEASE = true,
}
```

Never trust the client to tell you which codes are redeemed.

## Mistakes To Avoid

- Client grants rewards.
- Client decides whether the code exists.
- Codes can be redeemed repeatedly.
- Code input has no length limit.
- Server saves every failed attempt.
- Codes promise fake Robux.
