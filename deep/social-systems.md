# Roblox Social Systems Guide

Roblox is social by default, but social mechanics only help when they connect to the core loop. A random plaza, chat box, or invite popup does not create community by itself.

DPFTS should make social systems readable, safe, and useful.

## Good Social Systems

- Let players understand what others are doing.
- Help friends play together.
- Give players reasons to cooperate or compare.
- Show progress visually.
- Keep invites and notifications respectful.
- Support the core loop.

## Social Readability

Players should see social progress:

- Pets following players.
- Titles.
- Trails.
- Zone unlocks.
- Equipped tools.
- Leaderboards.
- Party status.
- Match queues.

If nobody can tell what anyone else achieved, the social layer is weak.

## Parties

Party systems should:

- Be server-owned.
- Require invites or consent.
- Limit party size.
- Clean up when players leave.
- Prevent invite spam.
- Route party members to matches or zones together.

Do not let the client decide who is in a party.

## Co-op Loops

Good co-op:

- Shared objective.
- Clear contribution.
- Reward that makes sense.
- No one player can ruin the entire group easily.
- Late join rules are clear.

Bad co-op:

- One player does all work.
- Rewards can be leeched without action.
- Trolls can trap or block progress.
- UI never says what teammates are doing.

## Invites

Use invites carefully:

- Cooldown repeated invites.
- Show who invited the player.
- Let players decline.
- Do not cover gameplay with invite spam.
- Do not fake social proof.

## Trading Boundaries

Trading is social and economic. Keep it strict:

- Confirm on both sides.
- Revalidate ownership.
- Lock trade state during finalization.
- Cancel when players leave.
- Avoid trading paid-only or temporary items unless explicitly designed.

## Mistakes To Avoid

- Invite spam.
- Random social plaza with no gameplay purpose.
- Party membership stored on the client.
- Rewards for doing nothing in co-op.
- Social UI covering mobile controls.
- Trading added before inventory is secure.

## Social Review Format

- Social purpose:
- Core loop connection:
- Player consent:
- Spam risk:
- Mobile UI risk:
- Reward fairness:
- Exploit path:
- What to remove:
- What to test with two players:
