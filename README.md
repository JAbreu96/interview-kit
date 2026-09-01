# interview-kit

Pit-crew tooling for a timed, AI-conducted coding interview.

- `skills/ai-interview` — the nine-phase pipeline. One gate, then it runs to the end.
- `skills/interview-debug` — what ships when the bug wins. Tripwire and abandon protocol.
- `agents/` — the three subagents the pipeline dispatches: implementor, reviewer, test-author.

Both skills are `disable-model-invocation: true`; they are slash-invoked by the candidate.

## Install

Symlinked into `~/.claude`, which is where Claude Code discovers them:

```bash
ln -sfn "$PWD/skills/ai-interview"    ~/.claude/skills/ai-interview
ln -sfn "$PWD/skills/interview-debug" ~/.claude/skills/interview-debug
for a in implementor reviewer test-author; do
  ln -sfn "$PWD/agents/interview-$a.md" ~/.claude/agents/interview-$a.md
done
```
