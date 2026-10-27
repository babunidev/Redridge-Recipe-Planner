# Redridge Recipe Planner — creative concept

**Implementation and creative direction:** Babuni.dev.  
**Original-idea credit:** Danoozy, as expressly recorded by the pre-audit AGENTS.md. The initial conversation establishing this attribution has not been retrieved; do not invent its contents.  
**Game and historical scope:** World of Warcraft: Forever 1.60.1 beta, Merchant's Favor faction vendors, October 15, 2026 catalog snapshot.

## From a long vendor list to a character-specific decision

The initial problem is that a new set of Merchant's Favor recipes at faction-associated camps is more complex than a simple shopping trip. A player faces different professions, reputation tiers, character levels, skill thresholds and costs. Many recipes might belong to an armor line or a character build they never intend to use. And a recipe that looks attractive can demand reagents that the character doesn't yet have.

Redridge Recipe Planner turns that problem into an in-game **plan**. It gives the player a catalog of **309 beta-listed recipes in seven professions**, asks which recipes are relevant, allows a planned number of crafts, and groups known materials into a shopping/gathering list. The project is deliberately an **exploratory planning aid**, not a promise that every recorded beta recipe is still sold or that a character has learned it.

Both factions are considered in the source description: the Alliance **Azeroth Commerce Authority** in Redridge Mountains and the Horde **Durotar Supply and Logistics** camp. The README says both sell the same recipes, a historical beta-catalog claim that should be checked against actual current vendor inventories.

## Put the build first, but leave the player in control

The filter language is based on **Tank, Melee, Healer, Caster, Hunter, Paladin and Utility** roles, with an independent **Show All Builds** override. The current character class establishes a thoughtful starting selection: Warriors get Tank/Melee; Paladins get Tank/Melee/Healer/Paladin; Hunters get Hunter/Melee; and so on. But these are merely defaults. The player may opt into other roles at any time.

An especially revealing iteration from October 15 was to require **explicit Paladin opt-in** for Paladin-tagged designs on other classes, even if a recipe also shares Tank or Melee tags. A Warrior's Tank preference is not permission to fill the list with Paladin equipment. The source also offers Show All Builds for those who intentionally want everything.

This is a useful design principle for a portfolio: **personal relevance with an escape hatch**. The tool interprets choices without pretending to decide what the user should craft.

## Favor costs are not the same as ingredient costs

Merchant's Favor is a **one-time recipe acquisition cost**; reagent quantities scale with how many times the player intends to craft the recipe. For instance, choosing one recipe for three crafts counts its listed favor **once** but multiplies the scanned reagent requirement threefold. This distinction is critical to a plan that can be trusted.

The same separation applies to knowledge. A recipe can be recorded in a vendor catalog without being learned by the current character. The game may expose its schematic even if it is unlearned. A different character may know an intermediate crafting recipe, but this character may not. The addon uses **per-character known flags** and only expands craftable intermediate ingredients where the current character has a complete captured schematic.

If the recipe schematic isn't available, the UI says **unknown** instead of assigning zero cost. If a vendor-scan discovery has no published Favor price, the cost is also **unknown**. The player may annotate trainer availability, but that mark remains **user-marked (unverified)**.

## A working notebook, not a static truth machine

The initial catalog was transcribed from [WoW Professions' Forever Merchant's Favor guide](https://www.wow-professions.com/forever/merchants-favor-durotar-supply-azeroth-commerce) as of October 15. Live scans can supplement it: a profession window may disclose learned recipes/reagents; an open merchant may link recipe scrolls or add newly observed entries. The source never invents unobserved item IDs, reagents or trainer duplicates just to fill the table.

This explicit distinction between *published vendor claims*, *live client observations* and *player notes* is part of the project's core integrity.

## Attribution and limits

The existing contributor instructions record **Danoozy as the original-idea contributor**; Babuni.dev is the developer, creative director and MIT copyright holder of this implementation. The available Git record does not show the original conversation or who authored each line. Later AI assistance on the October 27 documentation audit is technical/editorial, not a claim to have conceived the project.

This is an early **0.1.1-beta** implementation. Its Lua code and tests exist; **actual in-game behavior remains unverified in the repository's public status**. Any future interface changes or content import mechanisms require the creator's approval rather than being silently introduced by roadmap text.
