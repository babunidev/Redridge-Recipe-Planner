# Redridge Recipe Planner

**Developer and creative direction:** Babuni.dev · **Original-idea credit:** Danoozy (as recorded in the project's prior contributor instructions) · **License:** [MIT](LICENSE)

A WoW **Forever (1.60.1)** addon for planning recipes sold for **Merchant's Favor** at the Alliance **Azeroth Commerce Authority** in Redridge Mountains and the Horde **Durotar Supply and Logistics** camp.

**Status:** **0.1.1-beta**, first published in source on October 15, 2026. The 309-recipe catalog, planner code, automated Lua fixtures and GitHub Actions workflow are present; **in-game verification remains outstanding**. GitHub currently lists **no published releases or downloadable release assets**. This is a source repository, not a separately packaged release.

## Installation

**Updating from an older version:** replace the entire `RedridgeRecipePlanner` addon folder, not only `UI.lua`. Restart the game fully (or use `/reload` after confirming all Lua files were replaced). Saved character plans are retained by the migration.


1. Download this repository from GitHub (**Code → Download ZIP**), or clone it.
2. Copy **only** the `RedridgeRecipePlanner` folder into your Forever install:

   `World of Warcraft/_classic_beta_/Interface/AddOns/RedridgeRecipePlanner/`

3. The final folder must contain `RedridgeRecipePlanner_Camelot.toc`, `Catalog.lua`, `Core.lua`, and `UI.lua`.
4. Restart WoW, enable **Redridge Recipe Planner** at the AddOns screen, and type **`/rrp`**.

The addon targets interface **16001**, corresponding to the observed Forever 1.60.1 beta. If an update changes the interface, check `/run print(select(4,GetBuildInfo()))` before changing the TOC.

## How the planner handles evidence

**Known is per character; reagent schematics are reusable account data.** The addon stores profiles keyed by its own current `GetRealmName() .. "-" .. UnitName("player")` expression inside account-wide `RRPDB.characters`. The profile contains that character's plan, filters, current profession, page, intermediate preference, and an independently scanned `known` table. `RRPDB.materials`, discovered vendor recipes, scroll IDs and trainer annotations are shared in the overall saved database. A schematic cached while viewing an **unlearned** recipe may be useful, but **does not mark that recipe as learned**.

**Favor is paid per selected recipe, not per craft.** If you select one recipe and increase planned craft quantity from 1 to 3, its published Merchant's Favor price is included **once**, while its known reagent quantities are multiplied by **three**. Unknown Favor on newly discovered vendor items is flagged as unknown and is not quietly treated as zero. Current Favor totals are **purchase requirements**, not a wallet balance or proof you have paid.

**Missing ingredients are not free.** Material calculations include only actual client-returned reagent schematics, preserve `incomplete` when a slot cannot be resolved, and report selected recipes with unknown ingredients separately. Optional intermediate expansion works only when there is a complete schematic for an intermediate recipe **known to that character**. The displayed owned quantity checks **bags only**, not a bank, guild bank, mail, auctions or other characters.

**Trainer notes are not verification.** The “Mark trainer match” control stores a *user-supplied*, unverified note. The published catalog doesn't contain independently verified trainer duplicates. Similarly, the catalog is an October 15 **beta listing**, not authoritative current in-game vendor inventory; scanning a live vendor is the validation path.

## 0.1.1-beta fix

- Fixed a Lua error on opening the planner when the character's saved `known` recipe table was missing (`UI.lua:273`).
- Migrates partial character SavedVariables profiles safely and adds UI regression coverage.

## Features

- A static catalog of **309 beta-listed recipes across 7 professions**, with required level, profession skill, Merchant's Favor and reputation. The Alliance and Horde camps sell the same recipes.
- Choose a profession and combine Tank, Melee, Healer, Caster, Hunter, Paladin and Utility role tags. The selected character's class supplies initial defaults; **you decide the filters**. Paladin-tagged items stay hidden on a Warrior unless Paladin is explicitly selected. **Show All Builds** bypasses relevance filtering.
- Plan how many times to craft each selected recipe. Merchant's Favor is counted **once per chosen recipe**, not once per craft.
- Each character remembers its build filters, profession and craft quantities.
- With a profession window open, **Scan Profession** (or `/rrp scan`) attempts to save the player's learned recipes and exposed reagent schematics. Materials shown without a schematic are explicitly **unknown**, never zero.
- Required reagents are consolidated across chosen crafts and compared with bag quantities. Known intermediate recipes can be expanded into their ingredients (toggle via **Intermediate**).
- **Scan Open Vendor** (or `/rrp vendor`) can associate scroll item IDs with catalog recipes or import additional recipe scrolls. Choose the relevant profession before scanning the merchant.
- Trainer comparison defaults to **not verified**. Individual recipes can be *user-marked* as also available from trainers; this is a personal annotation, not an automatic verification. The alchemy comparison *Elixir of Lesser Intellect* versus *Elixir of Wisdom* is shown as a similar-effect alternative, **not** as identical recipes.

## Controls

| Command / control | Action |
| --- | --- |
| `/rrp`, `/redridge` | Open/close planner |
| `/rrp scan` | Scan currently opened profession |
| `/rrp vendor` | Scan currently open camp vendor after selecting profession |
| `/rrp resetview` | Reset visible filters and pagination |
| `/rrp list` | Open the complete shopping list |
| `+` / `-` on a recipe row | Increase/decrease planned craft count |
| Role buttons | Toggle one or more desired builds |
| Show All Builds | View every recipe in the chosen profession |
| Mark trainer match | Toggle user-supplied, unverified trainer note |

## Data provenance and limitations

- Catalog names, basic requirements and prices were transcribed as factual records from [WoW Professions' Forever Merchant's Favor guide](https://www.wow-professions.com/forever/merchants-favor-durotar-supply-azeroth-commerce), consulted **15 October 2026**. This is **beta data** and may change; the vendor scan should be used to double-check local listings.
- **No pre-populated reagent amounts, trainer duplicates or item IDs are invented.** Reagent information is collected from the in-game API when accessible. Scanning a trainer's recipes remotely is not supported.
- The planner currently matches the catalog's **English** recipe names against the profession UI. Non-English game locales may have incomplete matches.
- Material expansion only uses recipes for which the current user has captured a reliable schematic. Unresolved alternative reagent slots remain partial, and ownership counts cover **bags, not bank, guild bank or auction house**.
- There is no verified currency-wallet detection in this first version; Merchant's Favor values represent **purchase requirements**, not a subtraction from the character's balance.
- Recipe item links and live profession state depend on the Forever beta client. In-game compatibility has **not** been independently confirmed.
- The planner previews the five largest material entries; **Full Shopping List** (or `/rrp list`) opens the complete paginated list, including missing quantities and recipes with unknown materials.
- A recipe yielding an apparently weaker item may still use cheaper materials. This addon never automatically labels it worthless.

## Development

Code is Lua 5.1 compatible and intentionally has no external libraries.

The GitHub Actions workflow checks syntax and runs catalog invariants and planner math tests. For a local syntax check:

```bash
luac5.1 -p RedridgeRecipePlanner/*.lua
lua5.1 tests/catalog.lua
lua5.1 tests/core.lua
lua5.1 tests/ui.lua
```

Do not rename the installed addon folder; its name must match `RedridgeRecipePlanner.toc`.

## Provenance, licensing and test evidence

The catalog's 309 baseline records (seven professions) are transcribed from the linked [WoW Professions' Merchant's Favor guide](https://www.wow-professions.com/forever/merchants-favor-durotar-supply-azeroth-commerce), consulted October 15, 2026. These are **beta vendor data**, not independently rechecked current values. The project's preceding [AGENTS.md](AGENTS.md) specifically preserved **Danoozy's original-idea credit** separately from **Babuni.dev's implementation and artistic direction**; the original exchange or first-hand account was not included in this source audit.

The repository carries an explicit [MIT License](LICENSE) under **Babuni.dev's** copyright notice. Third-party vendor information and World of Warcraft client assets are not automatically covered by that code license.

Automated [catalog](tests/catalog.lua), [planner logic](tests/core.lua) and [UI smoke](tests/ui.lua) fixtures are committed, and a [GitHub Actions Lua workflow](.github/workflows/lua.yml) is configured to run them and Lua 5.1 syntax checks. The fixture descriptions demonstrate *intended coverage*, but this documentation audit did **not** execute them, inspect their latest CI run, or launch a Forever client. No screenshots were included in the inspected tree.

## License

See the existing [MIT LICENSE](LICENSE).

## Development documentation

- [Concept and collaboration origins](CONCEPT.md) · [Verification roadmap](ROADMAP.md) · [Git-based changelog](CHANGELOG.md)
- [Technical architecture](docs/ARCHITECTURE.md) · [Interface design](docs/DESIGN.md) · [Recipe and data content](docs/CONTENT.md)
- [Decisions and alternatives](docs/DECISIONS.md) · [Narrative development journal](docs/DEVLOG.md) · [Human–AI collaboration](docs/AI_COLLABORATION.md)
- [Development and AI rules](AGENTS.md)

The journal keeps user direction, Danoozy's recorded original-idea credit, Babuni.dev's creative/implementation work, source-backed iteration, AI documentation contributions, and test limitations distinct.
