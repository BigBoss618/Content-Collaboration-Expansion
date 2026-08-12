# Content Collaboration Expansion — Project Context

Last audited: 2026-08-12

This document is the durable briefing for a new Codex conversation. Counts and content lists are a dated snapshot; rescan the repository before relying on them.

## What is Europa Universalis V?

Europa Universalis V (EU5) is Paradox Interactive's historical grand-strategy game. Its moddable content is organized into script databases, localization, and graphical assets across lifecycle top folders such as `in_game` and `main_menu`. This project adds new database objects rather than changing the game executable.

## Project locations

- CCE mod: `C:\Users\Vasilis\Documents\Paradox Interactive\Europa Universalis V\mod\Content Collaboration Expansion`
- EU5 base-game reference: `D:\SteamLibrary\steamapps\common\Europa Universalis V\game`
- GitHub: https://github.com/BigBoss618/Content-Collaboration-Expansion
- Git remote `origin`: `https://github.com/BigBoss618/Content-Collaboration-Expansion.git`
- Primary branch at the last audit: `main`

The similarly named sibling folder `Content Expanded` is not this project. Never place CCE work there. The installed base game is reference-only.

## Project aim

CCE aims to increase content variety and historical flavour without losing EU5's internal structure. Its main content lanes are:

- Country- and culture-specific regular units and levies.
- Advances that unlock those units or new buildings.
- Generic and thematic military/economic buildings.
- Appropriate construction and maintenance goods.
- Custom prices where a design needs them.
- English localization for every player-facing key.
- EU5-style unit illustrations, masks, building icons, and advance icons.

Medieval II: Total War and its Kingdoms expansions are recurring inspiration for names, roles, descriptions, and buildings. Source descriptions should be researched and then adapted into original, polished EU5 content rather than copied mechanically.

## Implemented content snapshot

### Units and levies

- **Byzantium:** 20 variants across Byzantine Lancers, Latinikon, Skythikon, Byzantine Guard Archers, Trebizond Archers, Archontopoulai, Mangonels, Vardariotai, Dromons, and Lanternas.
- **France:** Voulgier, Grand Bombard, and Serpentine. At the snapshot date all three are Age 2; the two artillery units inherit default artillery demands and have no custom modifiers.
- **Hungary:** 10 regular variants across Royal Banderium, Croat Axemen, Magyar Cavalry, Bosnian Archers, and Peasant Archers, plus a Peasant Archer levy definition.
- **Milan:** 9 variants across Broken Lances, Milanese Cavalry Militia, Famiglia Ducale, and Dismounted Broken Lances, plus two cavalry-militia levy definitions.
- **Muslim and regional groups:** 7 variants across Sudanese Javelinmen, Lamtuna Spearmen, Nubian Spearmen, and the Baghlah.
- **England:** country files exist but were empty at the snapshot date.

There were 46 packaged unit illustrations and 46 matching black masks at the last audit.

### Buildings

Eleven custom building definitions existed at the last audit:

- Militia Drill Square
- Town Watch
- Militia Barracks
- Town Guard
- City Watch
- Stables
- Swordsmith Guild
- Master Swordsmith Guild
- Armourers' Guild
- Grand Mercantile Exchange
- Royal Arsenal

The buildings include age progression, settlement restrictions, employment, production methods, goods needs, caps, advances, localization, and art as appropriate. The Royal Arsenal has an original cannon-foundry building icon shared with its advance.

### Supporting systems

- Country-separated advance and unit files.
- Country-separated price and goods-demand files.
- Generic building advances, production methods, employment values, caps, localization, and icons.
- Modifier-type definitions, icon mappings, and localization for custom price-generated cost modifiers where needed.
- One deliberate vanilla advance replacement currently exists in `CE_age_of_renaissance.txt`; review it through the compatibility skill before changing it.

## Available skills

Project-relevant installed skills at the last audit:

