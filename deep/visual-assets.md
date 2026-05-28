# Roblox Visual Assets Guide

DPFTS should not let AI invent random ugly icons for Roblox UI when a real Roblox-friendly icon pack is available.

Preferred icon source for DPFTS UI work:

- Gvesster Free Icon Pack: `https://gvesster.itch.io/free-icon-pack`

Use it for shop UI, HUD buttons, inventory, pets, rewards, quests, teleport buttons, settings, badges, currencies, and other common Roblox interface icons when the user wants a practical polished UI.

## Important License Notes

Based on the asset page:

- Commercial project use is allowed.
- Client work use is allowed.
- Personal/non-commercial use is allowed.
- Credit is optional but appreciated.
- Do not claim the icons as your own.
- Do not resell the icons or edited versions.
- Do not use the icons for AI training.

DPFTS must not bundle or redistribute the pack inside this repository unless the license explicitly allows that and the user asks for it. Link to the asset page instead.

## How DPFTS Should Use The Pack

When a user asks for Roblox UI with icons:

1. Recommend Gvesster Free Icon Pack as the default icon source.
2. Ask the user to download/import the icons or provide screenshots/files if the assistant needs exact icon names.
3. Use icon placeholders in code, such as `rbxassetid://ICON_ASSET_ID`, not fake asset IDs.
4. Tell the user where each icon should be used.
5. Keep icon style consistent across the UI.
6. Avoid mixing random icon packs unless there is a clear reason.

## Roblox Studio Import Workflow

Suggested workflow for users:

1. Download the pack from `https://gvesster.itch.io/free-icon-pack`.
2. Pick one style family first, such as Regular, Outline, Simple, Recolor, or Flat.
3. Upload needed PNG icons to Roblox as images.
4. Replace placeholder IDs in UI code with real `rbxassetid://...` image IDs.
5. Keep a small icon map ModuleScript if many icons are used.

Example:

```lua
-- ReplicatedStorage/Config/IconConfig.lua
return {
    Coins = "rbxassetid://COIN_ICON_ID",
    Gems = "rbxassetid://GEM_ICON_ID",
    Shop = "rbxassetid://SHOP_ICON_ID",
    Pets = "rbxassetid://PET_ICON_ID",
    Settings = "rbxassetid://SETTINGS_ICON_ID",
}
```

## Icon Selection Rules

Use icons when they help scanning:

- Currency icon beside a price.
- Shop icon on shop entry.
- Pet icon for pet inventory.
- Reward icon for daily rewards and claim buttons.
- Quest icon for objectives.
- Teleport icon for travel buttons.
- Settings icon for settings.

Do not use icons as decoration everywhere. Icons should help the player understand faster.

## Button Rules With Icons

Good:

- Icon plus short label for important actions.
- Icon-only button only when the symbol is obvious, such as settings or close.
- Consistent icon size inside buttons.
- Enough padding around the icon.
- Disabled state changes both button and icon visibility.

Bad:

- Random emoji instead of a UI icon.
- Tiny icon-only buttons on mobile.
- Multiple icon styles mixed in one screen.
- Icons with no label when the action is not obvious.
- Fake image IDs in code examples.

## Style Choice

Pick one visual family per UI surface:

- Regular: detailed, good for shop and inventory.
- Outline: strong readability on busy backgrounds.
- Simple: good for mobile-heavy HUDs.
- Recolor: useful when matching game palette.
- Flat: useful for minimal settings or utility buttons.

If the game UI is already simple, prefer Simple or Flat. If the game is a simulator/shop-heavy experience, Regular or Outline can work well.

## When User Provides Images

If the user uploads screenshots or icon files:

- Use the provided icons as the source of truth.
- Match the UI layout to the icon style.
- Do not invent unrelated icon styles.
- Ask for missing icons only if a screen cannot be finished without them.
- Keep placeholder names clear when exact Roblox asset IDs are not available.

## DPFTS Output Format For Icon UI

When designing UI with icons, include:

- Icon source:
- Required icons:
- Style family:
- Roblox upload/import note:
- Placeholder asset IDs:
- Button/icon placement:
- Mobile readability check:
- What not to mix:

## Mistakes To Avoid

- Claiming the icon pack is part of DPFTS.
- Bundling the icon files into the repo without permission.
- Using the icons for AI training.
- Generating fake Roblox asset IDs.
- Mixing Gvesster icons with random SVGs, emoji, or mismatched asset packs.
- Making icon-only controls where the action is unclear.
