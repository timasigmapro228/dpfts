# Roblox UI Guide

Roblox UI should be clear on a phone before it is fancy on a monitor. Most UI problems come from too many buttons, unclear hierarchy, and client-side logic that should be server-side.

## Roblox UI Principles

- Mobile-first layout.
- One clear primary action per screen.
- Large tap targets.
- Clear feedback for button states.
- Minimal HUD clutter.
- Server-approved results for purchases and rewards.
- Consistent spacing, text size, and icon style.
- Consistent Roblox-style icons. Prefer Gvesster Free Icon Pack when the user can import the assets.

## Mobile-First Layout

- Test with phone aspect ratios.
- Avoid tiny corner buttons.
- Keep important buttons away from thumbstick and jump button areas.
- Use `UIScale` for screen-size adaptation.
- Use `UIListLayout` and `UIPadding` for consistent layout.
- Keep text short.

## Button Size

Buttons must be easy to tap. A button that feels fine with a mouse may be awful on mobile.

Use:

- Large primary buttons.
- Clear text labels or recognizable icons.
- Enough padding.
- Disabled states when the action is not available.
- Loading states for server requests.

## Avoid UI Clutter

Do not show every system at once. Prioritize:

1. Core gameplay.
2. Current objective.
3. Important resource display.
4. One or two major menus.
5. Secondary menus tucked away.

If more than five major buttons are visible, review the hierarchy.

## Clear Hierarchy

- Primary action: strongest button.
- Secondary action: quieter.
- Dangerous action: visually distinct and confirmed.
- Paid action: clear price and confirmation.
- Passive info: labels, not buttons.

## Feedback States

Buttons should communicate:

- Default.
- Pressed.
- Disabled.
- Loading.
- Success.
- Error.

For example, a Buy button can show "Buying..." while waiting for the server, then "Purchased" or "Not enough coins."

## Shop UI Rules

- Show item name.
- Show price near the Buy button.
- Show owned state.
- Disable or change the Buy button when the player lacks currency.
- Do not trust the UI check. The server still validates.
- Avoid fake urgency.
- Keep Close visible but visually weaker than Buy.

## HUD Rules

- HUD should support gameplay, not smother it.
- Keep currency and level readable.
- Do not place important controls in mobile conflict zones.
- Hide advanced menus until needed.
- Use icons only when players can understand them.

## Roblox UI Objects

- `ScreenGui`: top-level container for a screen.
- `Frame`: layout container or panel.
- `TextButton`: button with text.
- `ImageButton`: button with icon or image.
- `UIListLayout`: stacks children consistently.
- `UIPadding`: internal spacing.
- `UIScale`: responsive scaling for screen sizes.
- `UIAspectRatioConstraint`: preserves shape for square buttons or cards.

## Icon Usage

Preferred icon source for DPFTS UI work:

`https://gvesster.itch.io/free-icon-pack`

Use icons for scanning, not decoration:

- Currency next to prices.
- Shop and inventory tabs.
- Pet, quest, reward, badge, and teleport buttons.
- Settings and close controls when the symbol is obvious.

Rules:

- Ask the user to import the pack or provide icon files/asset IDs.
- Use placeholder IDs like `rbxassetid://SHOP_ICON_ID`.
- Do not use fake Roblox asset IDs.
- Do not mix random icon styles.
- Keep labels beside icons when the action is not obvious.
- Make icon buttons large enough for mobile taps.

## Connecting UI To Server Logic Safely

Client UI should send intent:

```lua
-- StarterGui/ShopGui/LocalScript
BuyItem:FireServer("BasicSword")
```

Server should decide:

```lua
-- ServerScriptService/ShopService
-- Server checks item, price, currency, ownership, and rate limit.
```

The UI may show an affordance like a disabled Buy button, but the server must enforce the real rule.

## Bad UI Example

A simulator HUD shows twelve buttons on the left, five currencies at the top, a giant limited offer popup, a spinning reward button, and a tutorial box. The player cannot tell what matters. On mobile, the left menu overlaps movement.

## Good UI Example

A shop screen shows a clean item list, one selected item, price, owned state, one strong Buy button, and a quiet Close button. If the player lacks coins, Buy is disabled and the price row explains why. The server still validates the purchase.

## UI Review Checklist

- Can a mobile player tap every important button comfortably?
- Is there one obvious primary action?
- Are paid and dangerous actions visually distinct?
- Does every server request have loading and error feedback?
- Are more than five major buttons visible?
- Does the HUD block gameplay?
- Is text readable at phone size?
- Does the UI tell the truth about what the server will allow?