- `eu5-mod-compatibility`: database entry modes, load order, folder exceptions, localization overrides, and compatibility review. Use for every EU5 scripting task.
- `eu5-add-advance`: creates, replaces, retires, repairs, and validates advances, full age-tree rewrites, prerequisites, unlocks, localization, AI bias, research costs, and icon behavior.
- `eu5-add-unit`: creates and validates regular units, ships, militia and levies, including variants, unlock advances, demands, prices, upgrades, localization, illustrations, and masks.
- `eu5-add-modifier-type`: creates or repairs modifier-type definitions, modifier icon mappings, and NAME/DESC localization, especially for custom prices and `Missing modifier type` logs.
- `imagegen`: generates and packages raster unit illustrations, masks, and icons. Its EU5 unit helper produces the current DXT1/mipmap contract.
- `skill-creator`: available to formalize more of this accumulated workflow into reusable installed skills.

The original pasted Advances instructions and later verified wiki/game findings are now incorporated into the installed `eu5-add-advance` skill. The installed game remains authoritative because the EU5 Advance Modding wiki is marked as last verified for version 1.0.

## Advance-tree redesign and retirement policy

CCE is preparing to rework the basic common advances in the Age of Renaissance. These are the broadly available entries in `0_age_of_renaissance.txt`, not advances gated by tags, cultures, governments, religions, country types, or the `adm`/`dip`/`mil` age preference unless explicitly brought into scope.

### Tree mechanics

- Every advance requires an `age`.
- A non-root advance supports one graphical parent through a single `requires` field.
- The required parent must be defined and loaded before the child.
- An intentional root omits `requires` and uses `depth = 0`; an otherwise unparented advance may be placed semi-randomly.
- `allow_children = no` marks a deliberate leaf, prevents random children, and exposes an explicit child reference as an error.
- Extra logical prerequisites can be placed in `allow`, commonly with `has_advance`, but do not create another graphical tree edge.
- CCE does not create cross-age `requires` relationships.
- Ordinary common content must not require a `for = adm`, `dip`, or `mil` choice advance, because countries selecting another preference could never reach it.

### Preferred approach: replace the concept, preserve the key

Treat a vanilla advance key as an interface used by other content. When anything references it, retain the key and replace the complete definition with the redesigned concept:

```txt
# Old vanilla concept:
# old_advance = {
# 	age = age_2_renaissance
# 	requires = renaissance_advance
# 	old_modifier = 0.10
# }

REPLACE:old_advance = {
	age = age_2_renaissance
	icon = verified_new_icon

	requires = verified_new_parent

	new_modifier = 0.10
}
```

Override `old_advance` and `old_advance_desc` in `main_menu/localization/english/replace/`. This removes the old concept from the player-facing tree while preserving compatibility for culture, country, government, religion, DLC, age-choice, event, setup, save, and other scripts that still refer to the identifier.

### Peter's unused-key suppression exception

Peter may suppress a vanilla advance only after an exact-key search across the complete installed base game, every installed DLC, and CCE finds no functional reference outside its own definition. Its localization and a same-named icon are not functional dependencies.

Use a complete valid replacement rather than a partial injection:

```txt
REPLACE:unused_old_advance = {
	age = age_2_renaissance
	allow_children = no

	potential = {
		always = no
	}
}
```

This hides the advance; it does not delete the database key. Do not use this exception if any of the following reference the key:

- another advance's `requires`, including a tag, culture, culture-group, government, religion, DLC, or `for = adm/dip/mil` advance;
- `has_advance`, `has_advance_available`, `can_research_advance`, or `advance_type:<key>`;
- `research_advance` or another scripted effect;
- setup or starting research, AI preferences, events, missions, scripted triggers/effects, unlock logic, or another database.

If a functional dependency exists, keep the original key as a redesigned working node or migrate every dependency to a verified same-age replacement before suppressing it.

### Physical omission and full-file replacement

