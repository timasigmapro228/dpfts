# Roblox Performance Guide

Performance is game feel. If the server heartbeat drops, everyone feels latency. If the client frame rate drops, movement and combat feel bad. If memory climbs forever, players crash.

DPFTS should treat performance as part of design, not a cleanup step after the game is already messy.

## Performance Priorities

1. Keep the core loop responsive.
2. Avoid server work that scales badly with player count.
3. Avoid client UI and visual effects that run every frame for no reason.
4. Clean up connections, instances, and temporary tables.
5. Test on lower-end devices, not only a powerful PC.

## Common Roblox Performance Problems

- Expensive code inside `RunService.Heartbeat` or `RenderStepped`.
- Loops over every player or every instance too often.
- Recursive deep cloning of large tables.
- Too many RemoteEvents fired per second.
- Creating and destroying UI constantly.
- Leaving old event connections alive.
- Giant maps with too many unneeded parts.
- Large unoptimized meshes or textures.
- Server doing visual-only work that clients could do locally.

## High-Frequency Events

Do not run expensive logic every frame unless the feature truly needs it.

Bad:

```lua
RunService.Heartbeat:Connect(function()
    for _, player in ipairs(Players:GetPlayers()) do
        updateEveryQuest(player)
        savePlayerData(player)
    end
end)
```

Better:

```lua
local UPDATE_INTERVAL = 1
local elapsed = 0

RunService.Heartbeat:Connect(function(deltaTime)
    elapsed += deltaTime

    if elapsed < UPDATE_INTERVAL then
        return
    end

    elapsed = 0
    updateQuestMarkers()
end)
```

Never save DataStores on a frame loop.

## Server vs Client Work

Server should do:

- Validation.
- Game rules.
- Rewards.
- Ownership.
- Match state.
- Data persistence.

Client should do:

- Local VFX.
- UI animation.
- Camera shake.
- Cosmetic previews.
- Sound feedback.

If an effect does not change game truth, consider doing it locally.

## RemoteEvent Performance

RemoteEvents are not free.

- Batch non-critical updates.
- Avoid sending large tables constantly.
- Send IDs instead of full config.
- Rate limit spammy actions.
- Do not use remotes as a per-frame animation channel.

## Memory And Cleanup

Clean up:

- Temporary parts.
- UI clones.
- Round connections.
- Character connections.
- Player state tables on `PlayerRemoving`.
- Timers when a round ends.

```lua
local connectionsByPlayer: { [Player]: { RBXScriptConnection } } = {}

local function cleanupPlayer(player: Player)
    local connections = connectionsByPlayer[player]
    if connections then
        for _, connection in ipairs(connections) do
            connection:Disconnect()
        end
    end

    connectionsByPlayer[player] = nil
end
```

## Map Performance

- Greybox first.
- Keep the playable route compact.
- Use packages for repeated assets.
- Avoid unnecessary tiny parts.
- Avoid huge decorative areas outside the loop.
- Stream or simplify large places when needed.
- Do not mix random asset packs with high texture cost.

## UI Performance

- Reuse UI panels instead of cloning constantly.
- Avoid updating labels every frame.
- Update countdowns once per second.
- Hide inactive panels instead of destroying and rebuilding them.
- Keep animations short and purposeful.

## Performance Review Format

- Client FPS risk:
- Server heartbeat risk:
- Memory leak risk:
- Remote spam risk:
- Map/asset risk:
- UI update risk:
- What to profile:
- What to remove:
- What to test on low-end device:

## Mistakes To Avoid

- Optimizing random code before measuring the real issue.
- Treating server lag like a client graphics problem.
- Putting DataStore work in frequent loops.
- Sending full inventory tables every frame.
- Making the server create cosmetic-only effects for everyone.
- Ignoring mobile performance.
