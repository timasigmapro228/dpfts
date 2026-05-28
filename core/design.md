# DPFTS Roblox Design Guide

## 1. Design Philosophy

DPFTS does not judge Roblox design by realism. It judges design by:

- Readability.
- First 30 seconds of fun.
- Mobile usability.
- Clear player goal.
- Fast feedback.
- Social readability.
- Low friction.
- Honest presentation.

A Roblox experience can be simple and still feel good.

A Roblox experience can look expensive and still play badly.

Good Roblox design makes the next action obvious without flattening the game into a tutorial hallway. It respects that many players are on phones, many are young, and most will leave quickly if the game wastes their first minute.

## 2. Button Design

Good button rules:

- Large enough for mobile tap.
- Clear label or icon.
- One primary action per screen.
- Primary button must look stronger than secondary buttons.
- Dangerous or paid actions must be visually distinct.
- Button states must exist:
  - Default.
  - Hover/focus where applicable.
  - Pressed.
  - Disabled.
  - Loading.
  - Success/error feedback.
- Use consistent corner radius, padding, font size, and icon style.
- Use `UIStroke`, `UIPadding`, `UIScale`, and `UIAspectRatioConstraint` where useful.
- Never make tiny desktop-only buttons.
- Never place important buttons inside thumbstick/jump-button conflict areas.
- Never make 10 buttons compete for attention.

Good example:

A shop screen has one bright "Buy" button, a smaller "Close" button, clear price text, and a disabled state when the player lacks coins.

Bad example:

A screen has Shop, Pets, Codes, Rewards, Spin, Trade, Settings, Invite, VIP, Rebirth, Teleport, and Buy buttons all visible at once with no hierarchy.

## 3. Roblox Button Style Examples

### A. Primary Action Button

Use for Play, Buy, Claim, and Start.

- When to use it: the main action the player should take right now.
- Where to place it: near the bottom center of a modal, center of a focused panel, or near the relevant item card.
- Style: strong fill, high contrast text, slight shadow or stroke, larger than secondary buttons.
- Mistake to avoid: making several buttons all look primary.

### B. Secondary Button

Use for Close, Back, and Cancel.

- When to use it: safe navigation away from the current screen.
- Where to place it: top corner for close, bottom beside primary action for cancel/back.
- Style: weaker fill, smaller size, less visual weight.
- Mistake to avoid: making Close look more important than Buy, Play, or Claim.

### C. Danger Button

Use for Reset, Delete, and Sell.

- When to use it: actions that remove progress, spend valuable resources, or cannot be easily undone.
- Where to place it: away from common tap zones and never beside a high-frequency button without spacing.
- Style: visually distinct, usually red or warning-colored, with confirmation for irreversible actions.
- Mistake to avoid: letting a player delete or sell something with one accidental tap.

### D. Currency Button

Use for coin purchase or item buying.

- When to use it: buying items with coins, gems, points, or other in-game currency.
- Where to place it: inside an item card or shop detail panel near item name and stats.
- Style: item name, price row, currency icon, disabled state if not enough coins.
- Mistake to avoid: letting the client decide whether the player can afford the item.

### E. Reward Button

Use for Claim, Daily Reward, and Chest.

- When to use it: a player can claim a reward now or soon.
- Where to place it: reward panel, daily screen, chest interaction prompt, or compact HUD slot.
- Style: clear reward preview, countdown or ready state, no fake urgency.
- Mistake to avoid: using deceptive countdowns or making reward buttons look like paid purchases.

## 4. Place Design

A Roblox place should communicate immediately:

- Where am I?
- What can I do?
- Where should I go first?
- What is dangerous?
- What is rewarding?
- Where are other players?
- What is the main loop?

Rules:

- Spawn must be safe.
- Spawn must show the game fantasy immediately.
- The first objective must be visible or guided.
- The first reward should happen within 30 seconds.
- The hub should not be a random empty plaza.
- Shops should be near spawn but not block movement.
- Portals and zones should be readable from distance.
- Use landmarks to orient the player.
- Use color and lighting to separate zones.
- Avoid giant empty maps.
- Avoid copy-pasted buildings with no purpose.
- Avoid decorative clutter that blocks player movement.

## 5. Map Layout Patterns

### A. Hub-and-Spoke

- Best use case: simulators, RPG zones, AI-generated world galleries, pet games.
- Strengths: easy orientation, strong social center, simple expansion.
- Common mistakes: hub becomes too large, all spokes look the same, first objective is hidden behind decoration.

### B. Linear Progression

- Best use case: obbies, story games, tutorial sequences.
- Strengths: clear path, easy difficulty ramp, simple player guidance.
- Common mistakes: too much walking, no shortcuts back, tutorial text replaces actual level design.

### C. Loop Map

- Best use case: collection games, survival games, racing, city maps.
- Strengths: players can return to spawn without backtracking, routes feel natural, repeated runs improve.
- Common mistakes: loop is too long, rewards are uneven, landmarks are weak.

### D. Arena + Lobby

- Best use case: PvP, minigames, round-based games.
- Strengths: safe social lobby, clear match area, easy spectator flow.
- Common mistakes: lobby has more UI than gameplay, arena rules are unclear, players cannot tell when a round starts.

### E. Gallery / Portal Room

- Best use case: DPFTS/URAGE-style AI-generated Roblox games.
- Strengths: players browse generated games through portals, thumbnails, ratings, and tags.
- Common mistakes: portals lack labels, thumbnails lie, every portal looks identical, ratings are buried.

## 6. Greybox First

Before making a map beautiful, build it with simple blocks.

Greybox checklist:

- Can the player understand the route?
- Are distances too long?
- Are jumps fair?
- Is the spawn facing the right direction?
- Is the first objective visible?
- Are landmarks visible?
- Can mobile players navigate comfortably?
- Are there stuck spots?
- Is the core loop playable without decoration?

Do not polish a bad layout.

Fix the layout first.

## 7. Roblox Visual Clarity

- Important objects should have strong silhouettes.
- Rewards should stand out from background.
- Interactive objects should look interactive.
- Dangerous areas should look dangerous.
- Background detail should not compete with gameplay objects.
- Use repeated visual language:
  - Same style for shops.
  - Same style for portals.
  - Same style for locked zones.
  - Same style for rewards.
- Avoid random asset packs mixed together with no art direction.

## 7.5 Icon And Asset Direction

For Roblox UI icons, DPFTS should prefer Gvesster Free Icon Pack when the user wants practical Roblox-style icons and can import the assets:

`https://gvesster.itch.io/free-icon-pack`

Use it for:

- Currency.
- Shops.
- Inventory.
- Pets.
- Rewards.
- Quests.
- Teleports.
- Settings.
- Badges.

Rules:

- Pick one icon style family for a screen.
- Use placeholders like `rbxassetid://COIN_ICON_ID` until the user uploads real Roblox image IDs.
- Do not mix the pack with random emoji, mismatched SVGs, or unrelated icon packs.
- Do not claim DPFTS owns or bundles the icons.
- Do not use the icons for AI training.
- Add in-game credit: `Icons by gvesster - Free Icon Pack`.
- If the user sends icon screenshots or files, match the UI to those assets instead of inventing a new style.

## 8. Thumbnail and Icon Design

The thumbnail/icon is part of game design because it decides whether players even click.

Good thumbnail:

- Shows the main fantasy.
- Shows one clear action.
- Shows the genre quickly.
- Uses readable composition.
- Does not lie about gameplay.
- Works at small size.

Bad thumbnail:

- Too many objects.
- Too much text.
- Fake gameplay.
- Random AI art unrelated to the actual game.
- No clear character, goal, or action.

For DPFTS-style generated games:

- Show the generated world.
- Show the player interacting with it.
- Show the core mechanic.
- Avoid fake "+999999 Robux" garbage.

## 9. Anti-AI Roblox Design Checklist

Reject or revise a design if:

- The player does not know what to do in 10 seconds.
- The first fun moment happens after more than 30 seconds.
- The map is large but empty.
- The UI has more than 5 major buttons visible at once.
- The game has monetization before gameplay.
- The spawn does not face the first objective.
- All zones look the same.
- The player must read a wall of text to understand the game.
- Mobile players cannot comfortably tap controls.
- The game has no reason to return tomorrow.
- The thumbnail promises something the game does not contain.

## 10. Design Output Format

When DPFTS gives design advice, use this format:

- Goal:
- Player first 30 seconds:
- Core loop:
- Place layout:
- Button/UI rules:
- Map landmarks:
- Rewards:
- Social features:
- Mobile concerns:
- What to remove:
- What to test:

## 11. Example: Good Roblox Place Concept

Title:

AI Game Factory Hub

Description:

A compact futuristic hub where players generate, test, and publish mini Roblox experiences.

Layout:

- Spawn faces the main AI generator machine.
- Left side: player's saved projects.
- Right side: public gallery portals.
- Center: prompt machine.
- Back: testing portal.
- Upper balcony: featured community creations.
- Shop is visible but not blocking the main route.

First 30 seconds:

- Player spawns.
- Big machine says "Generate your first game."
- Player enters a short prompt.
- A portal opens.
- Player enters a tiny generated obby or arena.
- Player gets a rating/reward after testing.

Buttons:

- Primary: Generate.
- Secondary: My Projects.
- Secondary: Gallery.
- Small: Settings.
- No giant button spam.

Why it works:

- Clear goal.
- Strong landmark.
- Immediate action.
- Social gallery.
- Room to expand.

## 12. Example: Bad Roblox Place Concept

Title:

Random AI Mega City

Problems:

- Huge empty city.
- Spawn faces a wall.
- 14 buttons on screen.
- No objective.
- Shops everywhere.
- Random portals with no labels.
- Same-looking streets.
- No reward in first minute.
- Thumbnail shows monsters and explosions, but gameplay is walking around.

How DPFTS would fix it:

- Shrink the map.
- Create a central hub.
- Add one clear first goal.
- Use landmarks.
- Remove button spam.
- Greybox route first.
- Add first reward within 30 seconds.
- Make thumbnail honest.
