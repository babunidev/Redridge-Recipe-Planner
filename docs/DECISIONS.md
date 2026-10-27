# Redridge Recipe Planner — decisions, revisions, and unresolved options

**Original-idea credit:** Danoozy, recorded by preexisting AGENTS instructions (initial conversation not available here).  
**Project implementation and creative authority:** Babuni.dev.

## Actual chronological decisions visible through code

| Oct 15 commit evidence | Source-backed direction |
| --- | --- |
| `0d2ba066` | Start with a curated, dated **309-recipe catalog** for seven professions rather than pretend full live vendor API coverage |
| `ebafa703` and `1c5ad27d` | Build per-character plans, role filters, scanned schematics, trainer annotations and a two-column planning UI |
| `c07c8f8b` | Make **learned state character-specific**, not account-wide; scan profession **on demand** |
| `2b52d69d` | Add full paginated shopping list to complement five-material preview |
| `27ba3262` | Paladin-specific recipes are opt-in for other classes; add `/rrp list` |
| `e48135d6` | Cache client-exposed reagent schematics even when recipe isn't learned; still don't set known |
| `397ea6a3`, `672028f2`, `3941eddb` | Fix `known` nil crash, migrate incomplete character profiles and make UI read old/mixed data defensively |
| `a3f5e23d` / 0.1.1-beta | Publish source-level hotfix and stronger regression coverage; still needs live game verification |

These are observable implementation choices. The source history doesn't include all original alternatives or a first-hand transcript of Danoozy's suggestion or user approval of every line.

## Tradeoffs worth preserving

**Curated beta table vs live vendor data.** A static external guide provides broad coverage immediately, with the risk of stale prices/reputation. The scanner supplements it but doesn't verify every field. User-facing text must say which data is a guide snapshot.

**Class defaults vs personal choice.** A role-based profile saves browsing time; Show All Builds and explicit role toggles prevent the addon from enforcing a single play style. Paladin opt-in is deliberately stricter than generic Tank/Melee matching.

**Account-wide cache vs character-owned knowledge.** Sharing schematics between characters helps plan materials, but “learned” must not leak from one to another. The code's saved variable is overall account-wide with nested per-character state.

**One-time recipe cost vs repeated crafts.** Merchant's Favor is a recipe acquisition cost, not material usage. Planned quantity changes reagents, not the scroll's Favor price.

**Unknown vs made-up completeness.** No item ID, trainer availability, reagent or vendor price should be silently guessed. The UI displays unknown/partial schematics and unknown favor rather than using false zeros.

**Automatic scan vs explicit action.** The code initially subscribed to profession events, then removed automatic scans due to client event availability concerns. Users now choose Scan Profession explicitly. This makes observation opt-in and reduces accidental API errors.

**Saving plans vs safely migrating schemas.** Moving `known` into per-character profiles exposed a nil-reference bug in the UI and older user data. The 0.1.1-beta hotfix repaired both code and old profiles, with explicit mock regression fixtures instead of asking users to reset their plans.

**MIT license and third-party data.** Babuni.dev code is MIT; the WoW Professions guide remains an external source. Danoozy's original-idea credit is maintained without claiming a copyright assignment.

## Pending questions needing a creator decision

Should the full shopping list have CSV/export? Should the source catalog receive a dated incremental updater? Should trainer annotations be community-confirmed? Would a profession-name collision require stricter matching? Should UI position be saved? Should the planner gain multi-character shopping aggregation, or deliberately remain personal? Can a fixed 920px window be made more responsive?

None of those is implemented or approved by this audit. Record future proposals, discussion, explicit acceptance and actual tests separately.
