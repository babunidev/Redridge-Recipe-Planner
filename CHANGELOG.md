# Redridge Recipe Planner — actual Git development history

**Project implementation / creative direction:** Babuni.dev. **Original-idea attribution:** Danoozy as already documented in prior AGENTS.md.  
**Date method:** Git commit timestamps; not a claim of in-game testing or separately packaged releases.

## October 27, 2026 — portfolio documentation

- Expanded eleven Markdown project files: README, CONCEPT, ROADMAP, CHANGELOG, AGENTS and `docs/` architecture, design, content, decisions, narrative DEVLOG and AI collaboration.
- Retained historical original-idea credit for Danoozy separately from Babuni.dev's implementation, creative direction and MIT copyright.
- Clarified per-character learned status versus shared schematics, one-time Favor purchase costs versus craft quantities, unknown material/trainer status, Paladin opt-in and profile-migration bug.
- Verified Git tree, source, test scripts and lack of published GitHub Releases. **Documentation only**; no Lua, TOCs, workflow, tests or MIT license changed. No actual test run in this audit.

## October 15, 2026 — 0.1.0-beta construction and first iteration

| Source commit | Visible change |
| --- | --- |
| `0d2ba066` | Add 309 Merchant's Favor **beta-listed** recipes, seven professions, skill/level/Favor/reputation/role metadata; no fabricated reagents or scroll item IDs. |
| `ebafa703` | Implement account-wide `RRPDB`, character profiles, class role defaults, profession scan, vendor scan, user trainer notes and reagent math. |
| `1c5ad27d` | Build in-game **920×630** planner with filter tabs, recipe quantity controls, selection details, materials summary. |
| `baa41fce`, `7099ddeb` | Two interface-16001 TOC variants, three Lua source modules, installation notes for Forever beta. |
| `a17da71a`, `ead83730` | Catalog invariant fixture and Lua GitHub Actions workflow. |
| `c07c8f8b` | Move learned-recipe flags from global to **per-character** profiles; make scanning **on demand**. |
| `2b52d69d` | Add **full paginated shopping list**, quantities, missing reagents and learned flag displays. |
| `27ba3262` | Hide Paladin-tagged items on other classes unless opted in; add `/rrp list`. |
| `c20fcac5`, `4074201e` | Add and enable pure Lua 5.1 tests of profile defaults, math, filters, scans and calculations. |
| `e48135d6`, `4d97fc23` | Cache API-provided schematic for **unlearned** recipes; verify it does not falsely mark them learned. |

## October 15 — reported crash, migration and 0.1.1-beta hotfix

- `397ea6a3` corrected `UI.lua` to use `p.known` rather than removed `RRPDB.known`.
- `52842635`, `cf806959` added a mocked UI smoke test and wired it into CI.
- `672028f2` migrated incomplete/legacy character fields in `GetProfile` without discarding planned recipes.
- `3941eddb` safeguarded `UI.Refresh` for old/mixed `known`, `plan`, `roles`.
- `41ff22e7`, `89982d04` added explicit profile and UI regression cases.
- `a3f5e23d`, `539c7e67`, `c60a98fc` finalized **version 0.1.1-beta**, documenting the missing-learned-table crash and need to update the **whole addon folder**.

The tight sequence is strong source evidence for actual bug correction, but does **not** imply an independently observed successful WoW client run.

## October 26 — creator attribution and documentation seed

- `b1ece014`, `4f721b8a`: standardize TOC author to **Babuni.dev**.
- `336f593e`: README creator credit.
- `fc7469c5`: update MIT copyright attribution to Babuni.dev.
- `9128a210`: add short CONCEPT, ROADMAP, CHANGELOG, AGENTS, architecture, decisions and DEVLOG.
- `107ec7f3`: link the documentation from README.

**Distribution note:** Version 0.1.1-beta is present in Git; there are **no published GitHub Releases** in the inspected repository. Do not claim an attached ZIP or stable release.
