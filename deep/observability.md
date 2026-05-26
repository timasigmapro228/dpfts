# Roblox Observability Guide

Observability means the game can explain what happened. For Roblox projects, that usually means useful logs, simple counters, analytics events, and release checks that show where players get stuck or where systems fail.

DPFTS should recommend observability that is practical for small teams. Do not build a fake enterprise telemetry platform for a simple Roblox game.

## What To Measure

Measure things that help decisions:

- Joins and session length.
- Tutorial start, first action, first reward, tutorial complete.
- Shop opens, buy attempts, buy success, buy rejection reason.
- Currency earned and spent.
- Quest accept, progress, complete, reward.
- Round join, round start, round finish, reward.
- Teleport attempt, success, failure reason.
- Party invite, accept, decline, leave.
- Trading start, confirm, cancel, complete, reject reason.
- Data load success, load failure, save success, save failure.
- RemoteEvent rejection counts.

Do not measure private chat content or sensitive personal information.

## Logs vs Analytics

Use logs to debug specific problems:

- Script errors.
- Rejected remote requests.
- Failed DataStore calls.
- Unexpected nil data.
- Permission denials.

Use analytics to understand player behavior:

- Where players leave.
- Whether the first reward happens fast enough.
- Which systems players ignore.
- Whether economy sinks are working.
- Whether updates improve retention.

## Server Event Pattern

Keep event names consistent. Send small payloads.

```lua
-- ServerScriptService/AnalyticsService.lua
local AnalyticsService = game:GetService("AnalyticsService")

local Analytics = {}

function Analytics.logFunnelStep(player: Player, funnelName: string, step: number, stepName: string)
    local success, err = pcall(function()
        AnalyticsService:LogFunnelStepEvent(player, funnelName, step, stepName)
    end)

    if not success then
        warn(("[Analytics] Funnel step failed for %s: %s"):format(player.Name, tostring(err)))
    end
end

function Analytics.logEconomySource(player: Player, currency: string, amount: number, source: string)
    local success, err = pcall(function()
        AnalyticsService:LogEconomyEvent(player, Enum.AnalyticsEconomyFlowType.Source, currency, amount, player:GetAttribute(currency) or 0, source)
    end)

    if not success then
        warn(("[Analytics] Economy source failed for %s: %s"):format(player.Name, tostring(err)))
    end
end

return Analytics
```

Roblox analytics APIs can change over time. If an API signature is different in the current Creator Hub docs, follow the current official API and keep the DPFTS principle: log meaningful server-confirmed events, not client claims.

## Event Naming Rules

Good names:

- `Tutorial_FirstReward`
- `Shop_BuyRejected_NotEnoughCoins`
- `Round_Completed`
- `Trade_Cancelled_PlayerLeft`
- `DataStore_SaveFailed`

Bad names:

- `thing`
- `button`
- `stuff_happened`
- `error`
- `monetization_event_17`

## Remote Rejection Tracking

Remote rejections are not just security noise. They show bugs, exploit attempts, and confusing UI.

Track:

- Remote name.
- Player UserId.
- Reason.
- Count per minute.
- Whether the rejection came after a UI action.

Do not kick players for one malformed request. Do rate-limit spam and investigate repeated abuse.

## Release Counters

Before and after updates, watch:

- Average session length.
- Tutorial completion rate.
- First purchase rate.
- DataStore failure rate.
- Remote rejection spikes.
- Error output spikes.
- Mobile UI complaints.
- Economy inflation.
- Trade cancel and dispute reports.

If an update makes these worse, roll back or patch quickly.

## Lightweight Debug Attributes

Attributes can help expose server state during testing.

```lua
-- ServerScriptService/ProfileDebug.server.lua
local function markProfileLoaded(player: Player, loaded: boolean)
    player:SetAttribute("ProfileLoaded", loaded)
end

local function markLastSave(player: Player, unixTime: number)
    player:SetAttribute("LastSaveUnix", unixTime)
end
```

Use attributes for simple state, not secrets. Anything in replicated instances can be visible to clients.

## Dashboards For Small Teams

For a small Roblox team, a useful dashboard can be a simple checklist plus analytics review:

- Are errors increasing?
- Are players reaching the first reward?
- Are purchases failing?
- Are saves failing?
- Are mobile players closing UI correctly?
- Are players using the new feature?

If the dashboard does not help someone make a decision, it is decoration.

## Privacy And Safety

Never log:

- Passwords or tokens.
- Private personal information.
- Full free-text chat content.
- Sensitive moderation details in public-facing UI.
- More player data than needed for debugging.

Prefer UserId over display names in logs when tracking a specific player issue.

## Observability Answer Format

When DPFTS suggests instrumentation, use:

- Question to answer:
- Event or log:
- Where it is recorded:
- Server-confirmed data:
- Payload fields:
- Failure signal:
- What decision it supports:

## Mistakes To Avoid

- Logging every frame.
- Letting the client report rewards, purchases, or trade success as truth.
- Adding analytics with no question behind it.
- Logging huge tables or full profile data.
- Ignoring failed analytics calls in development.
- Measuring monetization before measuring first fun.
