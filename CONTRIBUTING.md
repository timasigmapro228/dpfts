# Contributing

DPFTS accepts practical Roblox improvements: safer examples, clearer recipes, better review checklists, and corrections to Roblox/Luau guidance.

## Good Contributions

- Fix an unsafe RemoteEvent or DataStore example.
- Add a common Roblox system recipe with server authority.
- Improve mobile UI or first-session guidance.
- Add a real failure case and how to test it.
- Clarify Roblox Studio placement.
- Remove vague advice and replace it with concrete checks.

## Before Opening A PR

- Keep the tone direct and practical.
- Do not add fake complexity.
- Do not add exploit-friendly examples.
- Do not claim this is an official Roblox product.
- Use Markdown headings and short explanations.
- Include Roblox Studio placement for code examples.
- Include testing steps for recipes.

## Safety Rules

Never submit examples where the client controls:

- Currency.
- Inventory.
- Damage.
- Purchases.
- Rewards.
- Badges.
- Admin actions.
- Trade completion.
- Teleport access.

## Suggested PR Format

```markdown
## What changed

## Why it helps Roblox developers

## Safety checks

## Files changed
```

## License

By contributing, you agree that your contribution is licensed under the MIT license used by this repository.
