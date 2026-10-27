# Redridge Recipe Planner — visual and interaction design

**Developer and creative director:** Babuni.dev. **Original idea credit:** Danoozy, as recorded in the repository's earlier AGENTS rules.

## The interface is a planning surface

Redridge Recipe Planner is organized as a *working table* rather than a transient alert or an all-purpose inventory addon. The player first chooses a profession and relevant character roles, then considers recipes one by one, increases quantities, and checks what it would take to make those items.

The in-game window is **920×630 pixels**, using the client tooltip background/border, dark blue-black surfaces and gold/cream headings. Native `UIPanelButtonTemplate` buttons keep the interface consistent with other World of Warcraft panels. No custom icon pack, web images or font files are bundled.

The header explicitly states **“Unverified data is marked”**. That's not just a disclaimer in the README: it is a design rule reflected across recipe, trainer and material information.

## Two sides of the main window

**Left:** eight profession buttons including All, eight role buttons including All, and a list showing **11 recipes per page**. A selected recipe has a yellow prompt arrow; one that the current character has scanned as known gets a blue “(Known)” suffix; a recipe with planned quantity has a green marker. Plus/minus buttons let the user commit to crafting a quantity without actually buying or crafting anything.

**Right:** the selected recipe's name, profession, observed learned status, required level, profession skill, Merchant's Favor, reputation, build tags, trainer comparison and schematic completeness. It also has detail +/- buttons and a “Mark trainer match” toggle. This separation allows browsing and thinking without mistaking a choice for a game action.

**Bottom:** Scan Profession, Scan Open Vendor, Intermediate on/off, Show All Builds, Full Shopping List. These are explicit controls because they have different trust levels: a guide catalog, an observed client scan, and a user-chosen planning interpretation.

## Build defaults are a starting point

The first-time selected roles depend on class, but any role can be toggled. The Show All Builds button overrides relevance entirely. A significant Oct 15 refinement was that **Paladin-tagged items remain opt-in on non-Paladins**, even if a Warrior or another class has also selected Tank or Melee. This prevents a seemingly useful filter from overwhelming the player with another class's gear.

The UI should never imply an item is unavailable just because the current filter hides it. The “No recipes match these filters” state suggests enabling more build categories or choosing All.

## A second, complete shopping surface

The main panel shows the **five largest material entries** as a compact preview with owned/required amounts; if more exist, it says so. A second draggable **610×582** “Shopping & Gathering List” shows **15 lines per page** and can be opened with its button or `/rrp list`.

It sorts by missing amount, uses **NEED** or **READY**, and lists unknown recipes with a conspicuous orange “?” marker. The footer makes the limitation clear: **“Owned = bags only. Unknown reagents cannot be included in totals.”**

The design intentionally doesn't resolve unknown reagents into zero, and it doesn't hide uncertainty behind a deceptively finished shopping summary. Both incomplete reagent slots and unknown Favorite price are visible separately.

## The 0.1.1-beta stability iteration

The initial design relied on character-level `known` state. When earlier saved profiles didn't yet include that table—or a newer UI was mixed with older core files—the UI could crash on first open. October 15 fixes migrated incomplete saved profiles and made `Refresh` tolerate missing tables, with a mock UI test that reproduces the failure path.

This is a particularly valuable development story: a UI may look finished on a fresh test profile but still fail on someone else's existing SavedVariables. The implementation was refined to respect the player's prior planning work rather than discard it.

## Outstanding design questions

The 920px fixed width may be too large at low resolutions; role density and eight compact profession buttons need direct game testing; the recipe list truncates long names; and the shopping window is separately draggable but doesn't save placement. These are **potential improvements requiring creator approval**, not current features.

No actual WoW client screenshots or usability results were verified as part of this audit. Do not present synthetic Lua window fixtures as visual evidence of in-game success.
