---
name: gauntlet-loop
description: Turns any goal into a paste-ready "gauntlet loop" prompt - a prompt that makes an agent set a concrete quality bar, split the work into small judgeable pieces, run a builder and a separate harsh critic on each, compare blind against the bar, reset its strategy when it stalls, and loop until it wins. Two modes - a short prompt for most goals, a full operator prompt for long autonomous runs. Works for builds, writing, code, research, or design. Triggers on "/gauntlet-loop", "gauntlet loop", "gauntlet this", "make a gauntlet prompt", "loop until it beats X", "run this overnight until it is good enough".
---

# Gauntlet Loop

The user gives a goal. You give back ONE prompt they can paste into a fresh agent session.

You are not doing the work. You are writing the prompt that makes another agent grind on the work until it beats a real reference.

## Flow

1. **Read the goal.** One line restatement in your head, not on screen.
2. **Pick the mode.** Quick or deep, see below. Do not ask unless it is genuinely a coin flip.
3. **Set the bar.** If the user supplied a reference, use it. If not, offer **2 or 3 candidate bars**, one line each, and stop. Wait for their pick. Do not write the prompt yet.
4. **Write the prompt.** One block, paste-ready, no preamble, no narration after it.
5. **Offer to run it.** One flat line under the prompt: "I can run this here." Not a question.

If they say run it, you become the lead agent and follow the prompt you just wrote.

## Two modes

**Quick** is the default and covers most goals. One page, one essay, one tool, one deck. The run is minutes to an hour and the user is around to watch it. Output is a short prompt, around 250 words, from the template on this page.

**Deep** is for runs that go for hours with nobody watching. Reach for it when the work has many interdependent pieces, when the consequences are real (production, security, money, compliance), or when the user says something like "run it overnight", "keep going until it is actually done", "full gauntlet". Output is a longer operator prompt with an evidence trail, a progress dashboard and a real acceptance gate. When deep applies, read `references/deep-run.md` and build from the template there.

Keep these separate rather than splitting the difference. Every instruction you add is one fewer decision the agent makes with its own judgment, and its judgment mid-run beats a spec written before the work started. A short prompt wins on a one-hour build. It loses on a twelve-hour one, where the agent needs a memory of what it already verified and a rule for when to stop patching a losing approach.

## The bar is the whole trick

Everything else in a gauntlet loop is scaffolding. The loop only produces quality if the thing it compares against is real.

A bar has to pass three tests:

- **Named.** A specific thing, not a category. "Stripe's pricing page" works. "Award-winning SaaS sites" does not.
- **Fetchable.** The critic can actually get it - screenshot the live page, read the published piece, run the binary, open the repo, watch the footage. If the agent cannot obtain it, it will hallucinate the comparison.
- **Comparable.** Both can sit side by side and a judge can pick one. If you cannot imagine the A/B, it is not a bar.

Bars by goal type:

| Goal | Bar that works |
|---|---|
| Website, app, UI | The live site of a specific best-in-class product, screenshotted at the same viewport |
| Game, 3D, visual | Real footage or screenshots from a named shipped title |
| Writing | A specific published piece by a named author or publication, same length and format |
| Code, tooling | A named repo's implementation, plus its benchmark or test suite as the measurable half |
| Research, analysis | A named analyst report or a paper's methods section, judged on rigour and coverage |
| Deck, doc, deliverable | A real artifact from a firm known for it, same page count |

When you propose bars, prefer the hardest one the agent can genuinely reach. A bar that is too easy makes the loop exit on round one.

If the goal has a measurable half (load time, token cost, benchmark score, word count, pass rate), name it alongside the reference. Taste plus a number beats taste alone.

**Comparisons have to be equivalent.** Same viewport, same data, same point in the flow, same length. Otherwise the verdict is about which capture happened to look better rather than which work is better, and the agent will happily bank that as a win. Say so in the prompt whenever the bar is visual or stateful.

## Quick prompt template

Adapt the wording every time. Fill the brackets, keep it tight, keep the last line.

```
Build [GOAL].

The bar is [BAR]. Get the real thing first and compare against it directly, not against a description of it. Put both in the same state before judging - same viewport, same data, same point in the flow - so the verdict is about the work and not about the capture.

Break this into the smallest pieces that can be improved and judged on their own. For each piece, fan out a builder and a separate critic with fresh context. The critic inspects the actual output - runs it, renders it, reads it - puts it next to the bar blind with the labels stripped, picks the better one, says how sure it is, and names the single biggest remaining gap. That gap goes back to the builder.

The critic should be a harsh critic. Praise is not useful. It can demand the piece be better, not bigger. If ours does not win, or only barely wins, it keeps going.

Hold on to the best version that has actually won something. A newer round is not automatically better - if it makes things worse, throw it out and go back.

If a piece loses three times running, stop patching it. Give it to a fresh builder that has not seen the previous attempts, ask for a genuinely different approach, and compare that against the current best blind.

If the reference cannot be reached or the work cannot be run, mark that piece blocked and tell me. Never guess the comparison.

/loop on each piece until the critic picks ours blind. Do not stop before that.

Keep a live progress page updating as the work evolves so I can watch it - what is built, what is actually verified, and the biggest gap left.

Fan out subagents and ultracode.
```

Rules for what you fill in:

