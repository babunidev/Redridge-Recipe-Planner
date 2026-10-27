# Redridge Recipe Planner — architecture and persistence

**Inspected code:** `Catalog.lua` (263 lines), `Core.lua` (363 lines), `UI.lua` (330 lines), two identical 16001 TOC manifests, three Lua fixtures and GitHub Actions workflow, October 27, 2026. Static review only.

## Module load flow

```text
RedridgeRecipePlanner/RedridgeRecipePlanner.toc
RedridgeRecipePlanner/RedridgeRecipePlanner_Camelot.toc
  Interface: 16001 | Version: 0.1.1-beta
  SavedVariables: RRPDB (account-wide)
  Catalog.lua --> RRP.Catalog, RRP.ByKey, professions, roles, source
  Core.lua ----> profiles, filters, recipe API scans, vendor scan, math, commands
  UI.lua ------> 920x630 planner, 610x582 shopping, paging, controls
```

WoW passes `addonName, RRP` through the addon private table to all three files. The addon has no third-party runtime library or web fetch; the external guide is a **compiled-in beta data snapshot**, not a live HTTP request.

## The catalog and identity model

`Catalog.lua` defines each record with `profession`, `name`, `level`, `skill`, `favor`, `reputation`, `roles`, `key` (`profession .. "|" .. name`) and `source="vendor-list"`. It rejects duplicate keys when loaded. The seven profession counts checked by fixture are **29 Alchemy, 61 Blacksmithing, 6 Cooking, 39 Enchanting, 26 Engineering, 81 Leatherworking, 67 Tailoring** (309 total).

`RRP.GetRecipes` merges curated catalog items with any newly **vendor-scanned `discovered`** entries, then sorts by profession, level and name. `RRP.GetByKey` consults both stores. Observed discovered entries can lack unknown `favor`, level or skill; these are never manufactured.

`Normalize` trims text, lowercases and removes a leading “Recipe:/Plans:/Formula:/Pattern:” prefix and punctuation. Matching on normalized **name alone** is convenient but may collide across professions or fail in translated client locales. The profession vendor scanner explicitly constrains matching to selected profession; `ScanProfession` builds a name index across all catalog professions, so this is a future collision test point.

## Save model: per-character plans inside an account-wide variable

```text
RRPDB
├── characters
│   └── "<GetRealmName()>-<UnitName(player)>"
│        ├── plan["Profession|Recipe Name"] = craft count (0..999)
│        ├── known[key] = true           # current character learned
│        ├── roles = {Tank?, Melee?, Healer?, Caster?, Hunter?, Paladin?, Utility?}
│        ├── profession = "All"|selected profession
│        ├── showAll = boolean
│        ├── page = 1..
│        └── expandIntermediate = boolean (default true)
├── materials[key] = observed schematic, incl. incomplete/output info
├── discovered[key] = scanned new vendor recipe
├── scrollItems[key] = observed scroll itemID
└── playerTrainer[key] = user-marked unverified trainer note
```

The TOC declares **SavedVariables: RRPDB**, but the profile data is segmented **within** it by the exact expression `GetRealmName() .. "-" .. UnitName("player")`. That's a **code key shape**, not evidence there is a separate-realm gameplay workflow or that player identity requires a conventional cross-realm suffix. There is no `SavedVariablesPerCharacter` TOC declaration.

`GetProfile` creates class-specific role defaults and migrates old/incomplete saved records **in place**, preserving `plan` unless its type is invalid; it ensures `known`, `roles`, `profession`, `page` and `expandIntermediate` exist. The UI also uses table-type fallbacks while refreshing, addressing a prior mixed-version crash.

## Profession and merchant scanners

`RRP.ScanProfession` checks `C_TradeSkillUI.GetAllRecipeIDs`, then `GetRecipeInfo` and, when available, `GetRecipeSchematic`. A matched **learned** recipe sets the current profile's `known[key]`. Reagent schematics can be cached **for learned or unlearned** recipes when the client exposes them. `inspectSchematic` stores only required slots with exactly one identified reagent, marks incomplete if a required alternative or missing ID/quantity cannot be uniquely resolved, and stores `outputItemID` and `quantityMin`.

`RRP.ScanVendor` requires a selected profession and an open vendor; it reads `GetMerchantNumItems`, `GetMerchantItemInfo`, `GetMerchantItemLink`, recognizes recipe-scroll prefixes (Recipe, Plans, Pattern, Schematic, Formula, Design), and links item IDs to catalog keys or adds a discovered record. It doesn't read/deduct an actual Merchant's Favor balance and doesn't invent price/rep for new discoveries.

Profession scanning is **on demand** via button or `/rrp scan`; earlier automatic TRADE_SKILL_SHOW/LIST_UPDATE hooks were removed due to uncertain event/API availability.

## Filters, quantities and costs

The class defaults populate `roles`, but all can be changed by player. `RRP.IsRelevant` first respects Show All Builds, then requires explicit Paladin role for Paladin-tagged recipes, then checks any selected role matching the recipe's comma-separated tags.

Each recipe `plan[key]` is the planned **craft count** capped at **999**, not how many scrolls to buy. `Calculate` loops selected recipes: adds `favor` once if known, otherwise `unknownFavor=true`; for each known schematic, multiplies reagent amounts by the planned quantity. Missing/incomplete schematics are recorded in `unknown` even if some materials were captured.

`RRPDB.materials` is globally reusable schematic data; this does **not** automatically set current character `known`. Intermediate crafting expansion is enabled only when `p.expandIntermediate` and a recipe's output ID has a **complete material schema learned by the current character**. It uses `ceil(requiredAmount / outputCount)` crafts, guards against cycles with a `visited` table, then aggregates base reagents by item ID.

Ownership reads `GetItemCount(itemID,false)` and only reflects **bags**. `result.missing` adds `max(0, required-owned)` per item. No bank, mail, auction house or guild inventory is examined.

`playerTrainer` marks are global **user assertions**, deliberately displayed “user marked (unverified).” The catalog's Elixir of Lesser Intellect/Elixir of Wisdom example is an **alternative**, not an identical trainer duplicate.

## UI, commands and regression history

`UI.lua` builds a **920×630** draggable dark/Blizzard-themed dialog with eight profession buttons including All, eight role buttons including All, **11 recipe rows per page**, recipe detail, plan +/- and a five-entry combined material preview.

The separate **610×582** shopping window has **15 lines per page**, sorted with the greatest missing quantity first and with “?” lines for unknown schematics. The Summary shows selected recipe count and Favor total with incomplete-price warning; the full list has per-material owned/required/missing labels.

Commands: `/rrp` and `/redridge` open/close, `/rrp scan`, `vendor`, `resetview`, `list`. The panels are registered in `UISpecialFrames`; both dialogs can be dragged. **Window positions are not saved** in the current profile.

On October 15 an inconsistency arose after known status moved from account-wide `RRPDB.known` to **per-character `p.known`**. `UI.Refresh` still read the old field, causing a reported `UI.lua:273` nil error in some installs. Commits first updated the UI field, then migrated partially initialized old profiles, and added defensive local fallbacks for mixed-version combinations. The 0.1.1-beta hotfix includes regression fixtures and recommends replacing the **whole addon folder**.

## Verification boundary

Current tests: `tests/catalog.lua` for 309 entries/metadata; `tests/core.lua` for profile migration, Paladin filters, favor and reagent maths, observed scans and known flags; `tests/ui.lua` for mocked window open/refresh/legacy data/shop. GitHub Actions `.github/workflows/lua.yml` declares syntax and all three tests.

The presence of scripts/workflow isn't a pass result: **none was run or its workflow status read in this documentation audit**. Real 1.60.1 vendor, trade-skill and UI behavior remains explicitly unverified in the repo README.
