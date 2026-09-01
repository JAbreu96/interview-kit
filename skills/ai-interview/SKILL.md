---
name: ai-interview
description: Pit crew for a timed AI-conducted coding interview.
disable-model-invocation: true
argument-hint: "paste the interview prompt, and the total minutes if not 60"
---

# AI interview

You are the **pit crew**. The user is the candidate, the clock is real, and working code beats
a complete pipeline.

Nine phases, **one stop**. Everything the user approves is collected into a single gate after
phase 3; after it, run to the end without blocking them again.

Say this once at kickoff: `/fast` runs Opus with faster output without downgrading the model.

## Phase 0 — Kickoff (~2 min)

Stamp the start with `date +%s` and keep the number; every boundary compares against it.

Read the prompt, then propose the phase list with a minute budget beside each, scaled to the
total the user gave (60 when they gave none). Drop phases this task does not need.

Present it as a table and wait for approval. The budgets below are the 60-minute defaults.

## Phase 1 — Requirements (~5 min)

Produce two artifacts.

**The MVP cut.** Label every requirement:

- **must** — the smallest set that makes the prompt's headline ask work end to end. A
  requirement is a *must* because the submission is not a submission without it, never because
  it is easy or interesting.
- **should** — the stretch queue, worked after the must-set is green.
- **could** — named in the README, not built.

Demote implied and inferred requirements on your own authority. A requirement the prompt
**states outright** stays in the must-set unless the user removes it: propose the cut as its
own line at the gate — *"the prompt asks for X; I recommend cutting it to protect Y — your
call"* — and treat it as in scope until they act on it. Graders score against their own
checklist, so a cut the user did not see is the one failure with no recovery.

**The assumption ledger.** Every ambiguity you resolved, with the assumption you made, one
line each. It ships in the README.

## Phase 2 — System design (~6 min)

One mermaid diagram, in chat, covering the must-set. It is a thinking aid: its job is to make
the missing component visible before anyone writes code.

Draw a decision tree or a data-flow diagram instead when that is the shape the task actually
has. One diagram, not three.

## Phase 3 — Assertion manifest (~5 min)

Dispatch `interview-test-author`, asking for the manifest. It returns one line per intended
assertion over the must-set, plus any must-set requirement with no assertion covering it.

## THE GATE

Present in one message, together:

1. The MVP cut — with any explicit-requirement cut on its own line, defaulting to in scope.
2. The assumption ledger.
3. The assertion manifest, and its gap list.
4. The diagram.

Ask for approval. This is the only time you stop the user; after they approve, run to the end.

## Phase 4 — Implementation design (~3 min)

Break the must-set into work units, naming the files each one touches.

**Parallelism bar.** Flag a split when two or more units touch provably disjoint file sets
*and* each holds ~10 minutes or more of work. Below that bar, one implementor — a subagent
starts cold, and on a small codebase two implementors contend on the same files. The flag is a
question to the user; they decide.

## Phase 5 — Execution (~18 min)

Dispatch `interview-implementor`. Must-set first, then the stretch queue while time allows.

Commit after each work unit lands: `git add -A && git commit -qm "wip: <unit>"`. These are
checkpoints, not history — they exist so a later phase can fall back to a known-good tree.
Pushing and submitting still stay with the user.

Code throwing → follow `~/.claude/skills/interview-debug/SKILL.md` before continuing.

## Phase 6 — Review (~5 min)

Dispatch `interview-reviewer` on the working diff. It returns findings already triaged into
*fix now* and *README limitation*. Send the fix-now findings to the implementor; hold the
others for the README.

Review lands here, before the tests exist, so a structural finding costs a rewrite of code
rather than a rewrite of code and tests.

## Phase 7 — Test code (~8 min)

Dispatch `interview-test-author` again. It writes tests to the manifest **as approved** and
runs them. A test outside the approved manifest gets surfaced, not slipped in.

Tests red → follow `~/.claude/skills/interview-debug/SKILL.md` before anything else.

## Phase 8 — Docs (~5 min)

Assemble the README from what the pipeline already produced:

- What was built, and how to run it.
- The assumption ledger.
- **Scope** — what was cut, and why.
- **Known limitations** — the review findings left unfixed.
- The diagram, when it still matches the code. A drifted one gets corrected in 60 seconds or
  dropped; a diagram contradicting the code says you designed one thing and built another.

The implementor documents each unit as it writes, so finish with a gap check for anything
undocumented rather than a fresh pass over every file.

Close with a summary of what to submit. Committing, pushing and submitting stay with the user.

## The clock

Hold **3 minutes unallocated** as the debug reserve — the budgets above sum to 57, not 60.
Release it to whichever phase needs it; when nothing breaks it goes to docs or the stretch
queue. It is what lets the abandon protocol land instead of overrunning the submission.

Run `date +%s` at every phase boundary and report elapsed and remaining against the approved
budget. Recommend a move-on; leave the decision with the user.

Past **two-thirds** of the total budget, the boundary report becomes a **salvage
recommendation**:

- Which manifest lines have no implementation behind them.
- What to cut, drawn from the approved labels — stretch items go before any *must*, and a
  *must* at risk gets said out loud.
- Which review findings become README limitations instead of fixes.
- The shortest path to a green test run.
- One line: what the submission is if the user stops right now.

That last line is the point of the whole protocol. At minute 45 the instinct is to finish the
thing in front of you; three working features with green tests and a README beat five
half-built ones, and the user can only make that trade if someone states the current position
plainly.
