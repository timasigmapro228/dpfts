# Badges Recipe

Badges are useful for milestones, achievements, and onboarding moments. Award them from the server when the player truly earns them.

## Folder Structure

```text
ReplicatedStorage/
`-- BadgeConfig (ModuleScript)

ServerScriptService/
`-- BadgeServiceHandler (Script)
```

## ReplicatedStorage/BadgeConfig

```lua
local BadgeConfig = {}

BadgeConfig.Badges = {
    CompletedTutorial = 123456789,
    FirstWin = 987654321,
}

return BadgeConfig
```

## ServerScriptService/BadgeServiceHandler

```lua
local BadgeService = game:GetService("BadgeService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local BadgeConfig = require(ReplicatedStorage:WaitForChild("BadgeConfig"))

local BadgeServiceHandler = {}

local function userHasBadge(player: Player, badgeId: number): boolean
    local ok, hasBadge = pcall(function()
        return BadgeService:UserHasBadgeAsync(player.UserId, badgeId)
    end)

    if not ok then
        warn("Failed to check badge", player.UserId, badgeId)
        return false
    end

    return hasBadge == true
end

function BadgeServiceHandler.award(player: Player, badgeKey: string): boolean
    local badgeId = BadgeConfig.Badges[badgeKey]
    if not badgeId then
        warn("Unknown badge key", badgeKey)
        return false
    end

    if userHasBadge(player, badgeId) then
        return true
    end

    local ok, result = pcall(function()
        BadgeService:AwardBadge(player.UserId, badgeId)
    end)

    if not ok then
        warn("Failed to award badge", player.UserId, badgeId, result)
        return false
    end

    return true
end

return BadgeServiceHandler
```

## Usage From Another Server Script

```lua
local BadgeServiceHandler = require(game.ServerScriptService:WaitForChild("BadgeServiceHandler"))

BadgeServiceHandler.award(player, "CompletedTutorial")
```

## Badge Design Rules

- Award meaningful milestones.
- Award from server-confirmed actions.
- Avoid badges for doing nothing.
- Avoid badge spam.
- Use badges to mark onboarding or achievement, not every small click.

## Mistakes To Avoid

- LocalScript awards badge.
- Badge ID is sent by client.
- No `pcall`.
- Badge is awarded before the action is truly complete.
- Unknown badge keys silently fail.
