# Redridge Recipe Planner — creative and technical development journal

**Original-idea contributor (per preexisting project instructions):** Danoozy  
**Developer, implementation and creative director:** Babuni.dev  
**Retrospective written:** October 27, 2026. Based on actual October 15/26 Git commit history, the source/README, tests and source code. The conversation in which the original idea was first raised was **not present**; its contents, participants' phrasing and exact chronology beyond Git have not been invented.

## Starting with a problem larger than a vendor window

At the center of Redridge Recipe Planner is a question familiar to a player who has suddenly been offered a long list of profession recipes: **which of these do I actually want, and what will buying and crafting them require?**

World of Warcraft: Forever's **Merchant's Favor** vendors added a complicated set of possible purchases. The Alliance **Azeroth Commerce Authority** in Redridge Mountains and the Horde **Durotar Supply and Logistics** camp offered recipes differentiated by profession, skill requirement, level, reputation and purchase currency. Simply copying that table to an in-game window would reproduce the burden of scanning a long list without solving the player's planning problem.

The existing project rules specifically preserve **Danoozy's original-idea credit** and distinguish that contribution from **Babuni.dev's implementation and artistic direction**. That is the available attribution evidence. This journal does not know exactly what Danoozy said, which interface details they proposed or how the first discussion developed, so it won't invent a dialogue or assert they authored a specific algorithm.

The authored solution embodied in the current implementation is a personal **recipe planning workspace**. It begins with the 309 recipes of a dated beta guide but moves beyond reading: selecting what matters, choosing how many crafts to make, identifying what recipes are already learned on a particular character, and building a shopping list from materials genuinely reported by the game.

The source itself is careful to draw a boundary between **information and invention**. The initial vendor catalog contains names, professions, level and skill thresholds, Merchant's Favor prices, reputation and rough build tags. It deliberately does not prepopulate scroll item IDs, reagent schematics or supposedly confirmed trainer duplicates. Those fields are gathered from the game's available profession/vendor interfaces if and when a user explicitly scans.

## October 15, 2026 — translating beta data into the first catalog