- Bake the bar in as a concrete, fetchable thing. URL, product name, repo, title.
- Name the maturity if the goal does not make it obvious. A prototype held to production standards burns rounds on the wrong things, and a production target passed on prototype evidence ships broken. One phrase does it: "this is a throwaway demo" or "this goes live to paying customers".
- Carry over any hard constraint the user gave - stack, deadline, compatibility, must-not-break - as a line saying it survives every round. Critics negotiate away constraints they never heard about.
- Add a budget or cost ceiling line **only if the user named one**. No default cap.
- Add tool names only if the goal needs them (image or video generation, a browser, a deploy target).
- Everything else stays out. No architecture, no file layout, no decomposition, no round count, no stack choice unless the user demanded it. The agent decides those, and it decides better than a spec written before the work started.

## Length and voice

Quick mode lands around 200 to 260 words. If the prompt needs a heading to stay readable, it belongs in deep mode instead.

Plain sentences. No bullet lists inside the quick prompt. It should read like someone telling an agent what perfect looks like and refusing to accept less.

## Portability

`/loop` and `ultracode` are Claude Code features. `/loop` reruns the prompt on an interval or lets the model pace itself. `ultracode` opts the turn into multi-agent orchestration.

For any other agent, swap the last two lines for: "Keep looping until the critic picks ours. Run the builders and critics as parallel subagents." The structure carries over unchanged.

If the target agent has no subagents at all, add one line: "Run builder and critic as separate passes, and do not let the builder grade its own work." Fresh context is the mechanism; separate passes are the fallback.

## Two filled examples

**Visual goal.** User: "landing page for my running brand, athletic, green and dark, has to feel alive."

Bars offered: A) Nike's current running campaign page B) On Running's homepage C) Gymshark's product landing page. User picks A.

```
Build a landing page for a running brand. Athletic, peak performance, green and dark, energetic, aimed at a young healthy audience. It needs to be interactive and visually unmistakable.

The bar is Nike's current running campaign page. Screenshot it at desktop and mobile and compare against those directly, not against a description of them. Shoot ours at the same two viewports at the same scroll position so the comparison is about the design and not the crop.

Break this into the smallest pieces that can be improved and judged on their own - hero, motion, type, colour, imagery, interaction, mobile. For each piece, fan out a builder and a separate critic with fresh context. The critic opens the real page in a browser, puts our screenshot next to Nike's blind with the labels stripped, picks the better one, says how sure it is, and names the single biggest remaining gap. That gap goes back to the builder.

The critic should be a harsh critic. Praise is not useful. It can demand the piece be better, not bigger - no new sections nobody asked for. If ours does not win, or only barely wins, it keeps going.

Hold on to the best version that has actually won a comparison. A newer round is not automatically better - if the page gets worse overall, throw the round out.

If a piece loses three times running, stop patching it. Give it to a fresh builder that has not seen the previous attempts, ask for a genuinely different visual approach, and compare that against the current best blind.

/loop on each piece until the critic picks ours blind. Do not stop before that.

Keep a live progress page updating as the work evolves so I can watch it - what is built, what is actually verified, and the biggest gap left.

Fan out subagents and ultracode.
```

**Non-visual goal.** User: "a 2000-word explainer on vector databases for non-engineers."

Bars offered: A) a specific Stripe engineering blog explainer B) a named Julia Evans post C) the Wikipedia article plus a comprehension test. User picks B.

```
Write a 2000-word explainer on vector databases for readers who are smart but not engineers.

The bar is Julia Evans' writing on hard technical topics. Pull three of her actual posts and compare against them directly, not against a description of her style. Compare like for like - a section of ours against a section of hers of similar length doing a similar job.

Break this into the smallest pieces that can be judged on their own - the opening, each explanation, the diagrams, the analogies, the ending. For each piece, fan out a writer and a separate critic with fresh context. The critic reads ours and hers blind with the bylines stripped, picks the one a non-engineer would understand faster, says how sure it is, and names the single biggest remaining gap. That gap goes back to the writer.

The critic should be a harsh critic. Praise is not useful. It can demand the piece be clearer, not longer - the word count holds. If ours does not win, or only barely wins, it keeps going.

Hold on to the best draft that has actually won a comparison. A newer round is not automatically better - if a rewrite loses ground, throw it out.

If a section loses three times running, stop editing it. Hand it to a fresh writer who has not seen the previous drafts, ask for a genuinely different way in - different analogy, different order, different opening move - and compare that against the current best blind.

/loop on each piece until the critic picks ours blind. Do not stop before that.

Keep a live progress page updating as the work evolves so I can watch it - what is drafted, what has actually won, and the weakest section left.

Fan out subagents and ultracode.
```

## What breaks a gauntlet loop

- **A vague bar.** The critic invents a comparison and approves everything. Most common failure by far.
- **The builder judging its own work.** The critic must be a separate agent with fresh context. It should not know how hard the builder tried.
- **A soft critic.** Say "harsh" in the prompt and give it a binary job: which one is better, A or B. Scores out of 10 drift upward every round.
- **A marginal win banked as a win.** If the critic can barely tell them apart, the piece is not done - that is a tie wearing a pass. Asking how sure it is costs one clause and closes the hole.
- **Polishing a local optimum.** The builder keeps patching the same losing approach and the loop runs forever going nowhere. The fix is not more rounds, it is a fresh builder with no memory of the failed attempts and permission to do it differently.
- **A quietly ratcheting bar.** Ours keeps losing, so the reference gets swapped for an easier one or the goal gets restated to match what was built. The gap is the point. Close it, do not move it.
- **Scope drift by critique.** Critics can always imagine more. Over a long run that turns a landing page into a platform. Better, not bigger.
- **Named exit after N rounds.** The exit is winning the comparison, or the user stopping the run. Never a round count.
- **Over-specifying.** Every extra instruction is one fewer decision the agent makes with its own judgment. Minimal wins - which is exactly why deep mode is a separate mode and not the default.
