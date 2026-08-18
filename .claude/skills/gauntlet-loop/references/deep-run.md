# Deep mode

For gauntlet runs that go for hours with nobody watching.

Quick mode assumes the user is nearby and the run is short, so it trusts the agent's judgment for almost everything. That trust is what makes it good. It stops being enough when the run outlives the context that started it - when the agent needs a memory of what it already verified, a rule for when to abandon a losing approach, and a gate that stops it declaring victory at 4am because the artifact looked plausible.

Use deep mode when at least one of these holds:

- The work has many interdependent pieces and finishing one can break another.
- The consequences are real - it ships to users, handles money, touches security or compliance.
- The user asked for a long unattended run: "overnight", "keep going until it is actually done", "full gauntlet".
- The user wants to watch a real dashboard rather than a scroll of messages.

If none of these hold, use quick mode. A long prompt on a short run just spends the agent's judgment on bookkeeping.

## What you infer before writing

Do not interrogate the user. Read these off the goal and the conversation, and only ask if getting it wrong would produce a materially different prompt:

- **Maturity** - throwaway demo, prototype, MVP, production, enterprise. This decides how much evidence a pass needs. Holding a prototype to production standards burns rounds on the wrong things; passing a production target on prototype evidence ships something broken.
- **Risk** - what happens when it fails. Higher risk means more independent verification, negative cases actually tested, and stronger evidence before anything is called done.
- **Hard constraints** - stack, deadline, compatibility, things that must not break. These have to be stated as non-negotiable, because a critic that never heard them will happily trade one away for elegance.
- **The deliverable** - what must actually exist at the end. If the goal implies building something, the run builds it. Advice about what to build is not the deliverable.

Fold these into the prompt as plain sentences. Never emit a variable block for the user to fill in - if you know enough to write the prompt, you know enough to write these.

## Template

Adapt the wording. Cut any section the goal genuinely does not need - a research brief has no viewport to normalise, a one-file tool has no integration seam.

```
Build [GOAL].

[CONTEXT - what already exists, who it is for, what must not break.]

This is a [MATURITY] target at [RISK] risk. Judge it at that level: do not hold a prototype to production standards, and do not let a production target pass on prototype evidence.

These constraints survive every round and are not open to critique: [CONSTRAINTS].

THE BAR

[BAR]. Fetch the real thing before you build anything. Every comparison from here is against the artifact itself - never against a description of it, never against your memory of it.

Put both sides in the same state before judging: same viewport, same data, same point in the flow, same length. If you cannot make them equivalent, name the differences that remain and do not let them decide the verdict.

[MEASURABLE HALF, if there is one - it also has to beat X on Y.]

DECOMPOSE

Split the work into the smallest pieces that can be built and judged on their own. Split by quality concerns that stand or fall separately, not by files or folders. Say which pieces the outcome actually depends on and work those first. A stack of finished small pieces is not progress while a load-bearing one is unresolved.

EACH PIECE

Fan out a builder and a separate critic with fresh context. The builder changes the actual artifact - a description of what should change does not count. The critic gets the artifact, not the builder's account of it: it runs it, renders it, opens it, reads it. "It should work" is not a finding when it can be executed.

The critic returns exactly this:

WINNER: A or B
CONFIDENCE: high, medium or low
BIGGEST GAP: the one change most likely to flip the next comparison
EVIDENCE: what was actually observed, run or measured

A low-confidence win does not close a piece. Get a second fresh critic, or stronger evidence, or both.

The critic is harsh. Praise is not useful - do not spend the review on what is already good. It can demand the piece be better, not bigger: the goal does not grow because a critic can imagine more. If something genuinely out of scope matters, note it separately and keep it out of the loop.

LOOP RULES

A piece passes when ours wins blind with real confidence. Nothing else passes it.

Keep a current best. The newest version is not automatically it - if a round fixes one thing and costs two, reject it, restore the previous best, keep the lesson.

If the same piece loses three times running, or the same gap keeps coming back, stop patching it. Hand it to a fresh builder that has not seen the previous attempts - give it the goal and the bar, not the history - and ask for a materially different approach: different structure, different interaction, different technology if that is what it takes. Compare that blind against the current best and keep whichever wins.

Do not make the bar easier because ours keeps losing, and do not restate the goal to match what got built. When ours loses, that is information about the gap, not a reason to move the target.

If the reference cannot be reached, or the artifact cannot be run in this environment, mark the piece BLOCKED and tell me why. Blocked is never passed.

PROGRESS PAGE

Keep a live page I can open while this runs. A real page, not a log. Update it whenever something actually changes state.

Show what is built and, separately, what has actually been verified. Those are different numbers and the distance between them is the honest picture. Weight it so a pile of finished small pieces does not read as nearly done while a load-bearing piece is still losing.

Also show the current biggest gap, what is blocking acceptance right now, open issues by severity, what each piece is waiting on, and how many rounds each has taken.

Never mark unverified work verified, never soften an issue to make the numbers look better, never show a piece as done before it has won.

BEFORE YOU TELL ME IT IS DONE

Pieces passing on their own does not mean the whole thing passes. Assemble it and put a fresh critic on the combined result: coherence, consistency, the end-to-end journey, the seams where independent work got joined. Ask what would still feel stitched together if one good team had built all of it.

Then take one fresh builder that has seen none of this. Give it the goal, the context and the bar, and ask for the strongest materially different solution it can produce. Compare it blind against the current best. If it wins, this is not finished - adopt it, or take what makes it better.

Then one fresh adversarial critic with this brief: the team believes this is finished, assume they are wrong, find the evidence it should not be accepted. Missed requirements, untested paths, broken edge cases, regressions, unsupported claims. Anything critical or high goes back into the loop.

DONE MEANS

Every piece has won blind or is explicitly blocked with a reason. Every pass points at something actually observed. The assembled artifact has been through its own comparison. The final challenger did not beat it. The adversarial critic found nothing critical still open. The progress page says the same thing.

If you stop for any other reason - a hard blocker, my call, no path left - say so plainly and list what is still open. Never dress a stop up as a finish.

Fan out subagents and ultracode.
```

## Fill rules

Same as quick mode, plus:

- **Cut, do not pad.** The template is a menu. A deep prompt that keeps sections the goal has no use for reads as ceremony, and the agent starts performing the process instead of doing the work.
- **The three end gates are the point.** Integration critic, final challenger, adversarial critic. They are what stops a long unattended run from converging on something internally consistent and externally mediocre. If you cut anything, cut elsewhere.
- **The dashboard is one paragraph, not a spec.** Say what has to be true (built and verified shown separately, biggest gap visible, nothing softened) and let the agent design it. Field-by-field card layouts are the fastest way to get a beautiful dashboard attached to work nobody verified.
- **Still no round count as an exit.** Rounds can cap cost if the user asked for a cap. They never define done.

## What deliberately stays out

Token accounting, resource registers, technology inventories, per-task metadata schemas, autonomy levels, formal state blocks. They generate work that looks like rigour and is not checkable, and every one of them is context spent on bookkeeping instead of on the artifact.

The test for anything you are tempted to add: if the agent quietly skipped this, would the final work be worse? If not, it is decoration.
