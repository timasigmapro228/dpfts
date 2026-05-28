# Full Roblox System Template Recipe

Use this recipe when a user asks DPFTS to build a complete system, such as a shop, inventory, quest system, pet system, trading system, round loop, or teleport system.

The goal is to stop AI from dumping one massive unexplained script.

## Prompt Template

```text
Use DPFTS to build a complete Roblox [SYSTEM NAME].

Game context:
- Genre:
- Main currency:
- Player data fields:
- UI needed:
- Multiplayer concerns:
- Monetization involved:

Requirements:
- Server-authoritative.
- Clean Luau.
- Roblox Studio placement for every script.
- RemoteEvents in ReplicatedStorage/Remotes.
- Config ModuleScripts where useful.
- Server/client separation.
- Testing steps in Studio.
- Common failure cases.
- Security review.
```

## Expected Output Shape

DPFTS should answer with:

1. Assumptions.
2. Folder structure.
3. Server/client responsibilities.
4. Remotes.
5. Config.
6. Data model.
7. Code by file.
8. Testing steps.
9. Security review.
10. Extension notes.

## Folder Structure Example

```text
ReplicatedStorage/
|-- Remotes/
|   |-- RequestAction
|   `-- ActionResult
|-- Config/
|   `-- SystemConfig
ServerScriptService/
|-- Services/
|   `-- SystemService
StarterGui/
`-- SystemGui/
    `-- LocalScript
```

## Server Checklist

- Owns all valuable state.
- Validates RemoteEvent input types.
- Uses server config for prices, rewards, IDs, and unlock rules.
- Checks profile loaded state.
- Applies cooldowns where spam matters.
- Sends clear success/failure result to client.
- Handles player leaving mid-action.

## Client Checklist

- Displays UI.
- Sends action requests.
- Shows loading state.
- Waits for server result.
- Shows success or error.
- Does not grant value.
- Does not decide final ownership or rewards.

## Security Section Template

```markdown
## Security Review

- Client cannot choose currency amount.
- Client cannot choose item price.
- Client cannot grant inventory.
- Server validates item existence.
- Server validates ownership.
- Server validates profile loaded state.
- Server returns result after mutation.
- Add cooldown if users can spam this request.
```

## Testing Section Template

```markdown
## Studio Testing

1. Start Server with two clients.
2. Test the happy path.
3. Test not enough currency.
4. Test invalid item ID.
5. Test clicking twice quickly.
6. Test leaving and rejoining.
7. Confirm server data and UI display match.
8. Check Output for errors.
```

## Mistakes To Avoid

- One giant script.
- No file paths.
- No Studio placement.
- No RemoteEvent validation.
- UI claims success before server confirmation.
- Client sends price, reward, or ownership.
- No failure cases.
- No multiplayer test.
