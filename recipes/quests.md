# Quest System Recipe

This recipe builds a simple server-authoritative quest system.

Quests should push players through the core loop. Do not add quests that are just chores with a progress bar.

## What The System Does

- Stores quest config in `ReplicatedStorage`.
- Tracks progress on the server.
- Lets the client claim rewards.
- Server validates completion.
- Server grants coins and marks the quest claimed.
- Server sends quest state to the client.

## Folder Structure

```text
ReplicatedStorage/
|-- QuestConfig (ModuleScript)
`-- Remotes/
    |-- ClaimQuestReward (RemoteEvent)
    `-- QuestUpdated (RemoteEvent)

ServerScriptService/
|-- QuestProgress (BindableEvent)
`-- QuestService (Script)

StarterGui/
`-- QuestGui/
    |-- ClaimButton (TextButton)
    |-- StatusLabel (TextLabel)
    `-- LocalScript
```

## ReplicatedStorage/QuestConfig

```lua
local QuestConfig = {}

QuestConfig.Quests = {
    FirstPurchase = {
        DisplayName = "Buy your first item",
        Goal = 1,
        RewardCoins = 100,
    },
}

return QuestConfig
```

## ServerScriptService/QuestService

```lua
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local QuestConfig = require(ReplicatedStorage:WaitForChild("QuestConfig"))
local remotes = ReplicatedStorage:WaitForChild("Remotes")
local claimQuestReward = remotes:WaitForChild("ClaimQuestReward")
local questUpdated = remotes:WaitForChild("QuestUpdated")
local questProgress = script.Parent:WaitForChild("QuestProgress")

local questStateByPlayer: { [Player]: { [string]: { Progress: number, Claimed: boolean } } } = {}

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

local function createQuestState()
    local state = {}

    for questId in pairs(QuestConfig.Quests) do
        state[questId] = {
            Progress = 0,
            Claimed = false,
        }
    end

    return state
end

local function sendQuestState(player: Player)
    local state = questStateByPlayer[player]
    if state then
        questUpdated:FireClient(player, state)
    end
end

local function addQuestProgress(player: Player, questId: string, amount: number)
    local quest = QuestConfig.Quests[questId]
    local state = questStateByPlayer[player]

    if not quest or not state or amount <= 0 then
        return
    end

    local questState = state[questId]
    if not questState or questState.Claimed then
        return
    end

    questState.Progress = math.min(questState.Progress + amount, quest.Goal)
    sendQuestState(player)
end

local function claim(player: Player, questId: unknown): (boolean, string)
    if typeof(questId) ~= "string" or #questId > 40 then
        return false, "Invalid quest"
    end

    local quest = QuestConfig.Quests[questId]
    if not quest then
        return false, "Unknown quest"
    end

    local state = questStateByPlayer[player]
    local questState = state and state[questId]
    if not questState then
        return false, "Quest not ready"
    end

    if questState.Claimed then
        return false, "Already claimed"
    end

    if questState.Progress < quest.Goal then
        return false, "Quest not complete"
    end

    local coins = getCoins(player)
    if not coins then
        return false, "Coins not ready"
    end

    questState.Claimed = true
    coins.Value += quest.RewardCoins
    sendQuestState(player)

    return true, "Claimed +" .. quest.RewardCoins .. " coins"
end

Players.PlayerAdded:Connect(function(player)
    questStateByPlayer[player] = createQuestState()
    sendQuestState(player)
end)

Players.PlayerRemoving:Connect(function(player)
    questStateByPlayer[player] = nil
end)

claimQuestReward.OnServerEvent:Connect(function(player, questId)
    local ok, message = claim(player, questId)
    claimQuestReward:FireClient(player, ok, message)
end)

questProgress.Event:Connect(addQuestProgress)
```

Create a `BindableEvent` named `QuestProgress` next to `QuestService` in `ServerScriptService`. Other server scripts can fire it when real gameplay actions happen.

## StarterGui/QuestGui/LocalScript

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local remotes = ReplicatedStorage:WaitForChild("Remotes")
local claimQuestReward = remotes:WaitForChild("ClaimQuestReward")
local questUpdated = remotes:WaitForChild("QuestUpdated")

local gui = script.Parent
local claimButton = gui:WaitForChild("ClaimButton")
local statusLabel = gui:WaitForChild("StatusLabel")

local selectedQuestId = "FirstPurchase"

claimButton.Activated:Connect(function()
    claimButton.Active = false
    statusLabel.Text = "Claiming..."
    claimQuestReward:FireServer(selectedQuestId)
end)

claimQuestReward.OnClientEvent:Connect(function(ok: boolean, message: string)
    claimButton.Active = true
    statusLabel.Text = message
end)

questUpdated.OnClientEvent:Connect(function(state)
    local quest = state[selectedQuestId]
    if quest then
        statusLabel.Text = "Progress: " .. quest.Progress
    end
end)
```

## How To Use Progress

When another server system completes the relevant action:

```lua
local questProgress = game.ServerScriptService:WaitForChild("QuestProgress")

questProgress:Fire(player, "FirstPurchase", 1)
```

For larger projects, wrap quest progress in a ModuleScript service API.

## Good Quest Design

- Teach the core loop.
- Reward actions the player should learn.
- Keep the first quest short.
- Show progress clearly.
- Give a useful reward.
- Lead to the next goal.

## Bad Quest Design

- Walk 500 studs for no reason.
- Wait 10 minutes.
- Open five menus.
- Buy something before understanding the game.
- Complete hidden tasks with no feedback.

## Testing Steps

- Join and confirm quest state is created.
- Try claiming before progress and confirm denial.
- Add progress from the server and confirm UI updates.
- Claim once and confirm coins increase.
- Try claiming again and confirm denial.
- Test with two players.

## Mistakes To Avoid

- Client sets quest progress.
- Client grants quest rewards.
- Quest reward can be claimed twice.
- Quest state is saved without validation.
- Quest UI hides the actual next action.
