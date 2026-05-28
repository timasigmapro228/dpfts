# Versioning

DPFTS uses practical semantic versioning from v1.0.0 onward.

## Version Rules

- `MAJOR`: changes the skill's core behavior, repository structure, or compatibility expectations.
- `MINOR`: adds new guides, recipes, examples, or meaningful capability areas.
- `PATCH`: fixes inaccurate examples, broken links, wording bugs, formatting issues, or small safety gaps.

## v1.0.0 Stability Promise

From v1.0.0 onward:

- `SKILL.md` remains the main AI-facing entry point.
- `core/`, `deep/`, `recipes/`, and `examples/` keep their current roles.
- Existing file paths should not be renamed without a major version reason.
- Security, playability, clarity, performance, and polish remain the priority order.
- The skill should stay practical and Roblox-specific.

## What Can Change In Minor Releases

Minor releases may add:

- New recipes.
- New deep guides.
- Better examples.
- Better review checklists.
- More installation notes.
- More precise Roblox Studio placement guidance.

Minor releases should not break existing prompts.

## What Belongs In Patch Releases

Patch releases should be small:

- Fix unsafe code examples.
- Fix Luau syntax.
- Fix inaccurate Roblox API usage.
- Fix stale links or repository names.
- Clarify confusing instructions.

If a patch changes how the assistant should behave in a major way, it is not a patch.
