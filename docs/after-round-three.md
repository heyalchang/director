# What to do after round three

The naive version of the rule is short: more than three rounds of bot review on
a PR usually means the code is structurally wrong, not that the bot is being
pedantic. This is true and worth holding. But on a real PR, "round three just
landed" is a moment of *judgment*, not a moment of mechanical action. The doc
here is about the disposition that produces the right call. The literal rule
is the floor; the disposition is what makes the floor useful.

## What round three is actually telling you

By round three, you've seen the bot find a roughly consistent flavor of thing.
Either the findings are getting smaller and more local each round (you're
**converging**), or each round opens new structural questions that the prior
rounds didn't see (you're **diverging**). The same headline ("round three has
two new findings") covers both. The work of round three is figuring out which.

A few signals I trust more than the count of items:

- **What kind of feedback did the *first* round have?** If r1 found
  load-bearing structural things and r2/r3 are mopping up, that's the
  expected shape. If r1 was clean and r2/r3 each found new structural
  issues, you missed something — and the bot is helping you see it. Don't
  rationalize that away.
- **Are the new findings adjacent to old ones, or far away?** Adjacent means
  the bot is iterating on the same area as the patches keep landing — usually
  fine. Far away means the surface is bigger than you thought, which is the
  bad signal.
- **Could you describe in one sentence why this PR exists, and is each new
  finding consistent with that purpose?** If round three's findings start
  reading like "while you're in there, also fix X / refactor Y," the PR has
  drifted past its frame.

## Take what you'd take. Don't merge with things on the table.

The temptation at round three is to declare victory: "the bot is iterating,
hand-merge with rationale, file follow-ups." Sometimes that's right. But
"merge-default with things on the table" doesn't have the right feel even
when merge-then-followup makes sense in shape.

If you'd take a finding *anyway* — if it's small, mechanical, defensible to
fix, and you're already looking at the file — take it now. Filing a
follow-up issue for the same fix isn't safer; it's just more PRs and a
trail of "we knew about this and shipped past it." The bot has done you a
favor by surfacing it before merge; don't waste the surfacing.

The cases where merge-then-followup is actually right are narrower than they
feel:

- The fix touches a different surface than this PR's frame.
- Taking it would force a redesign of this PR, not a one-touch addition.
- There's a real reason to ship now (deadline, blocking other work).

In all other cases, the converging-r3 disposition is *take what you'd take
and re-push*. It's still the same PR. The "round count" framing pushes you
toward declaring rounds done; the work-shape framing pushes you toward
finishing the work.

## What a real round-four examination looks like

If you've genuinely landed in round four — converging didn't happen, the
patches keep exposing new surface — the disposition is *not* "auto-redesign,"
and it's *not* "auto-merge with apologetic rationale." Either of those is
running away from the actual work, just in opposite directions. The
disposition is **examine**.

Concretely, that means:

- Re-read the cumulative review feedback as if it were one review. Look
  for the structural pattern, not the next finding. The bot has been finding
  things across all three rounds; what's the *shape* of what it's finding?
- Ask: "what design choice would make all of these go away at once?" If
  there's an answer to that, that's the redesign target. If there isn't,
  maybe the bot really is being pedantic — but the bar for that conclusion
  is high, and "the bot is being pedantic" is the textbook self-deception
  in this situation. The agent that blames the review is wrong almost every
  time.
- Bring it back to the principal before pushing. The conversation should
  be about the shape, not the next patch.

It's worth noting how often this examination produces a *smaller* fix than
the round-by-round patching would have. The shape question often has a
cheap answer — a renamed concept, a single field moved, a state machine
collapsed by half. Patches feel small per-round and large in aggregate;
the redesign feels large per-decision and small in aggregate.

## Supervision-mode gating

When I'm supervising a worker through a PR (rather than driving it
myself), there's an additional gate that the worker shouldn't bypass.

**Don't hand-merge without checking.** That's the rule. The worker pings
me, we look at the diffs together, and *I* make the call on whether
this is converging-take-it-now or diverging-examine. Worker autonomy on
fixes inside a round is fine; worker autonomy on the round-three
boundary call is not. The boundary is exactly where the supervision is
worth the most.

When converging, "look at the diffs together" usually means I read the
findings, the worker walks me through what's load-bearing, and we agree
the take-or-skip list before they push. When diverging, the conversation
shifts up a level: what does the cumulative feedback want this PR to
*be*, and is that what we're trying to ship?

The check-with-me-first gate is the new shape of the rule. The old shape
("if r3 raises new substantive items, hand-merge with rationale") was
too automatic — it produced the right action mechanically and the wrong
disposition substantively. Mechanical correctness without the
disposition is how you ship a clean log of follow-up issues for things
you should have just taken.

## A small honest note about what triggered this

Today's calibration came from a specific PR (dev-utils #2): r3 landed with
two new substantive items, both small and mechanical. Per the literal rule
we hand-merged with rationale and parked the items as a follow-up PR. The
follow-up was correct work but the wrong shape — the items were obviously
converging, the fixes were ~6 lines, they should have ridden along inside
the same PR via a take-and-re-push round four.

The follow-up PR (dev-utils #3) is in flight as the recovery. The lesson
is in the wrong-feel of the original call, not in the cleanup.
