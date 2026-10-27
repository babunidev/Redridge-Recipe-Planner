# Redridge Recipe Planner — collaboration and attribution

**Original idea contributor:** Danoozy, per the repository's preexisting AGENTS.md instruction.  
**Project implementation, creative director and MIT copyright holder:** Babuni.dev.

## Why this project needs more careful credit

The prior contributor guidance says: **“Where relevant, retain Danoozy's original-idea credit separately from Babuni.dev's implementation and artistic direction.”** This audit preserves that explicit attribution. The source does **not** contain the original idea discussion, exact roles in drafting a particular formula, or other first-hand contribution evidence. It would therefore be wrong to invent detailed Danoozy proposals—or to erase the collaborator and imply Babuni.dev/AI conceived everything alone.

Babuni.dev remains the project's developer, creative lead and author credited in its TOCs and MIT license. Git history is evidence that particular source changes were committed on dates, not proof of the identity of every author of each change or the precise split of earlier AI assistance.

## AI contributions during the October 27, 2026 audit

The assistant:
- Identified **Redridge-Recipe-Planner** as repository **#25** in the original Babuni.dev sequential audit after #24 ResistForever.
- Inspected three Lua modules, both matching interface-16001 TOCs, preexisting README/AGENTS, MIT LICENSE, the 309-entry catalog and its external [WoW Professions guide](https://www.wow-professions.com/forever/merchants-favor-durotar-supply-azeroth-commerce) reference.
- Reviewed October 15 feature and hotfix commits, including the transition from account-wide known flags to per-character profiles, class/Paladin filtering, unlearned schematic capture, full shopping list and migration of missing `known` state.
- Inspected `tests/catalog.lua`, `tests/core.lua`, `tests/ui.lua`, and `.github/workflows/lua.yml`; did not execute the tests or check live CI status.
- Verified **no GitHub Releases** exist in repository metadata; avoided inventing a download asset or confirmed in-game test.
- Expanded/created eleven Markdown documentation files, with a substantive journal, technical schema/algorithm explanation, visual design, content provenance, creator/AI credit and test roadmap.
- Committed documentation-only changes, leaving source, manifests, workflow, tests, license and other files untouched.

This contribution is **technical and historical analysis, editorial structure and repository documentation**. It is **not** original ideation, proof of actual WoW beta compatibility, current vendor confirmation, a coding fix for 0.1.1-beta, or an independently observed CI pass.

## How future collaboration should be recorded

For each substantial task, distinguish:
1. **Idea and user direction:** Original contributions (including Danoozy when applicable), Babuni.dev's product decisions, desired player benefit and constraints.
2. **AI interpretation:** Any suggested structure, code, research or generated documentation, without taking credit for original human thinking.
3. **Creative iteration:** Alternatives, disagreements, corrections, debugging and rejection.
4. **Creator approval:** What Babuni.dev actually accepted, rejected or postponed; ideas in ROADMAP are not tacit approvals.
5. **Implementation:** Exact changed files, commits, version metadata and dependencies.
6. **Verification:** Tests truly executed, CI checks truly observed, exact game client/version, screenshot evidence and unresolved items.

Portfolio-ready narrative should summarize the creative process faithfully but not publish raw private chats, player-account data, SavedVariables containing character names, credentials or unrelated personal information. Third-party guide content and game assets retain their external provenance.

No executable Lua, TOC, tests, workflow or license file was edited by this documentation pass.
