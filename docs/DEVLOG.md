# Redridge-Recipe-Planner — development journal

**Author:** Babuni.dev

## Retrospective source reconstruction — 2026-10-26

An early World of Warcraft: Forever Merchant's Favor recipe planner for faction vendors, per-character craft plans and a beta catalog. The data is tied to a beta listing; README identifies a 309-recipe catalog across seven professions and reports that actual in-game testing remains required. Version 0.1.1-beta repairs a missing saved known-recipe table. The implementation separates concerns as follows: RedridgeRecipePlanner/Catalog.lua, Core.lua and UI.lua hold recipes, plans and a window; two TOCs target interface 16001; tests/catalog.lua, tests/core.lua and tests/ui.lua provide regression coverage.

The documentation pass is an AI-assisted reconstruction based on committed material, not a claim about who wrote each line or when a proposal was accepted. Babuni.dev's project authorship is preserved, as are explicit human collaborator credits. No in-game tests were executed here. Run Lua tests, check actual vendors and inventory, migration of saved plans and in-game user interface. Confirm beta catalog does not claim current live prices.
