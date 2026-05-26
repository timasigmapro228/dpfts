# Mobile UI Guide

Roblox UI must work on phones. Desktop-only UI is not a style choice; it is a player loss machine.

## Mobile-First Rules

- Design the phone layout first.
- Keep one primary action visible.
- Avoid thumbstick and jump-button conflict zones.
- Use short labels.
- Prefer fewer, larger buttons.
- Keep HUD elements away from the center of gameplay.
- Make tap targets visually obvious.
- Test text at small screen sizes.

## Safe Screen Zones

Avoid placing important buttons:

- Bottom left, where the virtual thumbstick lives.
- Bottom right, where jump/action controls often live.
- Too close to screen edges.
- Under Roblox top bar areas.
- Behind chat, leaderboard, or platform overlays.

Important actions should usually live in the center panel, upper side panels, or a focused modal with enough padding.

## Button Hierarchy

Every screen should have:

- One primary action.
- One clear exit/back action.
- Optional secondary actions with lower visual weight.
- Disabled and loading states.

Bad mobile UI asks players to choose between twelve equally loud buttons.

## HUD Rules

Good HUD:

- Shows only resources the player needs now.
- Keeps interaction buttons large.
- Hides secondary systems in menus.
- Does not block the character, crosshair, or objective.

Bad HUD:

- Covers both sides of the screen.
- Uses tiny icons with no labels.
- Shows every monetization and reward system at once.
- Makes gameplay feel like tapping a coupon board.

## Shop UI On Mobile

- Use a vertical item list or paged cards.
- Keep item name, price, and Buy button close together.
- Disable Buy if the client knows the player lacks coins, but still validate on the server.
- Use a confirmation step for Robux purchases when appropriate.
- Keep Close reachable but visually secondary.

## Tutorial UI On Mobile

- Use contextual prompts near the thing being taught.
- Avoid long dialogue boxes.
- Avoid blocking movement with text.
- Let players perform the action immediately.
- Use arrows, highlights, or proximity prompts when they reduce reading.

## Common Mistakes

- Tiny desktop buttons copied to mobile.
- Buttons inside movement control zones.
- Text labels longer than the button.
- Popups stacked on popups.
- Reward, shop, invite, and event buttons all flashing at once.
- Paid buttons styled like free claim buttons.

## Mobile UI Review Format

- Primary action:
- Tap target risk:
- Thumb zone conflicts:
- Text readability:
- HUD clutter:
- Loading/error states:
- Paid-action clarity:
- What to remove:
- What to test on phone:
