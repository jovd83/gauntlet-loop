# Changelog

All notable changes to this repository are documented here.

The format follows Keep a Changelog.

This repository is a fork of [robonuggets/gauntlet-loop](https://github.com/robonuggets/gauntlet-loop). Version `1.0.0` records the upstream state at the point of the fork; later entries are changes made in this fork.

## [1.1.0] - 2026-08-18

### Added

- Deep mode: a second output for multi-hour autonomous runs, in `references/deep-run.md`, with a structured critic verdict, a live progress dashboard that separates built from verified, and integration-critic, final-challenger and adversarial-critic gates before completion may be claimed.
- Mode selection in `SKILL.md`, with quick mode remaining the default.
- Reference equivalence as an explicit rule: both sides in the same viewport, data and flow state before any comparison.
- Critic confidence in the generated prompt, so a marginal win no longer closes a piece.
- A current best verified version that a newer round does not automatically replace.
- Strategy reset after repeated losses on the same piece: a fresh builder with no exposure to the failed attempts and a mandate to solve it differently.
- Scope control, so critique makes the work better rather than bigger.
- `BLOCKED` as a state distinct from passed, for unreachable references or unrunnable artifacts.
- Fill rules for maturity target and hard constraints.
- Portability fallback for agents with no subagents at all.
- `CHANGELOG.md`.

### Changed

- Quick-mode prompt length target raised from roughly 150 words to roughly 250 to carry the new mechanics.
- Both worked examples in `SKILL.md` rewritten to match the updated template.
- `README.md` restructured to the fork author's house layout, with fork provenance, install paths for the nested skill root, and an expanded failure-mode list.

## [1.0.0] - 2026-08-06

Upstream state at the point of the fork, by Jay E at [RoboNuggets](https://robonuggets.com), packaging a technique created by [Matt Shumer](https://github.com/mshumer).

### Added

- `SKILL.md` with the quality-bar rules, the short prompt template, two worked examples, and the failure-mode list.
- `README.md`, banner asset, and CC BY 4.0 license.
