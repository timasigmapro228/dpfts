# Admin Commands Recipe

This recipe builds a small server-authoritative admin command pattern.

Admin commands are dangerous. Treat them like production tools, not toys. A bad admin remote can destroy your economy or let exploiters punish players.

## What The System Does

- Stores admin user IDs on the server.
- Accepts admin requests through a RemoteEvent.
- Validates the caller, command, target, and amount.
- Runs only allowlisted commands.
- Logs admin actions with `warn`.

## Folder Structure

```text
ReplicatedStorage/
`-- Remotes/
    `-- AdminCommand (RemoteEvent)

ServerScriptService/
`-- AdminCommandService (Script)

StarterGui/
`-- AdminPanelGui/
    |-- CommandButton (TextButton)
    |-- StatusLabel (TextLabel)
    `-- LocalScript
```

## ServerScriptService/AdminCommandService

```lua
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local adminCommand = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("AdminCommand")

local ADMINS = {
    [123456789] = true,
}

local MAX_COINS_TO_GRANT = 10000

local function isAdmin(player: Player): boolean
    return ADMINS[player.UserId] == true
end

local function findPlayerByName(name: string): Player?
    for _, player in ipairs(Players:GetPlayers()) do
        if string.lower(player.Name) == string.lower(name) then
            return player
        end
    end

    return nil
end

local function addCoins(target: Player, amount: number): boolean
    local leaderstats = target:FindFirstChild("leaderstats")
    local coins = leaderstats and leaderstats:FindFirstChild("Coins")

    if not coins or not coins:IsA("IntValue") then
        return false
    end

    coins.Value += amount
    return true
end

local function runCommand(admin: Player, commandName: unknown, targetName: unknown, amount: unknown): (boolean, string)
    if not isAdmin(admin) then
        return false, "Not allowed"
    end

    if typeof(commandName) ~= "string" or typeof(targetName) ~= "string" then
        return false, "Invalid command"
    end

    local target = findPlayerByName(targetName)
    if not target then
        return false, "Target not found"
    end

    if commandName == "GrantCoins" then
        if typeof(amount) ~= "number" then
            return false, "Invalid amount"
        end

        local safeAmount = math.floor(amount)
        if safeAmount <= 0 or safeAmount > MAX_COINS_TO_GRANT then
            return false, "Amount out of range"
        end

        if not addCoins(target, safeAmount) then
            return false, "Coins not ready"
        end

        warn(admin.Name .. " granted " .. safeAmount .. " coins to " .. target.Name)
        return true, "Granted coins"
    end

    return false, "Unknown command"
end

adminCommand.OnServerEvent:Connect(function(player, commandName, targetName, amount)
    local ok, message = runCommand(player, commandName, targetName, amount)
    adminCommand:FireClient(player, ok, message)
end)
```

## StarterGui/AdminPanelGui/LocalScript

Only show this UI to admins. Hiding UI is not security; the server allowlist is security.

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local adminCommand = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("AdminCommand")
local gui = script.Parent
local button = gui:WaitForChild("CommandButton")
local statusLabel = gui:WaitForChild("StatusLabel")

button.Activated:Connect(function()
    adminCommand:FireServer("GrantCoins", "PlayerNameHere", 100)
end)

adminCommand.OnClientEvent:Connect(function(ok: boolean, message: string)
    statusLabel.Text = message
end)
```

## Better Admin Practice

- Use user IDs, not names, for admin allowlists.
- Log every destructive or economy-changing action.
- Use small command allowlists.
- Add confirmations for destructive commands.
- Avoid commands that run arbitrary code.
- Avoid public admin spectacle that ruins normal gameplay.

## Never Do This

```lua
AdminCommand.OnServerEvent:Connect(function(player, code)
    loadstring(code)()
end)
```

Do not run client-provided code. Do not build "universal command" remotes.

## Testing Steps

- Test as non-admin and confirm denial.
- Test as admin and confirm success.
- Try unknown command.
- Try huge amount.
- Try missing target.
- Confirm action is logged.
- Confirm client cannot bypass the allowlist.

## Mistakes To Avoid

- Admin status stored in a LocalScript.
- RemoteEvent called `RunCode`.
- Commands accept any amount.
- Commands target players without validation.
- Admin UI is treated as the only protection.
