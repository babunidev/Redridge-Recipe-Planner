# Redridge Recipe Planner — testing and development roadmap

**Creative/implementation authority:** Babuni.dev. **Original idea credit preserved:** Danoozy.  
**Scope:** current **0.1.1-beta**. These are proposed validation tasks and open choices, **not author-approved new implementations**.

## Existing source-backed baseline

- 309 curated beta entries in seven professions: **Alchemy 29**, **Blacksmithing 61**, **Cooking 6**, **Enchanting 39**, **Engineering 26**, **Leatherworking 81**, **Tailoring 67**.
- Profession tabs, player-adjustable role relevance filters, explicit Paladin opt-in for other classes and Show All Builds.
- Per-character plan count (0–999 per entry), current profession, page, class-derived initial roles, intermediate-material option and `known` state.
- Separate account-wide schematic cache, vendor scan discoveries, scroll IDs and user-supplied trainer flags.
- Merchant's Favor **once per selected recipe**, materials multiplied by planned craft quantity; bag-only owned counts, expandable verified intermediate recipes, unknown/incomplete ingredient handling.
- Native `C_TradeSkillUI` profession scanner and `GetMerchant*` vendor scanner with conservative normalized English-name matching.
- 920×630 movable planner, 11 recipe rows per page, 610×582 full shopping list with 15 entries per page.
- Legacy/mixed profile safeguards added after a real reported `UI.lua:273` crash, plus pure Lua regression fixtures and GitHub Actions workflow.
- Original **MIT LICENSE**. **No GitHub Releases** entries or screenshots found as of October 27 audit.

## P0 — verify the application in the actual client

1. Install only the `RedridgeRecipePlanner` folder under `_classic_beta_/Interface/AddOns`, and test the correct **16001** variant TOC. A full replacement of all three Lua source files avoids mixed 0.1.0/0.1.1 code.
2. Launch WoW: Forever 1.60.1, record actual build, Lua errors and whether both manifest naming choices load correctly in the intended beta distribution.
3. Use `/rrp` and `/redridge` to open/close the window; validate selection, next/previous paging, role buttons, Show All Builds, quantity +/−, and the Full Shopping List.
4. Verify *existing* character profiles created by 0.1.0, including missing `known`, `plan`, `roles`, `profession`, `page`; ensure migration preserves previously planned recipes and never crashes the UI.
5. Visit the **Alliance Azeroth Commerce Authority** and **Horde Durotar Supply and Logistics** camp on suitable test characters. Confirm advertised recipe names, requirements, reputation and Merchant's Favor against in-game vendor listings. Don't assume the beta guide remains current.
6. With one profession open, run `/rrp scan`; record which `C_TradeSkillUI` methods actually exist and whether **unlearned** recipe schematics can be read. A cached schematic must not flag an unlearned recipe as known.
7. Scan vendor after choosing its profession; verify existing scroll IDs are linked correctly and new scroll recipes are imported **without imaginary Favor costs** or wrong profession associations.
8. Test bag ownership, two selected recipes sharing reagents, quantities greater than one, and nested intermediate recipes known vs unknown; check recursion guard and alternative/partial schematic slots.
9. Check the **trainer note** control: user-marked status should stay explicitly unverified. Verify *Elixir of Lesser Intellect* versus *Elixir of Wisdom* is labeled a **similar-effect alternative**, not as identical trainer availability.

## P1 — automate and capture evidence

10. Run `luac5.1 -p RedridgeRecipePlanner/*.lua` and `lua5.1 tests/catalog.lua`, `tests/core.lua`, `tests/ui.lua`; capture actual output plus interpreter version. The source contains the tests and a workflow but **this audit did not execute them or check a workflow run**.
11. Verify the GitHub Actions workflow actually completes on the latest commit, rather than treating the YAML alone as a successful CI result.
12. Add a fixture for **partially incomplete schematic** alternatives, stale data, missing vendor APIs, unknown Favor and out-of-date catalog matches.
13. Examine whether source's normalized-name match can associate **same-name recipes across professions**: currently an opened profession scan builds name-only matches, which could create cross-profession associations if names collide.
14. Test character names and the saved profile key created by `GetRealmName() .. "-" .. UnitName("player")` under the intended Forever naming model; do not assume a separate-realm player-identity workflow.
15. Test localization: catalog's English names/role tags and recipe-prefix parsing may not match translated client strings.
16. Test both addon manifest files for consistency after every release and test the workflow checks them indirectly by loading each Lua source.

## P2 — user experience, release and documentation

17. Evaluate **920px-wide fixed window** and **610px shopping pane** at common screen sizes and accessibility settings; add visual screenshots only after actual client testing and user review.
18. Check shopping page size (15), sort by most missing, “?” unknown recipes, 5-item inline preview and long translated item-name clipping.
19. Assess whether class-default filters are clear enough that hidden recipes are never mistaken for deleted catalog data.
20. Decide whether the catalog needs dated provenance and revision checks at release time; maintain third-party [source credit](https://www.wow-professions.com/forever/merchants-favor-durotar-supply-azeroth-commerce).
21. Publish a versioned release only once installer and actual in-game compatibility have been verified; the project currently has no published GitHub Releases.
22. Preserve MIT copyright, Danoozy's original-idea credit and Babuni.dev's authored implementation/creative direction.

## Open creator choices

Should the shopping list support bank/account inventories or remain bag-only? Should trainer verification become user crowdsourcing, or remain a private note? Would a more compact table help? Should a catalog snapshot include explicit changed-price warnings? How much cross-character schematic sharing is desirable? These are **unapproved directions**, to record in DECISIONS and DEVLOG when accepted or rejected.
