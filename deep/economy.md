# Roblox Economy Guide

Roblox economy design is about trust, pacing, and reasons to keep playing. A bad economy either gives everything away too fast, turns into endless grind, or becomes pay-to-win noise before the game is fun.

DPFTS should keep economies simple, server-owned, and tied to the core loop.

## Core Rule

The server owns all economy changes.

Clients can display coins, prices, boosts, and shop UI. They cannot grant currency, choose prices, apply discounts, or decide purchases succeeded.

## Sources And Sinks

Sources create currency:

- Completing the core loop.
- Quests.
- Daily rewards.
- Round rewards.
- Achievements.
- Developer product purchases.

Sinks remove currency:

- Tools.
- Upgrades.
- Cosmetics.
- Zone unlocks.
- Crafting.
- Pet upgrades.
- Rerolls, if they are honest and not predatory.

If sources are strong and sinks are weak, inflation eats progression.

## First Session Economy

Early economy should:

- Give a first reward quickly.
- Price the first purchase low.
- Show a visible next goal.
- Avoid five currencies.
- Avoid Robux prompts before the player understands value.

Good first economy:

- Earn 50 coins.
- Buy starter tool for 40 coins.
- See next tool costs 150 coins.
- Understand why playing again matters.

Bad first economy:

- Coins, gems, tickets, stars, shards.
- VIP popup before first action.
- First upgrade costs 10 minutes of grinding.

## Pricing Rules

- First purchase should be reachable fast.
- Prices should teach progression.
- Expensive items should preview value.
- Do not make every item linearly more expensive with no gameplay change.
- Cosmetics can be aspirational.
- Core tools should not make free players feel useless.

## Avoid Pay-To-Win Trash

Paid items should not destroy fair play.

Better monetization:

- Cosmetics.
- Convenience.
- Extra save slots.
- Private servers.
- Boosts with clear limits.
- Battle pass style progression if honest.

Risky monetization:

- Paid admin powers.
- Paid instant wins.
- Paid combat dominance.
- Paid trading advantage that affects economy stability.
- Popups before gameplay.

## Economy Validation

Validate:

- Currency exists and is server-owned.
- Price comes from server config.
- Player has enough currency.
- Item exists.
- Ownership rules.
- Purchase cooldown.
- Developer product receipt.
- DataStore profile is loaded before granting.

## Economy Review Format

- Core loop source:
- Main currency:
- First reward:
- First sink:
- Long-term sink:
- Inflation risk:
- Paid advantage risk:
- DataStore risk:
- What to remove:
- What to test:

## Mistakes To Avoid

- Client sends currency amount.
- Client sends item price.
- Daily reward is stronger than actual gameplay.
- Too many currencies early.
- First purchase takes too long.
- Paid items skip the whole game.
- No sinks after players earn currency.
- Economy changes are not tracked in analytics.
