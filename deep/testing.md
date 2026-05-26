# Roblox Testing Guide

Roblox testing is not just "press Play once." DPFTS should help users test security, first-session clarity, mobile UI, DataStores, remotes, and common failure paths.

## Testing Philosophy

- Test the exact player flow.
- Test server authority by trying bad client requests.
- Test with two players when systems involve ownership, trading, combat, leaderboards, or social state.
- Test on mobile viewport sizes.
- Test failure states, not only success.
- Use analytics and funnels for design questions.

## Minimum Test Pass Before Shipping

Before DPFTS says a Roblox system is ready, include checks for:

- Play Solo.
- Start Server with two players.
- Mobile emulator or real mobile test.
- Slow or repeated button presses.
- Invalid RemoteEvent arguments.
- Player leaving during the system.
- Data not loaded yet.
- UI disabled/loading/error states.

## RemoteEvent Security Tests

For every remote:

- Send wrong type.
- Send unknown ID.
- Send huge string.
- Fire quickly many times.
- Try action without enough currency.
- Try action without ownership.
- Try action from the wrong distance.
- Try action before data loads.

Expected result: server rejects safely and player state does not change.

## DataStore Tests

- New player gets default data.
- Returning player loads saved data.
- Invalid stored data is sanitized.
- Player leaves during autosave.
- Server shuts down with players inside.
- DataStore failure warns and uses safe fallback.
- Client cannot send a full profile table to save.

## UI Tests

- Buttons fit on phone size.
- Important buttons avoid thumbstick and jump zones.
- Text does not overflow.
- Loading state appears during server request.
- Error state appears when server denies the action.
- Close/back buttons are weaker than primary action.
- Paid actions are visually distinct.

## First Session Test

Run this with a player who has not seen the game:

1. Spawn them.
2. Say nothing for 60 seconds.
3. Watch what they do.
4. Note where they stop, hesitate, or ask questions.
5. Check whether they complete the first core loop.

If they cannot find the fun without explanation, fix the design before adding more systems.

## Simple Server Smoke Test

Place temporary tests in `ServerScriptService/SmokeTests` while developing. Remove or disable them before publishing if they spam output.

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local remotes = ReplicatedStorage:WaitForChild("Remotes")

local requiredRemotes = {
    "BuyItem",
    "EquipItem",
    "ClaimDailyReward",
}

for _, remoteName in ipairs(requiredRemotes) do
    local remote = remotes:FindFirstChild(remoteName)
    assert(remote and remote:IsA("RemoteEvent"), "Missing RemoteEvent: " .. remoteName)
end

print("Smoke tests passed")
```

## Analytics Tests

Use analytics to answer focused questions:

- Did players finish onboarding?
- Did players complete the first quest?
- Did players claim the first reward?
- Did players open the shop too early?
- Did a UI change lower completion or increase confusion?

Do not track everything. Track the few steps that prove whether the core loop works.

## Common Mistakes

- Testing only in Play Solo.
- Testing only with your own knowledge of the game.
- Never testing bad RemoteEvent inputs.
- Treating UI disabled state as security.
- Publishing a tutorial without watching a new player use it.
- Advertising before fixing first-session retention.
