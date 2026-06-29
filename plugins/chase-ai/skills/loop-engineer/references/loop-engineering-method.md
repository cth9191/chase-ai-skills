# Loop Engineering — The Method

The theory behind the `loop-engineer` skill. Distilled from Chase AI's
"The Four Step Process to Loop Engineer ANYTHING (+ Why Prompt Engineering Isn't
Dead)." This is the model the interview enforces.

## Table of contents
- What loop engineering actually is
- Why prompt engineering isn't dead
- The four phases of a loop
- The maturity ladder (the hero's journey)
- The five-tier verification ladder
- When NOT to loop
- Judge cautions (LLM-as-judge)
- State & the measurement-lag trap
- Loop engineering vs `/goal` vs auto-research

## What loop engineering actually is

Loop engineering = giving an agent (Claude Code, Codex, whatever) a task and
setting it up to complete that task by **iterating repeatedly until it hits a
defined success criterion**, instead of running a prompt once. A loop is just a
prompt plus scaffolding, repeated. At its core it's still prompts — "prompts
stacked on prompts that run over and over until the task is done."

The real payoff isn't repetition — it's **self-improvement**: each run reads what
prior runs tried and got, and does better because of it.

## Why prompt engineering isn't dead

"Prompt engineering is dead, only loop engineering matters" is backwards. A loop is
made of prompts; better prompts make better loops. They're different tools for
different jobs — a wrench didn't kill the screwdriver. Not every task should be a
loop. Knowing *when* a loop fits is the actual skill.

## The four phases of a loop

Every loop has four phases (plus a stop rule):

1. **Trigger** — how the run kicks off. Schedule, cron, webhook, manual, event. You
   want it automatic ideally, but any reliable start works.
2. **Execution** — where the AI does the work, usually best expressed as a **skill**,
   because a skill tells the agent to do a specific thing a specific way for a
   specific output — exactly what a loop targets.
3. **Verification** — the success criteria. *How do we know it's done right?* This is
   the make-or-break phase (see the five tiers).
4. **State** — output + memory. What the run produced and what happened, recorded so
   the next run can read it. This is what makes the loop self-improving (Ralph-loop
   lineage).

**Stop rule** (not a phase but essential): when does looping end? Goal hit, no more
progress, or a hard cap. AI isn't free — always have at least one hard stop.

## The maturity ladder (the hero's journey)

You don't jump straight to a loop. You climb:

1. **Manual.** Do the task by hand in Claude/Codex, very hands-on. This *must* be
   step one — it proves the task is even possible for the AI. Most people get stuck
   here forever.
2. **Codify.** Turn the validated manual process into a **skill** so you're not
   re-typing the steps.
3. **Automate.** Put the skill on a trigger (a routine/schedule: "run the X skill
   daily at 9am"). Now the trigger and execution phases are done — before you've even
   "started" loop engineering.
4. **Loop-engineer.** Add the second half: **success criteria + state logging** so it
   self-improves. This is the only step that turns an automated skill into a true loop.

Key insight: by the time you've climbed to step 3 you've already built half a loop.
Step 4 is just adding verification + memory. And sometimes step 3 is enough — if you
can define success *and* record state, you may not even need a formal step 4.

## The five-tier verification ladder

"If you get nothing else: success criteria." How you can tell the loop succeeded,
from best to hardest:

1. **Deterministic yes/no.** Compiles? Tests pass? Cleanest possible signal.
2. **Rule / constraint.** "Runtime under X," "no lint errors," "passes schema."
3. **A metric / number.** Runtime ms, likes, conversion rate. Objective, loopable,
   can stay fully automatic.
4. **Fuzzy — needs a judge.** "Is this a good article?" No clean number, or the number
   (likes) is a noisy proxy for quality. Requires an LLM-as-judge. Effectiveness
   drops; be deliberate.
5. **Needs a human.** Genuinely subjective or high-stakes. Put a human in the loop.
   Most powerful for quality, least autonomous — and a signal to ask whether it
   should be a loop at all.

Tiers 1–3 are where loops shine. Tiers 4–5 are where most real-world tasks actually
live (content, research, judgment work), so don't pretend a fuzzy task is tier 1 —
engineer the judge or the human gate honestly.

## When NOT to loop

- **No real success criterion.** If you can't define "done right" even fuzzily, a loop
  just spins and burns tokens. Don't build it.
- **One-shot tasks.** "Iterate until correct, once" is a single session — use `/goal`,
  not a loop.
- **Impatience, not horizon.** If you'd be fine running it by hand occasionally, you
  may just want a skill, not a loop.
- **Pure subjectivity with no tolerance for a proxy.** If only a human can judge and
  the human must judge every run, the "loop" is really a human workflow with AI assist.

Naming these honestly is the whole value of the interview.

## Judge cautions (LLM-as-judge)

For tier-4 criteria you'll use an LLM to judge output. Two rules:

- **Never let the model judge its own work.** Models reliably overrate their own
  output. If Claude wrote it, have a *different* model (e.g. Codex) judge it.
- **A high metric ≠ high quality.** Likes can spike from timing or topic, not craft.
  Be careful before treating a noisy proxy as the gold standard the loop optimizes
  toward — you can teach the loop the wrong lesson.

## State & the measurement-lag trap

State is the self-improvement engine. A run must record: the artifact, the outcome/
score, what was tried, what worked, what didn't. The next run reads this and adapts.
Without it every run is a silo and nothing improves.

**Measurement lag:** sometimes the outcome arrives long after the run (you post an
article at 9am; likes accrue over days). Then a single loop can't both act and learn
in one pass — you need a **second loop** that periodically scrapes outcomes and
backfills the state store, so the acting loop has real data to learn from. This is
the subtlety most people miss when the success metric is downstream.

## Loop engineering vs `/goal` vs auto-research

- **Auto-research (Karpathy-style).** Great, but *requires* hard, defined success
  criteria — it can't handle fuzzy goals. Perfect for "make this Python faster"
  (objective number). Not for "write better LinkedIn articles."
- **`/goal` (Claude Code) / Codex single session.** This *is* loop engineering in a
  nutshell — iterate until a condition — but scoped to **one session, one outcome**.
  It completes a thing once.
- **Loop engineering at large.** Infinite horizon. It's like running `/goal` forever
  with memory between runs — the self-improvement aspect is what `/goal` and
  auto-research don't give you. "Make better articles every week, forever" is loop
  engineering; "make me one good article now" is `/goal`.

The differentiator vs all of them: loop engineering tolerates somewhat fuzzy criteria
(via judges/humans) and carries state across an unbounded series of runs.
