# Roblox Review Checklists

Use these checklists when DPFTS is asked to review code, architecture, UI, game design, or release readiness. Lead with the highest-risk findings first.

## Security Review

Reject or revise if:

- A LocalScript grants currency, inventory, damage, rewards, purchases, badges, teleports, or admin actions.
- A RemoteEvent accepts price, reward amount, rank, damage, winner, or ownership from the client.
- A RemoteEvent has no type validation.
- A RemoteEvent can be spammed for value.
- Admin commands do not use a server-side allowlist.
- Trade completion can be decided by the client.
- Marketplace rewards are granted without server-side purchase verification.

Output:

- Critical exploit:
- Server authority fix:
- Remote validation needed:
- Rate limit needed:
- Test with two clients:

## DataStore Review

Check:

- `pcall` around load and save.
- Complete default data.
- Schema version.
- Loaded data validation.
- Save on `PlayerRemoving`.
- `BindToClose` handling.
- Reasonable autosave interval.
- No DataStore calls in frame loops.
- Safe behavior when load fails.

Output:

- Data loss risk:
- Corruption risk:
- Request budget risk:
- Missing default:
- Migration risk:
- Test:

## UI Review

Check:

- Mobile tap size.
- Thumbstick/jump-button conflict areas.
- Clear hierarchy.
- One primary action per screen.
- Loading, disabled, success, and error states.
- Text fit on small screens.
- Server-confirmed success for purchases and rewards.

Output:

- Mobile issue:
- Button hierarchy issue:
- Feedback issue:
- Server-result issue:
- What to remove:
- What to test:

## Game Design Review

Check:

- Player understands what to do in 10 seconds.
- First fun happens within 30 seconds.
- Spawn faces the first objective.
- Map has landmarks.
- UI does not show too many major buttons.
- Core loop is clear.
- Reward timing is visible.
- Thumbnail honestly represents gameplay.

Output:

- First 30 seconds:
- Core loop:
- Map readability:
- UI noise:
- Reward timing:
- AI slop risk:

## Economy Review

Check:

- First reward is fast.
- First purchase is reachable.
- Sources and sinks are balanced.
- Prices come from server config.
- Paid items do not skip the whole game.
- Daily rewards do not overpower gameplay.
- Currency cannot be farmed by rejoin or spam.

Output:

- Inflation risk:
- First purchase issue:
- Sink issue:
- Paid advantage risk:
- Exploit path:
- Test:

## Performance Review

Check:

- No remotes every frame.
- No DataStore calls in tight loops.
- No repeated UI rebuilds every frame.
- Event connections are cleaned up.
- Server scripts do not run expensive work constantly.
- Spawn and hubs are not overloaded.

Output:

- Server cost:
- Client cost:
- Remote cost:
- Memory leak risk:
- Map cost:
- Test:

## Release Review

Check:

- Start Server with two clients works.
- New player flow works.
- Returning player flow works.
- Mobile UI works.
- Data saves after leave/rejoin.
- Remote validation rejects bad input.
- Rollback or disable plan exists.
- Output is not full of errors.

Output:

- Must-fix:
- Can ship with:
- Rollback trigger:
- Final verdict:

## Review Style

Be direct. Do not bury critical security findings under compliments.

Use severity labels:

- `Critical`: exploitable, data loss, purchase/reward abuse, or release blocker.
- `High`: likely player-facing bug or major maintainability issue.
- `Medium`: confusing behavior, weak UX, or scaling risk.
- `Low`: polish, naming, or small clarity issue.

For each finding, include:

- File or system.
- Problem.
- Why it matters.
- Concrete fix.
- Test.
