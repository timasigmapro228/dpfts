# Tutorial / FTUE Recipe

FTUE means first-time user experience. A good Roblox tutorial gets the player to fun quickly, teaches only what matters now, and tracks where players drop off.

## What The Tutorial Does

- Teaches movement or one core interaction.
- Guides the player to the first objective.
- Lets the player complete the first core loop.
- Gives a first reward.
- Previews the next goal.

It does not explain every system in the game.

## Tutorial Structure

Use this simple flow:

1. Spawn facing the first objective.
2. Show one short prompt.
3. Highlight or point to the target.
4. Let the player act.
5. Reward immediately.
6. Unlock the next visible goal.

## Example FTUE For A Simulator

- Player spawns facing a training dummy.
- Prompt says: "Tap to train."
- Player taps the dummy.
- Coins pop out.
- Quest UI says: "Buy your first tool."
- Shop path lights up.
- Player buys the cheap starter tool.
- Next zone gate becomes visible.

That is enough. Save pets, trading, rebirth, codes, daily rewards, and gamepasses for later.

## Tutorial Prompt Rules

- One sentence at a time.
- No lore dump.
- No paragraph boxes.
- No blocking movement unless necessary.
- Use arrows or highlights when they explain faster than text.
- Let players skip repeated guidance.
- Never block the core action behind a long dialogue.

## Tutorial Event Tracking

Track key steps:

- Spawned.
- Reached first objective.
- Completed first action.
- Claimed first reward.
- Opened first shop or upgrade.
- Completed first loop.

If many players quit before step 3, the first objective is unclear or too slow.

## Server-Safe Progress Pattern

The client can show prompts. The server should decide whether the real tutorial step is complete.

```lua
local tutorialStepByPlayer: { [Player]: number } = {}

local function completeStep(player: Player, step: number)
    local currentStep = tutorialStepByPlayer[player] or 1

    if step ~= currentStep then
        return
    end

    tutorialStepByPlayer[player] = currentStep + 1
end
```

Use actual gameplay events to complete steps, such as server-confirmed purchase, reward, or interaction.

## Better To Do

- Teach through the real map.
- Give a win quickly.
- Keep early goals short.
- Use UI only to support the action.
- Track drop-off.
- Remove steps players do not need.

## Better Not To Do

- Start with a long dialogue.
- Explain all currencies at once.
- Force players through five menus.
- Add popups over movement controls.
- Teach monetization before gameplay.
- Use fake urgency.

## Testing Steps

- Watch a new player for 60 seconds without helping.
- Check whether they understand the first objective.
- Check whether they complete the first loop.
- Test on mobile.
- Remove one tutorial step and see if clarity improves.
- Track completion rates after publishing.

## DPFTS Output Format

- Tutorial goal:
- First prompt:
- First action:
- First reward:
- What the server validates:
- What the client displays:
- Drop-off points:
- What to remove:
- What to test:
