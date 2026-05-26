# Roblox Bug Report Template Recipe

This recipe gives users a useful bug report format for AI assistants and teammates. A good bug report saves time because it separates symptoms, evidence, and guesses.

Use this when a user says something is broken but the cause is unclear.

## Copyable Template

```markdown
# Roblox Bug Report

## Summary
One sentence: what broke?

## Expected Behavior
What should happen?

## Actual Behavior
What happens instead?

## Steps To Reproduce
1. Start from a clean server or specify the saved player state.
2. Do the exact action.
3. Observe the result.

## Test Mode
- Play Solo:
- Start Server with multiple clients:
- Live server:
- Device or screen size:

## Roblox Studio Placement
- Server script path:
- LocalScript path:
- ModuleScript path:
- RemoteEvent or RemoteFunction path:
- UI path:

## Output Errors
Paste the exact client or server Output error here.

## Server/Client Ownership
- What does the client request?
- What does the server validate?
- What does the server change?
- What does the client display?

## Data State
- New player or returning player:
- Profile loaded before action:
- DataStore enabled:
- Relevant saved fields:

## Recent Changes
What changed right before the bug appeared?

## Screenshots Or Video
Attach if the bug is visual, UI, map, or animation related.

## What I Already Tried
List only real checks, not guesses.
```

## How DPFTS Should Use It

When a user gives a weak report, ask for the smallest missing piece that changes the next debugging step.

Examples:

- If there is no Output error, ask for the exact client and server Output error.
- If a RemoteEvent is involved, ask for the RemoteEvent path and the server handler.
- If the bug is visual, ask for a screenshot or screen size.
- If the bug is data-related, ask whether the profile was loaded and whether it fails for new or returning players.

## Minimal Bug Report

If the user is a beginner, accept a shorter version:

```markdown
What I clicked:
What I expected:
What happened:
Where the script is:
Output error:
Play Solo or live server:
```

## Example Good Report

```markdown
# Roblox Bug Report

## Summary
Buying a sword removes coins but does not add the sword to inventory.

## Expected Behavior
Player spends 100 coins and receives IronSword in inventory.

## Actual Behavior
Coins decrease from 150 to 50. Inventory UI still shows empty.

## Steps To Reproduce
1. Start Server with two clients.
2. Give Player1 150 coins through server test command.
3. Open ShopGui.
4. Click Iron Sword.
5. Close and reopen inventory.

## Test Mode
- Start Server with multiple clients: fails
- Play Solo: works sometimes
- Live server: not tested
- Device or screen size: desktop

## Roblox Studio Placement
- Server script path: ServerScriptService/ShopService
- LocalScript path: StarterGui/ShopGui/LocalScript
- ModuleScript path: ReplicatedStorage/ShopConfig
- RemoteEvent path: ReplicatedStorage/Remotes/BuyItem
- UI path: StarterGui/ShopGui

## Output Errors
Server Output: [ShopService] Profile inventory missing Inventory table

## Server/Client Ownership
- Client requests itemId IronSword.
- Server validates price and coins.
- Server changes Coins.
- Server should add inventory item, but does not.
- Client displays result from server.

## Data State
- Returning player.
- Profile loaded before action: yes.
- DataStore enabled: yes.
- Relevant saved fields: Coins, Inventory.

## Recent Changes
Changed profile schema from Items to Inventory yesterday.
```

## Mistakes To Avoid

- Only saying "it does not work."
- Pasting a giant script without the error.
- Sending screenshots for server bugs with no Output.
- Hiding script placement.
- Not saying whether it fails in Play Solo, Start Server, or live.
- Mixing expected behavior and guesses about the cause.
