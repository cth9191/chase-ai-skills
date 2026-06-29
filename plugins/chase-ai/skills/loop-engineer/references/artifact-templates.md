# Artifact Templates

Templates the `loop-engineer` skill emits. Fill the `<placeholders>`. Keep what's
relevant to the rung being scaffolded; don't dump every file at once.

Default output dir: `./loops/<slug>/` where `<slug>` is a kebab-case name for the
loop (e.g. `linkedin-article`, `python-speedup`).

---

## 1. LOOP-SPEC.md (the blueprint — written at step 3, before any code)

```markdown
# Loop Spec: <loop name>

_Drafted <by loop-engineer>. This is the blueprint — review before scaffolding._

## Task
<one paragraph: what the loop does and why>

## Verdict: should this be a loop?
<YES / NO / NOT YET>. <reasoning — name the success criterion and the horizon. If
NO or NOT YET, say what to do instead and stop.>

## Maturity rung
Current: <0–4, label>. Scaffolding target this pass: rung <N> → <N+1>.

## The four phases
- **Trigger:** <schedule/cron/webhook/manual + cadence>
- **Execution:** <skill name; new or existing; what it does>
- **Verification:** Tier <1–5> — <exactly how success is measured>
  - <if tier 4: which judge model, why not the executor>
  - <if tier 5: what the human approves and when>
- **State:** <store location + format; what gets logged; how next run reads it>
  - Measurement lag: <none | second scraper loop needed, describe>

## Stop rule
<goal-hit | no-progress plateau | hard cap N / token budget — pick at least one hard stop>

## Open risks
<anything fuzzy, any proxy-metric danger, any unproven assumption>

## Files this pass will generate
<list>
```

---

## 2. MANUAL-RUN.md (rung 0 — task not yet validated)

```markdown
# Manual Run-Through: <task>

You haven't proven an AI can do this task yet, so we're NOT building a loop. Do it
by hand first. A loop only automates something that already works.

## One-time manual run
1. Open Claude Code / Codex in <relevant repo or folder>.
2. Prompt it directly: "<the plain-language task>".
3. Inspect the output. Did it actually produce <acceptable result>?
   - If yes → you're at rung 1. Re-run `loop-engineer` to codify it into a skill.
   - If no → adjust the prompt and retry. If it never works manually, it won't work
     in a loop. Stop here.

## What to watch for
- What did you have to correct by hand? (those corrections become skill instructions)
- What inputs/tools did it need? (those become the skill's dependencies)
- How would you know it did a good job? (that's your future success criterion)
```

---

## 3. Execution skill (rung 1 → 2)

Write to `loops/<slug>/skill/SKILL.md`. This is a normal Claude Code skill.

```markdown
---
name: <slug>
description: >-
  <what the task produces and when to run it. If this skill will be fired by a
  trigger inside a loop, say so.>
---

# <Loop name> — execution

<imperative steps to perform the task a specific way for a specific output>

## Self-improvement (added at rung 3+)
Before doing the work, read the loop state at `../state.json` (or
`<state path>`). Review prior runs: what was tried, the scores, what worked and
what didn't. Bring that into this run — don't repeat a losing approach. After the
work, append this run's result to state (see verify.md / state.schema.json).
```

---

## 4. trigger.md (rung 2 → 3)

```markdown
# Trigger: <loop name>

Cadence: <e.g. daily 09:00>.

## Option A — Claude Code routine / scheduled task
Create a routine/automation that runs the `<slug>` skill on schedule. In the
routine instructions, simply: "Run the <slug> skill." Set the schedule to <cadence>.

## Option B — OS scheduler (portable)
- macOS/Linux: cron entry — `0 9 * * *  cd <repo> && claude -p "run the <slug> skill"`
- Windows: Task Scheduler task running the same command.

## Option C — Webhook / event
<only if event-driven: describe the event and the handler that invokes the skill>

Pick the simplest that fits. Automating the trigger is independent of full
loop-ification — you can do this before adding state/verification.
```

---

## 5. state.schema.json + verify.md + stop.md (rung 3 → 4)

### state.schema.json
```json
{
  "loop": "<slug>",
  "runs": [
    {
      "run_id": "<iso-timestamp or seq>",
      "timestamp": "<iso8601>",
      "inputs": "<what this run was given>",
      "artifact_ref": "<path/url to what it produced>",
      "approach": "<what was tried this run — hooks, params, diffs, angle>",
      "score": "<the metric value, or judge verdict, or 'pending' if lagged>",
      "worked": "<what helped>",
      "failed": "<what didn't>"
    }
  ]
}
```

### verify.md
```markdown
# Verification: <loop name> — Tier <N>

## How success is measured
<the concrete check>

### Tier 1–3 (objective)
Run <command/script> → read <metric>. Pass if <condition>. Record `score` in state.

### Tier 4 (judge — different model than executor)
Send the artifact to <judge model, e.g. Codex> with this prompt:
> "<judge prompt — score the artifact on <criteria> from 1–10, list what works and
> what doesn't. Be adversarial; do not flatter.>"
Record the verdict + reasons in state. NEVER let the executor judge its own output.

### Tier 5 (human gate)
Stop and present the artifact to the user. Do not mark success autonomously. Record
the human's decision + notes in state.

## Measurement lag
<if the score arrives later: describe the second loop that scrapes outcomes and
backfills `score` for past runs by `run_id`>
```

### stop.md
```markdown
# Stop rule: <loop name>

Stop looping when ANY of:
- Goal hit + verified: <condition>
- No progress: <metric> hasn't improved over the last <N> runs.
- Hard cap: <N iterations> or <token/$ budget> reached.

Always keep at least one hard cap — AI isn't free.
```

---

## 6. README.md (always, at rung 3+)

```markdown
# <Loop name>

## What this loop does
<plain language>

## The decisions behind it
- Why it's a loop (or what tier of success criteria it relies on): <…>
- Trigger: <…> · Execution: <…> · Verification: <…> · State: <…> · Stop: <…>

## How to run it
<command(s)>

## How to read its state
State lives at <path>. Each entry is one run with its approach and score. To see
what's improving, sort runs by `score`.

## How to change it
<which file to edit for trigger / criteria / stop>
```