The repo was created on October 15, and its first meaningful code commit **`0d2ba06682fc`** inserted `Catalog.lua` with **309 entries across seven professions**. It also records the external source: [WoW Professions' Forever Merchant's Favor guide](https://www.wow-professions.com/forever/merchants-favor-durotar-supply-azeroth-commerce), consulted on the same date.

These figures were not merely decorative. Their structure shaped what the interface could ask. Blacksmithing, Leatherworking and Tailoring had many families of armor recipes whose usefulness depended on intended character role. Alchemy had competing potions and elixirs. Enchanting and Engineering provided other equipment or consumable directions. The source classified recipes with **Tank, Melee, Healer, Caster, Hunter, Paladin, Utility** tags.

A prewritten catalog makes the tool usable before the player visits a vendor, but it also introduces staleness as soon as the beta changes. The developer chose to retain provenance and to call the information *beta* rather than imply that it was the server's definitive current state. The corresponding catalog fixture enforces the exact total and profession counts; it cannot verify the vendor is actually selling those entries today.

## The same day — a planner, not a catalogue viewer

Commit **`ebafa7033770`** added the stateful Core module, followed by **`1c5ad27d8272`** for the window. The user could now select a profession, toggle relevant build roles, assign craft counts to recipes, mark personal trainer observations and calculate what the chosen plan might cost in Favor and materials.

The main interface is an intentionally information-dense but game-native **920×630** panel. Profession/role buttons occupy the top, paginated recipes fill the left, selected recipe details appear on the right, and a condensed shopping summary sits below. It is not a settings window that happens to list items; it is a desk where the player can assemble a decision.

A small but important distinction appeared in the arithmetic. Merchant's Favor pays for the recipe once. A player planning three elixirs doesn't need to buy three copies of the scroll, but does need three times the crafting reagents. The Core calculates **one Favor cost per selected recipe** and multiplies actual material schematic quantities by planned crafts. New scanned vendor discoveries with unknown Favor are marked incomplete. Recipes whose material data isn't available appear in an **unknown list**, not as free crafts.

That rule is both an algorithmic choice and a statement of honesty: a blank field is not proof of zero cost.

## Character relevance becomes personal, and saved knowledge gets tricky

The early Core stored a shared set of `known` recipes in the overall SavedVariables object. On **October 15**, commit **`c07c8f8b142c`** changed that design. Whether a recipe has been learned belongs to the **current character**, while the ingredients of a recipe can be useful knowledge across multiple characters.

So the source moved learned flags to the per-character profile `p.known`, while retaining schematic data in the account-level `RRPDB.materials`. It also stopped trying to scan professions automatically on profession-update events, instead offering an explicit button/`/rrp scan` for the client APIs that were actually available.

This change is easy to miss in a screenshot but essential in play. A Warrior may plan an item without knowing the crafting recipe; an alt with another profession might know it; the tool should not mark a shared recipe as learned on every character simply because the account has seen it.

The program creates each profile under a key derived from the client-provided `GetRealmName()` and `UnitName("player")`. That code detail is part of storage, not proof of cross-realm player naming mechanics. The actual player identity conventions of the intended Forever client still deserve direct testing.

The next refinement, **`2b52d69d2b72`**, added the **complete paginated Shopping & Gathering List**. The main pane could only show the five largest material entries; that was a teaser, not enough to complete a long gathering session. A separate window now lists all observed required materials and incomplete recipes, page by page, with how much the player owns **in bags** and how many units remain missing. The interface openly declines to count bank, alt or guild stock.

Another immediate improvement, **`27ba3262a25d`**, added a special filter rule for Paladin-tagged items. Initially a generic Tank or Melee tag might let Paladin armor fill a Warrior's planner. The revised rule requires a non-Paladin to select **Paladin** explicitly—or turn on **Show All Builds**—before those recipes appear. This is a practical example of the creator's role-aware design being refined through implementation.

## More schematics without claiming more learning

The project then confronted a useful difference in the Forever profession API. Sometimes the client can return reagent schematic information for a recipe **the player has not learned**. The first scan logic only saved schematics when the recipe was learned.

Commit **`e48135d670b1`** expanded that: capture a real schematic whenever the client exposes it, but only mark the current character's `known` flag when `GetRecipeInfo().learned` confirms it. A test was added immediately afterward to preserve the separation.

This creates a nuanced planner. It may know what a recipe takes to craft without falsely indicating that the character can craft it. The intermediate-material expansion still requires a **complete schematic and a learned intermediate recipe on the current character**, rather than using every theoretical transformation in the catalog.

The UI also supports manually marking a recipe as potentially available from a trainer. The marker is intentionally **unverified**. In the catalog, the alchemy example of *Elixir of Lesser Intellect* versus *Elixir of Wisdom* is described as a **similar effect** but not the same recipe. The project resists an easy false equivalence because acquiring a vendor scroll has real resource consequences.

## A first-day failure: the learner state was moved, but the UI wasn't ready

The most revealing development episode is how quickly the new per-character model exposed a practical failure.

The source was changed to store known recipes inside `p.known`, but `UI.lua` still had a read against the old global `RRPDB.known`. On some existing or mixed installations, opening the window triggered the now-documented error around **`UI.lua:273`**. The following rapid October 15 commits show a careful repair sequence.

First, **`397ea6a355de`** changed the UI to read from the character profile. Then **`528426358bbd`** created a mocked UI smoke test that actually opens the full planner, and **`cf8069593229`** wired that test into GitHub Actions. But only updating one source line wasn't enough for people with **old SavedVariables**: some saved character profiles had a plan, perhaps role choices and a profession, but no `known` table at all.

So **`672028f24da0`** made `GetProfile` fill in missing fields **in place**, keeping existing selections. **`3941eddb2678`** strengthened `UI.Refresh` by using defensive local `knownRecipes`, `plan` and `roles` tables if it received an incomplete profile. Finally, **`41ff22e710f2`** and **`89982d04496a`** added dedicated regression cases for the old-profile path and a mixed-version UI refresh.

Version **0.1.1-beta** collected those fixes and told users to **replace the entire addon folder**, not just UI.lua. That instruction matters because a partially updated Lua set can reproduce a bug already fixed in Git.

The record makes the relationship between code and user experience unusually concrete. A saved plan is creative work by the player: they have spent time deciding what to buy and how much to craft. A robust upgrade should not casually delete that work to sidestep a missing table. The fix therefore migrated the data rather than recommending a reset.

The Git history shows the commits and the README reports the bug. It does not give us all original bug-report messages, user names, screenshots or proof of successful in-game repair on the actual beta client. Those should not be invented.

## Testing infrastructure and the game-client gap

The project includes three focused **Lua 5.1 test files**:

- `tests/catalog.lua` verifies catalog shape, totals, uniqueness, and a documented elixir alternative.
- `tests/core.lua` exercises role defaults/Paladin opt-in, one-time Favor math, shared reagent multiplication, intermediate crafting, profiles, unlearned schematic capture, manual trainer notes and vendor discoveries.
- `tests/ui.lua` uses mocked WoW frames and simulates opening, refreshing, reading missing `known` tables and using the full shopping panel.

An included GitHub Actions workflow installs Lua 5.1, checks syntax and invokes those three scripts. This provides reviewable intent and repeatable automated checks, **not proof of gameplay compatibility**. The current October 27 documentation audit inspected the files and YAML but did **not** execute them or read an actual CI run status.

The README remains clear that **in-game testing is still required**. Profession API shapes, actual beta vendor offerings, Merchant's Favor information, translated recipe names and visual layout all need a real client before anyone can honestly say the planner is verified end to end.

## October 26–27 — preserving both collaboration and limitations

October 26 commits standardized the TOC and README author to **Babuni.dev**, updated the MIT copyright notice and created a small first set of documentation files. This October 27 audit expands those into a concept, architecture, visual-language record, content inventory, decisions register, roadmap, attribution note and this narrative.

It preserves the earlier project instruction about **Danoozy's original-idea credit** while leaving implementation/artistic direction and MIT copyright with **Babuni.dev**. The original exchange that established the idea was not supplied to the audit, and this journal doesn't claim knowledge of Danoozy's exact words or the AI/human implementation share of each original Git commit.

**Verified from source and history:** the 309-entry catalog, three modules, two manifests, committed test scripts and CI workflow, first-day feature/bugfix chronology, existing MIT license and absence of published release artifacts. **Not verified here:** a game client, live merchant/profession reads, actual test execution or CI status, user SavedVariables, accurate vendor pricing after the October 15 snapshot, or screenshots.

## The portfolio's publishable story

What began as the need to plan a long vendor shopping list became a richer lesson in trustworthy tools: keep class relevance customizable, separate the cost of *acquiring a recipe* from *making its product*, distinguish a recipe the client can describe from one a character knows, mark unknown materials as unknown, and preserve a saved plan through a migration rather than deleting it.

The October 15 bug is worth displaying as part of the work, not hiding. It shows how **a stronger data model can expose a fragile assumption elsewhere in the UI**, and how tests and migration code can protect the player's previous effort.

The next journal chapter belongs to actual playtesting: visit the camps, confirm prices, run the profession scanner, compare bag counts and make the UI useful under real beta constraints. That evidence—not an invented claim of completion—will make the finished Babuni.dev portfolio entry compelling.
