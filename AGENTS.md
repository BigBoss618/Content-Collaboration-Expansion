# Content Collaboration Expansion — Codex Instructions

## Project identity

- This repository is the Europa Universalis V mod **Content Collaboration Expansion** (CCE).
- Repository: https://github.com/BigBoss618/Content-Collaboration-Expansion
- Local mod root: `C:\Users\Vasilis\Documents\Paradox Interactive\Europa Universalis V\mod\Content Collaboration Expansion`
- Installed base-game data: `D:\SteamLibrary\steamapps\common\Europa Universalis V\game`
- Treat the base-game directory as read-only reference material.
- Never add or change this project's content in the sibling `Content Expanded` mod. If the active workspace is `Content Expanded`, operate on the CCE path above or stop if the requested target is ambiguous.
- Read `PROJECT_CONTEXT.md` before substantial design or implementation work.

## Purpose

CCE expands EU5's historical content variety: country- and culture-flavoured units, advances, levies, buildings, prices and goods demands, localization, and matching UI art. Medieval II: Total War is a frequent source of inspiration, but the EU5 implementation must be original, historically plausible, balanced against installed vanilla data, and valid for EU5's script databases.

## Required workflow

1. Inspect the live CCE definition, comparable vanilla objects, and the installed game version before editing.
2. Use the installed `eu5-mod-compatibility` skill for every EU5 scripting task.
3. Use `eu5-add-advance` whenever creating, replacing, retiring, repairing, or reviewing advances or their localization.
4. Use `eu5-add-unit` whenever creating, changing, or reviewing regular units, ships, militia, levies, unit demands, prices, upgrades, or unit art.
5. Use `eu5-add-modifier-type` whenever a custom price or modifier causes or could cause a generated `*_cost_modifier` entry.
6. Use the `imagegen` skill for new raster illustrations or icons and follow its EU5 packaging instructions.
7. Preserve unrelated user changes in the dirty worktree. Do not push, publish, or destructively reset without an explicit request.
8. Validate brace balance, references, duplicate keys, encodings, and asset headers before finishing.

## Scripting conventions

- Mirror EU5 paths beneath the correct top folder (`in_game` or `main_menu`); never add an extra `game` directory.
- Prefer unique `CE_*.txt` filenames and new `ce_`, `a_ce_`, `n_ce_`, or `levy_a_ce_` keys.
- New database objects use ordinary definitions. Use `INJECT`, `REPLACE`, or their variants only when deliberately modifying an existing key according to the compatibility skill.
- An advance may never require an advance from another age.
- Every non-root advance has exactly one graphical parent through `requires`; the parent must load before the child. Intentional roots omit `requires` and use `depth = 0`.
- Advances without `requires` may be semi-randomly placed. Use `allow_children = no` for deliberate leaves, and never make another advance require such a leaf.
- Additional logical prerequisites may use `allow = { has_advance = other_advance }`, but they do not create additional tree edges.
- When choosing prerequisites for widely available custom advances, scan only the corresponding vanilla `0_age_of_<age>.txt` file. For a unit advance that must follow its vanilla unit family, verify the same-age vanilla unit-unlock advance as well. Vary sensible prerequisite sources instead of attaching everything to one node.
- Treat referenced vanilla advance keys as compatibility anchors. Prefer a complete `REPLACE:old_advance = { ... }` redesign under the original key, plus replacement localization, over deleting or hiding the key.
- Peter may suppress an advance through a complete replacement containing `potential = { always = no }` only after an exact-key search of the installed base game, all installed DLC, and CCE finds no functional reference outside the advance's own definition. Localization and same-named art do not count as functional references.
- Do not suppress or omit an advance used by culture, country, government, religion, DLC, or `for = adm/dip/mil` advances, or by triggers, effects, setup, AI, events, missions, or other scripts. Preserve and redesign the key or deliberately migrate every dependency.
- Physically omit a key from an exact-path full-file replacement only under the same no-dependencies condition or after all references have been migrated. Full-file replacement is broad and conflicts with other mods replacing the same file.
- Country-specific advances should normally use `potential = { has_or_had_tag = TAG }` unless the design specifies culture, religion, or another scope.
- Localize unit advances through the unit: `advance: "$unit_key$"` and `advance_desc: "$unit_key_desc$"`.
- Later-age unit variants need progression in their displayed names (for example Late, Renaissance, Veteran, or another appropriate term). Reuse descriptions through `$earlier_key_desc$` when requested.
- Unit modifiers are written directly in the unit definition. Do not create a `modifiers = {}` block. If no modifiers are requested, omit modifier fields.
- A custom unit construction or maintenance demand may use at most four goods. If default category demands are requested, omit the unit-level demand fields and remove unused custom demand objects and localization.
- Check `upgrades_to` against role and category; spearmen should not arbitrarily upgrade to crossbowmen, for example.
- Preserve UTF-8 BOM where required, especially localization and levy files. Localization begins with `l_english:`.

## Art contracts

- Unit illustration filename: `<unit_category>_<unit_key>.dds`, with the identical filename under `main_menu/gfx/interface/illustrations/units/masks/`.
- Current CCE unit-card contract: `1080x440`, DXT1/BC1, 11 mip levels, 317,512 bytes. Masks use the same contract and are solid black.
- Unit cards normally show exactly three prominent members of the unit in slightly different poses, in EU5's realistic painterly historical style. Age variants must look visibly more advanced while preserving culture, role, and defining weapon.
- Current building-icon contract: `128x128`, transparent DXT5, 8 mip levels.
- Current advance-icon contract: `256x256`, transparent DXT5, 9 mip levels.
- Inspect comparable live assets before relying on these values; the project files are authoritative.

## Handoff

- State exactly what changed and what was validated.
- Link the principal CCE files using absolute paths.
- Mention assumptions about age, country availability, pricing, or balance.
- Do not claim the game itself was tested unless EU5 was launched and its logs were inspected.
