# Redridge Recipe Planner — developer and AI collaboration rules

**Original-idea credit:** Danoozy, as preserved by the project prior to this audit.  
**Implementation, creative direction and MIT copyright:** Babuni.dev.  
**Version:** 0.1.1-beta · **Target:** World of Warcraft: Forever 1.60.1 / interface 16001.

## Respect separate human contributions

The original contributor guidance credited **Danoozy with the original idea** and Babuni.dev with implementation and artistic direction. The initial exchange was not in the inspected Git record; don't fabricate what Danoozy requested, claim they wrote code without evidence, or attribute the original idea entirely to AI or Babuni.dev. Preserve that distinction in README, concept, DEVLOG and collaboration notes.

Keep the concept a **character-specific Merchant's Favor recipe and gathering planner**, not a shop automation/currency spending tool, item crafting macro, dynamic auction flipper or universal live vendor scanner.

## Source invariants

- Catalog contains **309 beta-listed recipes across 7 professions**, keyed by `profession|name`. These are curated external guide claims as of **October 15, 2026**, not verified live vendor prices.
- The installed addon folder is `RedridgeRecipePlanner`, containing **Catalog.lua**, **Core.lua**, **UI.lua** and the appropriate interface **16001 TOC**. Both TOCs should stay consistent.
- Preserve separate **per-character `p.plan`, `p.known`, `p.roles`, `p.profession`, `p.page`, `p.showAll`, `p.expandIntermediate`**. Overall account-wide `RRPDB` stores catalog augmentations/schematics, scroll IDs, discovered entries and user trainer marks.
- `RRP.GetProfile()` migrates incomplete legacy character records **without discarding selections**. `UI.Refresh()` has defensive fallbacks for mixed/old tables. Do not reintroduce the `UI.lua:273` `nil known` crash from the 0.1.0-beta period.
- Distinguish **recipe scroll purchase** from **number of crafts**. Favor is summed **once per selected recipe**; reagents scale by quantity. Unknown Favor must remain marked incomplete, never counted as known zero.
- No invented item IDs, reagents, trainer duplicates or live vendor prices. Vendor scans link actual observed scroll IDs or cautiously import new names with **unknown Favor**.
- A profession scan may see reagent schematics for **unlearned recipes**; cache observed materials but mark `known` only if `GetRecipeInfo().learned` actually reports true.
- Intermediate expansion requires a **complete schematic learned by current character**, uses output quantities, cycle guard, and must be user-toggleable.
- Owner quantity is checked via **GetItemCount(itemID,false)**, limited to bags; never claim bank/alt/guild stock is included.
- **Paladin-tagged designs stay opt-in** for non-Paladins despite shared Tank/Melee role labels. Show All Builds intentionally overrides.
- Trainer notes are user-marked **unverified** observations, not a verified trainer catalog.
- Source uses English recipe names and localized UI assumptions may fail; don't claim full translations.
- The original **MIT LICENSE** under Babuni.dev remains unchanged. External WoW Professions data and WoW assets require distinct attribution.

## Change/test/review workflow

1. Read README, CONCEPT, ROADMAP, docs/ARCHITECTURE, docs/DEVLOG, docs/DECISIONS and all three source modules before code edits.
2. Record user intent, Danoozy idea credit, Babuni.dev's creative decision and any AI proposal **as separate items**.
3. Make reviewable changes; for code run `luac5.1 -p RedridgeRecipePlanner/*.lua`, `lua5.1 tests/catalog.lua`, `lua5.1 tests/core.lua`, `lua5.1 tests/ui.lua`; collect actual output and CI status. Never invent test passes.
4. Test functionality in real Forever beta when possible, including vendor lists, chosen character known state and old SavedVariables. Mock tests are not live game-client proof.
5. Preserve third-party provenance and use synthetic fixture data; don't publish private character profiles or local account paths.
6. Update README, CONCEPT, ROADMAP, CHANGELOG, AGENTS and docs/ARCHITECTURE, DESIGN, CONTENT, DECISIONS, DEVLOG, AI_COLLABORATION with substantial implementation.

The October 27 docs-only audit must **not modify Lua, TOC, workflow, tests or LICENSE**.
