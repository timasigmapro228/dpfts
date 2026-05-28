# Roblox Security Audit Recipe

Use this before releasing a Roblox system that touches currency, inventory, rewards, purchases, trading, admin tools, teleports, badges, damage, or player data.

## Audit Prompt

```text
Use DPFTS to run a Roblox security audit on this system.

Focus on:
- Client trust.
- RemoteEvent validation.
- DataStore safety.
- MarketplaceService safety.
- Reward/currency/inventory ownership.
- Rate limits.
- Admin permissions.
- Leave/disconnect cases.
- Exploit-friendly examples.

Return findings by severity with concrete fixes and Studio tests.
```

## Required Inputs

Ask for only what is needed:

- Script paths.
- RemoteEvent and RemoteFunction names.
- Relevant server code.
- Relevant LocalScript code.
- Data model.
- Item, reward, or purchase config.
- What the user expects the system to do.

## Critical Checks

- Can the client grant currency?
- Can the client grant inventory?
- Can the client choose item price?
- Can the client claim a purchase succeeded?
- Can the client choose damage or winner?
- Can the client complete a quest directly?
- Can the client award a badge?
- Can the client teleport anywhere?
- Can the client run admin actions?
- Can RemoteEvents be spammed for value?

## Data Checks

- Are profile loads protected with `pcall`?
- Are saves protected with `pcall`?
- Is loaded data validated?
- Is there a default profile?
- Is there schema version handling?
- Are rewards blocked until profile load?
- Are save requests limited?

## Marketplace Checks

- Gamepass perks are checked server-side.
- Developer product rewards are granted through receipt processing.
- Duplicate receipts do not duplicate rewards.
- Purchase UI does not promise success before confirmation.
- Robux prompts do not appear before the player understands value.

## Trading Checks

- Both players confirm on the server.
- Offered items are locked or revalidated before completion.
- Leaving cancels safely.
- Timeout cancels safely.
- Items cannot be duplicated.
- Trade result is server-owned.

## Audit Output Format

```markdown
## Findings

### Critical

- File/system:
- Problem:
- Exploit path:
- Fix:
- Test:

### High

### Medium

### Low

## Release Verdict

Safe to ship: yes/no
Must fix before release:
```

## Mistakes To Avoid

- Calling something safe because it works in Play Solo.
- Reviewing only LocalScripts.
- Ignoring failure paths.
- Ignoring rate limits.
- Giving vague advice like "add validation" without naming the validation.
