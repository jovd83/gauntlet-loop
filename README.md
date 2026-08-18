<p align="center">
  <img src="assets/banner.png" alt="gauntlet loop" width="100%">
</p>

# Gauntlet Loop

[![version](https://img.shields.io/badge/version-1.1.0-blue)](CHANGELOG.md)
[![license](https://img.shields.io/badge/license-CC%20BY%204.0-green)](LICENSE)

> **This is a fork of [robonuggets/gauntlet-loop](https://github.com/robonuggets/gauntlet-loop)** by Jay E at [RoboNuggets](https://robonuggets.com), which packages a technique created by [Matt Shumer](https://github.com/mshumer). See [Attribution](#attribution) for what came from where and [Changes In This Fork](#changes-in-this-fork) for what was added here.

Turn any goal into a prompt that makes an agent grind on the work until it beats a real reference.

This repository packages an Agent Skill that writes gauntlet loop prompts. You give it a goal, it fixes a concrete quality bar, and it hands back a paste-ready prompt that makes the receiving agent split the work into small judgeable pieces, run a builder and a separate harsh critic on each one, compare blind against the bar, reset its approach when it stalls, and keep looping until it wins. The skill does not do the work itself; it writes the prompt that does.

Most agent output stops at "good enough" because nothing holds it to a standard. This gives it a standard it cannot argue with.

## What This Skill Does

- Converts a one-line goal into a single paste-ready gauntlet loop prompt.
- Proposes 2 or 3 candidate quality bars and refuses to proceed on a vague one.
- Bakes blind builder/critic comparison, strategy reset, and an evidence-based exit into the generated prompt.
- Chooses between a short prompt for ordinary runs and a full operator prompt for long unattended ones.
- Works across domains: builds, writing, code, research, design, decks.

## What This Skill Does Not Do

- build the artifact itself unless you explicitly ask it to run the prompt it just wrote
- score work against a self-authored rubric, which drifts upward every round
- exit after a fixed number of rounds
- prescribe architecture, file layout, stack, or decomposition, all of which the receiving agent decides better mid-run
- persist cross-project memory or conventions

## Two Modes

**Quick** is the default and covers most goals: one page, one essay, one tool, one deck. The run is minutes to an hour with you watching. Output is a short prompt of roughly 250 words, and the agent decides everything the prompt does not say.

**Deep** is for runs that go for hours with nobody watching, or where the consequences are real: production, security, money, compliance. Output is a longer operator prompt with an evidence trail, a live progress dashboard, and three closing gates. The skill picks the mode from the goal; ask for the other one by name if you want it.

Deep mode carries three things a short prompt cannot hold over a long run:

| Mechanic | Failure it prevents |
|---|---|
| **Current best verified version** | Round 7 is worse than round 4 and nobody notices |
| **Strategy reset** | The builder patches the same losing approach forever |
| **`BLOCKED` as a real state** | The agent invents a comparison to escape an unreachable reference |

And three gates before it may claim completion: an integration critic on the assembled result, a fresh builder that tries to beat the whole thing from scratch, and an adversarial critic whose only job is to prove it is not done.

## The Quality Bar

A rubric asks the agent to grade itself against words it wrote. A bar makes it compare against something that already exists and is undeniably good. The skill will not write a prompt until the bar passes three tests:

- **Named.** A specific thing, not a category. "Stripe's pricing page" works. "Award-winning SaaS sites" does not.
- **Fetchable.** The critic can screenshot it, read it, run it, or open it. If it cannot obtain the reference, it hallucinates the comparison and approves everything.
- **Comparable.** Both can sit side by side and a judge can pick one.

Comparisons also have to be equivalent: same viewport, same data, same point in the flow, same length. Otherwise the verdict is about which capture happened to look better, and the agent banks that as a win.

## Installation

The skill root is `.claude/skills/gauntlet-loop/`, not the repository root, so copy that folder rather than the whole repo.

Project-level install:

```bash
git clone https://github.com/jovd83/gauntlet-loop
cp -r gauntlet-loop/.claude/skills/gauntlet-loop your-project/.claude/skills/
```

User-level install:

```bash
cp -r gauntlet-loop/.claude/skills/gauntlet-loop ~/.agents/skills/
```

Then invoke it:

```
/gauntlet-loop build me a pricing page for my SaaS
```

## Repository Layout

```text
gauntlet-loop/
|- .claude/skills/gauntlet-loop/
|  |- SKILL.md              # the skill, and the quick-mode prompt template
|  `- references/
|     `- deep-run.md        # the long-run operator prompt
|- assets/
|  `- banner.png
|- CHANGELOG.md
|- LICENSE                  # CC BY 4.0
`- README.md
```

## Usage

1. **Give a goal.** Anything. A site, an essay, a CLI tool, a research brief.
2. **Pick a bar.** The skill offers 2 or 3, each a specific real thing your agent can actually fetch. Not "award-winning design", but a named page, a named post, a named repo.
3. **Take the prompt.** One block, paste-ready, no preamble.
4. **Paste it into a fresh session.** That agent splits the work, runs builder and critic pairs, and loops.

The critic is the part that matters. It is a separate agent with fresh context, it opens the actual output, it puts your work next to the bar with the labels stripped, and it picks one. Not a score out of 10. A pick, plus how sure it is, because a comparison it can barely call is a tie wearing a pass.

The loop exits when your work wins the blind comparison, or when you stop the run. Never after a fixed number of rounds.

## Examples

```
/gauntlet-loop a landing page for my running brand, dark and green, has to feel alive
```
Bar becomes a specific brand's live campaign page, screenshotted at desktop and mobile.

```
/gauntlet-loop a 2000 word explainer on vector databases for non-engineers
```
Bar becomes a named writer's actual published posts, judged on which one a non-engineer understands faster.

```
/gauntlet-loop a CLI that formats JSON logs
```
Bar becomes a named tool's implementation plus its benchmark, so taste and a number both have to win.

```
/gauntlet-loop rebuild our checkout flow, run it overnight, it goes live to paying customers
```
Deep mode. Evidence trail, dashboard, and all three closing gates.

## Works With Any Agent

`/loop` and `ultracode` are Claude Code features. `/loop` reruns a prompt until you stop it, and `ultracode` opts a turn into multi-agent orchestration.

For any other agent, the skill swaps those two lines for plain instructions: keep looping until the critic picks ours, and run the builders and critics as parallel subagents. Where there are no subagents at all, it falls back to separate passes with the builder barred from grading its own work. The structure is identical.

## What Breaks A Gauntlet Loop

- **A vague bar.** The critic invents a comparison and approves everything. By far the most common failure.
- **The builder judging its own work.** The critic needs fresh context and no knowledge of how hard the builder tried.
- **A soft critic.** Give it a binary job, not a score.
- **A barely-won comparison banked as a win.** That is a tie wearing a pass, which is why the critic has to say how sure it is.
- **Polishing a local optimum.** Nine rounds patching the same losing approach is not persistence. It needs a builder with no memory of the failed attempts.
- **A quietly ratcheting bar.** Ours keeps losing, so the reference gets swapped for an easier one. The gap is the point.
- **Scope drift by critique.** Critics can always imagine more. Better, not bigger.
- **A fixed round count.** The exit is winning, or you calling it.

## Changes In This Fork

Upstream ships a single short prompt. This fork keeps that as the default and adds the mechanics that a long unattended run needs, drawn from a parametrised gauntlet meta-prompt maintained separately by the fork author.

Added to the default prompt, at about one line each:

- reference equivalence before any comparison
- critic confidence, so a marginal win does not close a piece
- a current best verified version that a newer round does not automatically replace
- strategy reset after repeated losses on the same piece
- scope control, so critique makes the work better rather than bigger
- `BLOCKED` as a distinct state from passed

Added as a second mode, in `references/deep-run.md`:

- a full operator prompt for multi-hour autonomous runs
- a structured critic verdict with explicit evidence
- a live progress dashboard that separates built from verified
- integration critic, final challenger, and adversarial critic gates before completion may be claimed
- an explicit statement of what is deliberately left out, and why

See [CHANGELOG.md](CHANGELOG.md) for the release history.

## Attribution

The gauntlet loop technique is **[Matt Shumer's](https://github.com/mshumer)**. He built [Claude of Duty](https://github.com/mshumer/Claude-of-Duty), wrote the [original prompt](https://github.com/mshumer/Claude-of-Duty/blob/main/prompt.md), and named the loop. Every idea underneath this skill - the harsh critic, the blind comparison, the refusal to stop until the work wins - comes from that prompt.

The skill that packages the technique is **[Jay E's](https://robonuggets.com)**, published at [robonuggets/gauntlet-loop](https://github.com/robonuggets/gauntlet-loop). This repository is a fork of that work, not a replacement for it.

Related reading: [Anthropic on building effective agents](https://www.anthropic.com/engineering/building-effective-agents), which covers the evaluator pattern the loop is built on.

## Contributing

Keep the skill lean and auditable:

- prefer explaining why a rule exists over adding another rule
- anything added to the quick prompt has to earn its line; the default output stays around 250 words
- deep-mode additions belong in `references/deep-run.md`, never in `SKILL.md`
- before adding anything, apply the test at the end of `deep-run.md`: if the agent quietly skipped this, would the final work be worse?

## License

Licensed under the [Creative Commons Attribution 4.0 International License (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/). The full licence text is in [LICENSE](LICENSE), including its disclaimer of warranties and limitation of liability.

This work is a modified version of [robonuggets/gauntlet-loop](https://github.com/robonuggets/gauntlet-loop) by Jay E at [RoboNuggets](https://robonuggets.com), used under CC BY 4.0. Modifications by [jovd83](https://github.com/jovd83) are described in [Changes In This Fork](#changes-in-this-fork) and [CHANGELOG.md](CHANGELOG.md), and are released under the same licence.

The underlying gauntlet loop technique is by [Matt Shumer](https://github.com/mshumer).
