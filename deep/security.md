# Roblox Security Guide

Security in Roblox is mostly about authority. If the client can decide the outcome, an exploiter can decide the outcome.

DPFTS should push every important system toward server ownership, narrow remotes, validation, and boring failure behavior.

## Security Philosophy

- Client input is a request, not proof.
- Server state is the source of truth.
- Validation belongs beside the action it protects.
- Rate limits slow abuse but do not replace validation.
- Error messages to players should be simple.
- Server warnings should include enough detail to debug.
- Never make the "happy path" the only path.

## Server-Owned State

The server must own:

- Currency.
- Inventory.
- Damage.
- Rewards.
- Purchases.
- Cooldowns that affect rewards or combat.
- Quest completion.
- Admin commands.
- Moderation actions.
- DataStore writes.

The client may display state and request actions. It must not invent the final result.

## Client-Owned Presentation

The client can own:

- UI open/close state.
- Button loading states.
- Camera effects.
- Animation previews.
- Sound feedback.
- Local input collection.
- Cosmetic previews before purchase.

If the outcome changes progression, money, ownership, or another player, the server decides.

## Remote Threat Model

Assume an exploiter can:

- Fire any RemoteEvent.
- Invoke any RemoteFunction.
- Send wrong argument types.
- Send huge strings or tables.
- Spam requests.
- Replay old requests.
- Claim impossible positions or targets.
- Bypass your UI entirely.

Design remotes as if the UI does not exist.

## Validation Checklist

For every server action, validate:

- Player exists and is allowed to act.
- Argument types are correct.
- String IDs have length limits.
- Item, quest, target, or action exists in server config.
- Player owns required unlocks.
- Player has enough currency.
- Player is close enough to the object, if location matters.
- Target is valid and still in the game.
- Cooldown or rate limit allows the request.
- The action makes sense for the player's current state.

## Safe Remote Shape

Good remote:

```lua
BuyItem:FireServer("BasicSword")
```

The client sends only intent. The server looks up the price, checks currency, checks ownership, subtracts coins, grants the item, and returns a result.

For value-changing actions, validate before mutation:

1. Validate request type and size.
2. Validate config exists.
3. Validate player data is loaded.
4. Validate ownership, price, cooldown, distance, or permission.
5. Validate the reward/item/tool can actually be granted.
6. Mutate server state.
7. If a later step fails, roll back earlier mutations or reject before mutating.

Do not grant a Tool, badge, pet, reward, or trade result before the server has decided the transaction can finish safely.

Bad remote:

```lua
BuyItem:FireServer("BasicSword", 1, true, 999999)
```

The client sends price, discount, ownership, or reward values. That is not a request. That is an exploit menu.

## Validation Helper Example

Place this ModuleScript in `ServerScriptService/Security/RemoteGuards`.

```lua
local RemoteGuards = {}

local MAX_ID_LENGTH = 40

function RemoteGuards.isSafeId(value: unknown): boolean
    return typeof(value) == "string" and #value > 0 and #value <= MAX_ID_LENGTH
end

function RemoteGuards.getCoins(player: Player): IntValue?
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

return RemoteGuards
```

Use helpers to reduce repeated checks, not to hide important game logic.

## Rate Limit Example

Place rate limit state on the server.

```lua
local Players = game:GetService("Players")

local COOLDOWN_SECONDS = 0.5
local lastRequestByPlayer: { [Player]: number } = {}

local function canRequest(player: Player): boolean
    local now = os.clock()
    local lastRequest = lastRequestByPlayer[player]

    if lastRequest and now - lastRequest < COOLDOWN_SECONDS then
        return false
    end

    lastRequestByPlayer[player] = now
    return true
end

Players.PlayerRemoving:Connect(function(player)
    lastRequestByPlayer[player] = nil
end)
```

Use different cooldowns for different actions. A shop purchase and a combat hit should not share one global throttle.

## Distance Checks

If a player must be near something, verify it on the server.

```lua
local MAX_INTERACTION_DISTANCE = 12

local function isNearPart(player: Player, part: BasePart): boolean
    local character = player.Character
    local root = character and character:FindFirstChild("HumanoidRootPart")

    if not root or not root:IsA("BasePart") then
        return false
    end

    return (root.Position - part.Position).Magnitude <= MAX_INTERACTION_DISTANCE
end
```

Do not let the client say "I am close enough."

## Admin Commands

Admin actions need explicit permission checks.

- Store admin user IDs on the server.
- Require the allowlist even in Studio unless the command is intentionally limited to a private local test file.
- Never let the client choose permission level.
- Log important admin actions.
- Validate targets.
- Avoid dangerous public remotes like `RunCommand`.

Example:

```lua
local ADMINS = {
    [123456789] = true,
}

local function isAdmin(player: Player): boolean
    return ADMINS[player.UserId] == true
end
```

## Common Security Mistakes

- Client sends currency amount.
- Client sends damage amount.
- Client chooses reward tier.
- Client says whether it owns a gamepass.
- Client tells the server a purchase succeeded.
- RemoteEvent accepts tables without size limits.
- Server trusts UI disabled states.
- Admin remote has no user ID allowlist.
- Cooldowns only exist in LocalScripts.
- DataStore saves client-provided profile tables.

## Security Review Format

When DPFTS reviews a Roblox system for security, use:

- Server-owned state:
- Client-owned presentation:
- Remotes:
- Validation missing:
- Rate limits:
- DataStore risk:
- Marketplace risk:
- Exploit path:
- Fix first:
- Test with:
