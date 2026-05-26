# Abuse Prevention Guide

Abuse prevention is not only anti-cheat. It includes spam, admin misuse, economy abuse, invite spam, trade scams, and unsafe user-generated text.

DPFTS should help Roblox developers prevent obvious abuse without pretending every game needs enterprise moderation tooling.

## Abuse Categories

- RemoteEvent spam.
- Economy farming exploits.
- Trade scams or dupes.
- Invite spam.
- Chat/name text abuse.
- Admin command misuse.
- Teleport abuse.
- Reward farming with alts.

## Basic Protections

- Server validate every important action.
- Add cooldowns to spammy remotes.
- Limit string lengths.
- Use allowlists for admin tools.
- Revalidate ownership at finalization.
- Keep logs for admin/economy actions.
- Avoid client-submitted rich profile data.

## Text Safety

For user-entered text:

- Limit length.
- Do not use text as code.
- Do not use text as DataStore keys without normalization.
- Use Roblox filtering APIs where player-visible custom text is involved.
- Avoid showing raw user text in admin logs that get copied into public spaces.

## Anti-Spam Pattern

```lua
local WINDOW_SECONDS = 5
local MAX_REQUESTS = 10
local requestsByPlayer: { [Player]: { number } } = {}

local function canUseRemote(player: Player): boolean
    local now = os.clock()
    local requests = requestsByPlayer[player]

    if not requests then
        requests = {}
        requestsByPlayer[player] = requests
    end

    for index = #requests, 1, -1 do
        if now - requests[index] > WINDOW_SECONDS then
            table.remove(requests, index)
        end
    end

    if #requests >= MAX_REQUESTS then
        return false
    end

    table.insert(requests, now)
    return true
end
```

Clean up player tables on `PlayerRemoving`.

## Admin Abuse Prevention

- Use user ID allowlists.
- Log every admin action.
- Cap amounts.
- Require confirmations for destructive actions.
- Avoid paid admin powers that affect other players.
- Do not ship debug commands to public players.

## Economy Abuse Prevention

- Reward from server-confirmed actions.
- Recheck cooldowns server-side.
- Track one-time claims in DataStore.
- Avoid unlimited redeemable codes.
- Validate receipt IDs for developer products.

## Social Abuse Prevention

- Cooldown invites.
- Allow declines.
- Cancel trades if offers change.
- Do not hide final trade details.
- Avoid UI that pressures fast confirmation.

## Abuse Review Format

- Abuse surface:
- Remote spam risk:
- Economy abuse risk:
- Social abuse risk:
- Admin abuse risk:
- Text safety:
- Logs needed:
- Cooldowns needed:
- What to test:
