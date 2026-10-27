# Redridge Recipe Planner — catalog, provenance and saved content

**Original-idea contributor (recorded):** Danoozy. **Developer/creative direction:** Babuni.dev.  
**Scope:** snapshot of third-party **October 15, 2026 Forever beta** listings, not authoritative changing vendor inventory.

## Catalog composition

The source catalog `RedridgeRecipePlanner/Catalog.lua` registers exactly **309 entries** with unique **profession|recipe name** keys:

| Profession | Entries |
| --- | ---: |
| Alchemy | 29 |
| Blacksmithing | 61 |
| Cooking | 6 |
| Enchanting | 39 |
| Engineering | 26 |
| Leatherworking | 81 |
| Tailoring | 67 |
| **Total** | **309** |

Each built-in recipe record includes **name**, **profession**, **required character level**, **required profession skill**, **Merchant's Favor price**, **reputation** (e.g. None/Friendly/Honored), **role tags** and `source="vendor-list"`. The key joins profession + name. This catalog does **not** invent **scroll item IDs**, reagent quantities, learned state, trainer availability or live purchase data.

**Source:** [WoW Professions' Forever Merchant's Favor guide](https://www.wow-professions.com/forever/merchants-favor-durotar-supply-azeroth-commerce), explicitly credited by Catalog.lua and README as consulted **October 15, 2026**. A vendor scan can help a player validate local in-game inventory, but doesn't update every listed requirement or price automatically.

The code describes Alliance **Azeroth Commerce Authority** (Redridge Mountains) and Horde **Durotar Supply and Logistics** camps; the README asserts their catalog overlap, a historical claim requiring live check after the beta changes.

## Distinct levels of source confidence

| Data item | Provenance | Meaning |
| --- | --- | --- |
| Catalog cost, level, skill, rep | External Oct 15 beta guide | Historical listing to verify with vendor |
| Added vendor recipe | Actual client merchant scan | Observed name, profession and maybe scroll item ID |
| Known status | Current character's `C_TradeSkillUI.GetRecipeInfo().learned` | Learned only when reported true |
| Reagent quantities | Client `GetRecipeSchematic`, if available | Complete, partial or absent; never guesses |
| Trainer match | Manual player annotation | **Unverified** personal label |
| Bag ownership | `GetItemCount(itemID,false)` | Bags only; excludes bank/other stores |
| Merchant's Favor balance | Not implemented | Purchase requirement sum only, **not** current wallet |

Some client APIs may provide schematics for **unlearned** recipes. Those can be cached without treating the current character as owning the recipe.

The one specific comparison in the catalog is **Elixir of Lesser Intellect** versus trainer **Elixir of Wisdom**, described as **similar effect but different duration and ingredients**, not an identical recipe nor proof of its availability.

## SavedVariables model

`RRPDB` is **account-wide** in the TOC, but `characters` separates each player profile by the program's constructed `GetRealmName .. "-" .. UnitName` key. Profile data include `plan` counts, `known`, `roles`, `profession`, `showAll`, `page` and `expandIntermediate`.

Outside individual profiles, **materials**, **discovered**, **scrollItems**, **playerTrainer** are shared stores. The use of an observed character-key string in source is **not evidence that the underlying Forever naming model has separate game realms or cross-realm collisions**.

**Not stored:** real bank contents, auction listings, current Favor wallet, raw vendor history, other players' scans on a network service, sold-item prices, character equipment stats, payment receipts, or a web database.

## Interface text and assets

The Lua UI embeds **English** labels for profession and role filters, “Scan Profession,” “Scan Open Vendor,” “Intermediate,” “Full Shopping List,” “Mark trainer match,” and “Unknown materials.” There is no locale table or localization folder in the current project.

No custom artwork/screenshot asset directory is in the current Git tree. The interface uses built-in WoW tooltip border/background files and button templates. Third-party game visual assets are not original project art.

## Licensing and publication

[LICENSE](../LICENSE) is **MIT**, with 2026 Babuni.dev copyright. That applies to the code as licensed in this repository; it does **not** transfer ownership of external guide data, WoW game UI assets, or a collaborator's separately attributable original idea.

The catalog's third-party guide deserves prominent credit in any portfolio write-up. Keep user SavedVariables/character identifiers private; examples in documentation should be synthetic.
