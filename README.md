# Chase AI+ Skills

Claude Code skills for the [Chase AI+](https://www.skool.com/) community. This repo
is a **Claude Code plugin marketplace** — install once, get every skill, update with
one command.

> Access to this repo comes through the Chase AI+ classroom. Keep the link inside the
> community.

---

## Install (2 commands)

In Claude Code:

```
/plugin marketplace add cth9191/chase-ai-skills
/plugin install chase-ai@chase-ai-plus
```

That's it. To pull updates later:

```
/plugin marketplace update chase-ai-plus
```

---

## What's inside

### `loop-engineer`

An interview-driven **loop-engineering architect**. You bring a task you want to
automate; it does the hard part for you — the *decisions*, not just the code.

It will:

1. **Diagnose** where your task sits on the maturity ladder (manual → codified skill
   → automated → self-improving loop).
2. **Grill you one question at a time** — recommending an answer for each — until the
   loop is fully specified: trigger, execution, success criteria, state, stop rule.
3. **Force the success-criteria decision** through a 5-tier verification ladder
   (deterministic → rule → metric → LLM-judge → human). This is the thing most people
   skip, and the thing that makes or breaks a loop.
4. Write a **`LOOP-SPEC.md` blueprint** and stop for your sign-off.
5. **Scaffold only the rung you're ready for** — execution skill, trigger config,
   state schema, verification block, stop rule, and a README that explains the whole
   loop so you can debug it, not just run it.

And — most importantly — it's willing to tell you a task **shouldn't be a loop**, so
you don't burn tokens spinning on something with no real success criteria.

**Run it:**

```
/chase-ai:loop-engineer
```

…or just describe a recurring task you want to automate ("every week I want Claude
to…") and it triggers on its own.

See [`walkthrough.html`](./walkthrough.html) for a full example session.

---

## Background

This skill is the companion to the video **"The Four Step Process to Loop Engineer
ANYTHING (+ Why Prompt Engineering Isn't Dead)."** The video teaches the theory and
walks the manual setup; this skill removes the manual labor and the decision paralysis.

---

© Chase AI. For use by Chase AI+ members. All rights reserved.
