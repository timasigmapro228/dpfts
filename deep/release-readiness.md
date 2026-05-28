# Roblox Release Readiness Guide

Release readiness means the game can survive real players, mobile screens, network delay, exploit attempts, and leaving/joining at awkward times.

DPFTS should treat release checks as practical risk reduction. Do not turn a tiny MVP into a six-month certification project.

## Release Priorities

Check in this order:

1. Security.
2. Data safety.
3. First-session playability.
4. Mobile usability.
5. Performance.
6. Economy balance.
7. Social and abuse risk.
8. Polish.

If the game loses data or trusts the client, it is not ready.

## Minimum Pre-Release Pass

Before publishing or announcing an update:

- Test with Start Server and at least two clients.
- Test a new player path.
- Test a returning player path.
- Test mobile screen size.
- Test leaving during a reward, purchase, trade, round, or teleport.
- Test DataStore load and save failure behavior.
- Test RemoteEvent validation with bad inputs.
- Test UI loading, disabled, success, and error states.
- Test performance in the busiest area.
- Check the Output window for errors.

## Security Check

Review every RemoteEvent and RemoteFunction:

- What can the client request?
- What does the server validate?
- Can the client choose currency, price, reward, damage, ownership, rank, badge, teleport destination, or trade result?
- Is there a cooldown or rate limit?
- Are invalid requests rejected without crashing?

If a LocalScript can grant value, fix that before release.

## DataStore Check

Verify:

- Loads use `pcall`.
- Saves use `pcall`.
- Default data is complete.
- Schema version is handled.
- Profile data is validated after load.
- Autosave is not too frequent.
- `PlayerRemoving` saves.
- `BindToClose` saves remaining players.
- Rewards wait until profile data is loaded.

Run a leave-and-rejoin test after earning currency and items.

## Marketplace Check

Verify:

- Gamepass ownership checks happen on server.
- Developer product grants happen through receipt processing.
- Purchases are not granted by RemoteEvent claims.
- Failed purchases do not grant rewards.
- Duplicate receipts do not duplicate rewards.
- Purchase UI explains what the player gets.

Do not put Robux prompts before the player understands the game.

## Mobile UI Check

Verify:

- Primary buttons are easy to tap.
- Important controls avoid thumbstick and jump-button conflict areas.
- Text fits on small screens.
- Close/back buttons are visible.
- Shop, inventory, party, trade, and reward UI have loading and disabled states.
- No screen has ten major buttons fighting for attention.

If mobile players cannot use it, most Roblox players cannot use it.

## First 30 Seconds Check

The first session should answer:

- What is this game?
- What do I do first?
- Where do I go?
- What is the reward?
- Why should I keep playing?

Fix before release if:

- Spawn faces a wall.
- First objective is not visible.
- Tutorial blocks the first action.
- UI covers the whole screen before gameplay.
- First reward takes more than 30 seconds.

## Performance Check

Look for:

- Server scripts doing repeated expensive work.
- Remotes firing too often.
- UI updating every frame.
- Large unoptimized maps.
- Too many physics objects.
- Memory leaks from repeated connections.
- Expensive effects in spawn or hub.

Test the busiest likely moment, not just an empty server.

## Economy Check

Verify:

- First reward is fast.
- First purchase is reachable.
- Prices come from server config.
- Sources and sinks make sense.
- Paid items do not skip the whole game.
- Daily rewards do not overpower gameplay.
- Currency cannot be farmed by spam-clicking or rejoining.

## Social And Abuse Check

Verify:

- Invites have cooldowns.
- Trade actions are server-owned.
- Admin commands have allowlists.
- User-generated text is filtered or avoided.
- Party membership is server-owned.
- Teleports validate destination and access.
- Badge awards are tied to server-confirmed milestones.
- Completed trades have trade IDs and before/after inventory snapshots for debugging disputes or dupes.

## Rollback Plan

Before a meaningful update, know:

- What changed.
- Which files or systems changed.
- How to disable the feature.
- Whether a config flag can turn it off.
- What data migration happened.
- What player-facing message is needed if it breaks.

A simple rollback plan beats heroic panic.

## Release Answer Format

When DPFTS reviews release readiness, use:

- Release goal:
- Must-fix before release:
- Security risks:
- Data risks:
- Mobile risks:
- First-session risks:
- Performance risks:
- Economy/social risks:
- Tests to run:
- Rollback plan:
- Safe to ship?:

## Mistakes To Avoid

- Publishing after only Play Solo testing.
- Treating no visible error as proof.
- Forgetting mobile.
- Adding monetization before first fun.
- Ignoring leave/rejoin and disconnect cases.
- Shipping without checking RemoteEvents.
- Shipping without a way to disable a broken new feature.
