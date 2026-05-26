# Roblox Release Checklist Recipe

This is a practical pre-release checklist for small Roblox games and updates. It is not a replacement for testing with real players, but it catches the mistakes AI-generated Roblox projects often ship with.

Use this before publishing a new game, major update, monetization feature, economy change, trading feature, party feature, or DataStore migration.

## Release Info

```markdown
# Release Checklist

Release name:
Date:
Owner:
Main change:
Rollback method:
```

## Must Pass

- [ ] Game starts without Output errors in Play Solo.
- [ ] Game starts without Output errors in Start Server with two clients.
- [ ] New player can complete the first action.
- [ ] New player gets a first reward within 30 seconds.
- [ ] Returning player data loads correctly.
- [ ] Leaving and rejoining preserves earned currency and items.
- [ ] Mobile screen size is usable.
- [ ] Primary UI buttons are easy to tap.
- [ ] No LocalScript grants currency, inventory, badges, purchases, damage, admin powers, or teleports.
- [ ] Every RemoteEvent has server validation.
- [ ] DataStore calls use `pcall`.
- [ ] There is a rollback or disable plan for the main change.

## Security Pass

- [ ] Currency changes happen on the server.
- [ ] Inventory changes happen on the server.
- [ ] Rewards happen on the server.
- [ ] Damage or round results happen on the server.
- [ ] Purchases are granted by server-side MarketplaceService logic.
- [ ] RemoteEvents reject invalid types.
- [ ] RemoteEvents reject unknown item IDs or destinations.
- [ ] RemoteEvents reject actions before profile load.
- [ ] RemoteEvents have cooldowns where spam matters.
- [ ] Admin actions require a server-side allowlist.

## Data Pass

- [ ] Default profile data is complete.
- [ ] Loaded data is validated.
- [ ] Schema version is handled.
- [ ] Autosave interval is reasonable.
- [ ] `PlayerRemoving` saves.
- [ ] `BindToClose` saves remaining players.
- [ ] Failed loads show a safe message or safe fallback.
- [ ] Failed saves are logged.
- [ ] Rewards are not granted before data is loaded.

## UI And Mobile Pass

- [ ] HUD does not cover core gameplay.
- [ ] Shop, inventory, quests, party, and trade screens have clear close/back controls.
- [ ] Buttons have default, pressed, disabled, loading, and result states where needed.
- [ ] Paid or dangerous actions are visually distinct.
- [ ] Text fits on small screens.
- [ ] Important buttons avoid thumbstick and jump-button conflict areas.
- [ ] No screen shows more than five major actions at once.

## First Session Pass

- [ ] Spawn faces the first objective or main landmark.
- [ ] First goal is visible or strongly guided.
- [ ] Tutorial is short and interactive.
- [ ] First reward happens quickly.
- [ ] Player sees the next goal after the first reward.
- [ ] Shop is visible but does not block the main route.
- [ ] Thumbnail and icon honestly represent the game.

## Economy Pass

- [ ] First purchase is reachable quickly.
- [ ] Prices come from server config.
- [ ] Main currency has useful sinks.
- [ ] Daily rewards do not overpower gameplay.
- [ ] Paid items do not make free play pointless.
- [ ] Currency cannot be farmed through rejoin spam, button spam, or client edits.

## Social And Abuse Pass

- [ ] Party invites have cooldowns.
- [ ] Trade confirmations are server-owned.
- [ ] Trade cancel and player-leave cases are handled.
- [ ] Teleports validate access and destination.
- [ ] Badge awards come from server-confirmed milestones.
- [ ] User text is filtered or avoided.
- [ ] Reports or moderation-sensitive actions do not expose private data.

## Performance Pass

- [ ] No RemoteEvent fires every frame.
- [ ] No DataStore calls happen in tight loops.
- [ ] No expensive server logic runs every frame without need.
- [ ] UI is not rebuilt every frame.
- [ ] Event connections are not duplicated every time a menu opens.
- [ ] Spawn and hub performance are acceptable with multiple players.

## Launch Decision

```markdown
Must-fix issues:
Known acceptable issues:
Rollback trigger:
Safe to publish: yes/no
```

## Mistakes To Avoid

- Checking only the happy path.
- Publishing after only one-player testing.
- Ignoring mobile.
- Testing purchases by trusting a LocalScript.
- Forgetting disconnect and leave cases.
- Shipping without a rollback plan.
- Calling the release ready while Output is full of errors.