EU5 does not currently document an object-level `DELETE:<key>` operation for advances. A mod file with the exact vanilla relative path and filename replaces the complete vanilla file, so omitting a key from a CCE `in_game/common/advances/0_age_of_renaissance.txt` makes that vanilla file contribution absent.

Apply the same dependency audit before physical omission. Reparent or replace every child and rewrite every scripted reference first. A full-file replacement gives complete control over the common Renaissance tree but is intentionally broad: another mod replacing the same relative file will conflict according to playset order.

For each advance considered for retirement, record one of three outcomes:

1. **Preserve and redesign the key** — default when any dependency exists.
2. **Suppress with `always = no`** — Peter's exception for a proven unused key.
3. **Physically omit and migrate references** — only after a complete dependency rewrite or proof that none exist.

Reference: https://eu5.paradoxwikis.com/Advance_modding

## Important learned rules

- Do not create cross-age `requires` links between advances.
- Give each non-root advance exactly one earlier-loaded graphical parent; use `depth = 0` for roots and `allow_children = no` for deliberate leaves.
- For widely available advances, inspect the matching vanilla `0_age_of_<age>.txt` file and choose varied, sensible same-age prerequisites.
- Unit-unlock advances should usually follow the vanilla unlock for the same category and era.
- Preserve referenced vanilla advance keys as compatibility anchors and redesign them with complete `REPLACE` definitions.
- Allow Peter to suppress an advance with `potential = { always = no }` only after a full exact-key audit proves the key has no functional use anywhere in the base game, installed DLC, or CCE.
- Before suppressing, renaming, or omitting an advance, audit `requires`, `has_advance`, `has_advance_available`, `can_research_advance`, `advance_type:`, `research_advance`, setup, AI, events, missions, and other database references.
- New content uses new keys; do not use `INJECT` or `REPLACE` by habit.
- Unit modifiers are direct fields, not a `modifiers = {}` container.
- Use no more than four goods in each custom unit construction or maintenance demand.
- When default demand is desired, omit the override entirely and delete dead demand entries and localization.
- Custom price objects can generate required `<price_key>_cost_modifier` database entries; use the modifier-type skill.
- Levies have separate definitions and availability logic even when their equipment is based on a unit type.
- Validate upgrade paths against unit role and category.
- Later-age variants need visibly progressive names and artwork.
- Building production methods are mandatory where the building database expects them.
- Employment and manpower efficiency must be compared against vanilla age limits.
- Localization and some script databases require UTF-8 BOM.
- Preserve user changes in a dirty worktree and only touch Content Collaboration Expansion.

## New-conversation startup checklist

1. Confirm the task targets Content Collaboration Expansion, not Content Expanded.
2. Read the root `AGENTS.md` and this context document.
3. Check `git status` and preserve unrelated changes.
4. Load the applicable installed skill instructions.
5. Inspect the exact CCE objects and comparable installed vanilla objects.
6. Verify the current age, category, prerequisite, encoding, and asset contract.
7. Implement, then validate references, duplicate keys, braces, BOM, and DDS headers.

## Recommended future additions

1. Create dedicated installed skills for `eu5-add-unit`, `eu5-add-advance`, and `eu5-add-building` so the complete workflows—not only project rules—are reusable in every conversation.
2. Add a lightweight validation script that checks brace balance, missing references, duplicate keys, localization BOMs, maximum goods per demand, and DDS dimensions/compression/mips.
3. Add a root README for human contributors with installation instructions, supported EU5 version, design philosophy, and contribution workflow.
4. Record the tested EU5 version and mod version in a small changelog; base-game updates can invalidate templates, modifiers, or load behavior.
5. Maintain a content index generated from live files rather than manually updating unit counts.
6. Add balance guidelines by age and category: baseline strength, manpower, gold cost, maintenance, goods demand, and acceptable modifier ranges.
7. Decide a consistent availability policy for tag-specific, culture-specific, regional, and religion-group content.
