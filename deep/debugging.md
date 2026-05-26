# Roblox Debugging Guide

Roblox debugging is mostly about proving where the truth lives. Guessing from symptoms usually wastes time, especially when LocalScripts, server Scripts, remotes, DataStores, and Studio test modes all behave differently.

DPFTS should debug by narrowing the failure, checking server authority, and asking for only the missing evidence that changes the next step.

## Debugging Priority

Debug in this order:

1. Reproduce the bug.
2. Identify whether it is server, client, data, UI, network, or design.
3. Check the Roblox Studio Output window.
4. Check script placement.
5. Check RemoteEvent direction and validation.
6. Check DataStore loading state and failure paths.
7. Add temporary logs with clear labels.
8. Remove or downgrade noisy logs after the fix.

Do not rewrite the whole system because one symptom is confusing.

## Ask For Evidence, Not Vibes

Good debugging questions:

- What exact action triggers it?
- Does it fail in Play Solo, Start Server, or live servers?
- Is the error in the client output or server output?
- Which script owns the value that is wrong?
- Does the RemoteEvent fire?
- Does the server reject the request?
- Is the player's profile loaded before the action?

Weak debugging questions:

- Can you send everything?
- Are you sure it works?
- Did you try again?
- Maybe Roblox is broken?

## Common Roblox Failure Classes

### Script Placement

Symptoms:

- Code never runs.
- UI code works in Studio edit mode but not Play mode.
- Server cannot find a LocalScript-only object.

Check:

- Server logic belongs in `ServerScriptService`.
- Shared config belongs in `ReplicatedStorage`.
- Client UI logic belongs under `StarterGui` or `StarterPlayerScripts`.
- Server-only secrets never belong in `ReplicatedStorage`.

### Client/Server Mismatch

Symptoms:

- Player sees coins change, but server does not save them.
- Shop works for the local player only.
- Exploiters can grant rewards.

Check:

- Client requests an action.
- Server validates the action.
- Server changes currency, inventory, rewards, badges, teleports, or damage.
- Client only displays the result.

### RemoteEvent Direction

Symptoms:

- `OnServerEvent` never runs.
- `OnClientEvent` never runs.
- First argument is unexpectedly a `Player`.

Rules:

- `RemoteEvent:FireServer(...)` is called by a client.
- `RemoteEvent.OnServerEvent` receives `player` as the first argument.
- `RemoteEvent:FireClient(player, ...)` is called by the server.
- `RemoteEvent.OnClientEvent` does not receive `player` automatically.

### Data Not Loaded

Symptoms:

- New players have nil profile data.
- Purchases fail randomly after joining.
- Leaving quickly loses progress.

Check:

- Default data exists.
- DataStore calls are wrapped in `pcall`.
- Player actions wait until the profile is loaded.
- `PlayerRemoving` and `BindToClose` save safely.

### Studio vs Live Differences

Symptoms:

- Works in Studio but fails live.
- DataStore tests are inconsistent.
- Teleports or badges behave differently.

Check:

- API Services are enabled when testing DataStores.
- Test with Start Server and multiple clients.
- Badge IDs, place IDs, universe permissions, and product IDs are real.
- Live throttling and network delay are considered.

## Temporary Logging Pattern

Use clear log labels while debugging. Remove spam once the bug is fixed.

```lua
-- ServerScriptService/ShopService.server.lua
local DEBUG_SHOP = true

local function debugShop(message: string, data: any?)
    if not DEBUG_SHOP then
        return
    end

    print("[ShopService]", message, data or "")
end

-- Usage inside a RemoteEvent handler where player and itemId are in scope:
buyItem.OnServerEvent:Connect(function(player, itemId)
    debugShop("Buy request received", {
        userId = player.UserId,
        itemId = itemId,
    })
    -- validation and purchase logic here
end)
```

Good logs say:

- Which system produced the log.
- Which player was involved.
- What action happened.
- What decision the server made.

Bad logs say:

- `print("hi")`
- `print("works")`
- `print(player)`
- Huge tables every frame.

## Minimal Reproduction

When the bug is unclear, reduce it:

- Disable unrelated UI.
- Test one RemoteEvent.
- Test one item config entry.
- Test one player, then two players.
- Replace a complex map trigger with a simple part.
- Replace a full DataStore profile with one field.

If the reduced version works, add pieces back until it breaks.

## Debugging RemoteEvents

Checklist:

- Remote exists in `ReplicatedStorage/Remotes`.
- Client waits for the RemoteEvent with `WaitForChild`.
- Client sends an intent, not authority.
- Server receives `player` first.
- Server validates type, item existence, price, ownership, cooldown, and loaded profile.
- Server returns or fires a clear result.

Example server rejection:

```lua
-- ServerScriptService/ShopService.server.lua
local function reject(player: Player, reason: string)
    warn(("[ShopService] Rejected %s: %s"):format(player.Name, reason))
    BuyResult:FireClient(player, false, reason)
end
```

## Debugging DataStores

Checklist:

- Output shows the exact DataStore failure.
- Failed loads use default data only when safe.
- Player profile has a loaded flag.
- Saving does not happen every coin change.
- Save queue or debounce prevents request spam.
- `UpdateAsync` handles old data and schema version.

Never hide DataStore errors during development. A quiet failed save becomes a support problem later.

## Debugging UI

Checklist:

- UI exists under `StarterGui` or is cloned into `PlayerGui`.
- Buttons are large enough on mobile.
- The LocalScript is running.
- Button connections are created once, not every time a frame opens.
- Disabled and loading states are visible.
- UI waits for server results before claiming success.

## Debugging Performance

Look for:

- Work inside `Heartbeat`, `RenderStepped`, or `while true do` loops.
- RemoteEvents firing every frame.
- UI labels updated every frame without need.
- Large maps with too many unanchored parts.
- Expensive pathfinding or physics loops.
- Memory leaks from repeated event connections.

Fix the biggest repeated cost first.

## Debug Answer Format

When DPFTS answers a bug report, use:

- Symptom:
- Most likely system:
- Evidence needed:
- First check:
- Likely root causes:
- Minimal test:
- Fix direction:
- Regression test:

## Mistakes To Avoid

- Guessing a fix without reproducing the issue.
- Replacing a secure server system with client authority to make it "work."
- Ignoring the Output window.
- Debugging only in Play Solo for multiplayer systems.
- Leaving noisy debug logs in production.
- Treating DataStore failure as impossible.
- Trusting UI state more than server state.
