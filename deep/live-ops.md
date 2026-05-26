# Live Ops Guide

Live ops means keeping a Roblox experience healthy after launch. It includes updates, events, analytics, rollback thinking, and community feedback.

DPFTS should treat live ops as player trust. Do not use fake urgency, dishonest limiteds, or chaotic weekly feature piles.

## What Good Live Ops Does

- Gives returning players a reason to come back.
- Adds to the core loop.
- Keeps the first-time experience understandable.
- Uses analytics to find friction.
- Fixes bugs and performance before scaling promotion.
- Communicates clearly.

## Update Rhythm

A small team can use:

- Small updates every 2 to 4 weeks.
- Larger feature updates every 2 to 3 months.
- Hotfixes when bugs break progression, purchases, saves, or matchmaking.

Do not promise a schedule the team cannot maintain.

## Event Design

Good event:

- Uses the existing loop.
- Adds a clear temporary goal.
- Rewards play, not only login.
- Has honest timing.
- Does not punish new players for joining late.

Bad event:

- Adds a random currency.
- Hides normal progression.
- Requires a wall of instructions.
- Is mostly a shop page.
- Uses fake scarcity.

## Retention Hooks

Healthy hooks:

- Daily/weekly quests.
- Limited-time zones with normal gameplay.
- Social goals.
- Rotating shop stock with honest timers.
- Seasonal cosmetics.
- Community milestones.

Weak hooks:

- Claim button with no gameplay.
- Fake "only 2 left" pressure.
- Login streak that punishes missing a day too harshly.
- Random popups every time the player spawns.

## Analytics To Watch

- D1 retention.
- First-session completion.
- Tutorial step drop-off.
- Core-loop completion.
- Average session time.
- Crash/error rate.
- Purchase conversion.
- Quest completion.
- Event participation.

Use analytics to ask better questions. Do not optimize only for short-term money.

## Rollback Thinking

Before shipping an update, know:

- Which files changed.
- Which DataStore schema changed.
- Whether old data can still load.
- How to disable a broken event.
- How to disable a broken product or reward.
- Which analytics should move if the update works.

For risky features, add server flags:

```lua
local LiveOpsConfig = {
    DoubleCoinsEventEnabled = false,
    TradingEnabled = false,
}

return LiveOpsConfig
```

## Communication

Patch notes should say:

- What changed.
- Why it matters to players.
- What bugs were fixed.
- What event ends when.

Do not bury important purchase or save changes.

## Live Ops Review Format

- Update goal:
- Core loop impact:
- New player impact:
- Returning player reason:
- Event/reward design:
- Analytics to watch:
- Rollback plan:
- Communication:
- What to remove:

## Mistakes To Avoid

- Shipping monetization before fixing retention.
- Adding live events that confuse new players.
- Making every update a new currency.
- Breaking old saves without migration.
- Running limited events with dishonest timers.
- Ignoring performance after adding content.
