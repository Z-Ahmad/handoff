---
name: handoff
description: Produce a minimal, agent-agnostic session handoff with workflow history, git status, and direct links to the top 3 important changes. Use when the user asks for a handoff, fresh-context prompt, /handoff, or to continue work in another agent/session.
disable-model-invocation: true
---

# Handoff

Produce a paste-ready handoff another agent can run cold. Minimal tokens. No fluff.

## Steps

1. Collect facts (run in parallel):
   - `git status --short --branch`
   - `git log --oneline -12`
   - PR URL if any (`gh pr view --json url,number,title` or known PR)
   - Uncommitted vs committed delta
2. Pick the **top 3 important changes** (not the last 3 commits). Prefer: direction pivots, blocking bugs, user-rejected artifacts, next required action.
3. For each of the 3, give a **direct link**:
   - Commit: `https://github.com/<owner>/<repo>/commit/<sha>`
   - PR: `https://github.com/<owner>/<repo>/pull/<n>`
   - File path in repo (absolute from root) when the change is uncommitted
   - Artifact path under `/opt/cursor/artifacts/` when visual evidence matters
4. Emit the template below. Do not add sections not listed. Do not narrate process.

## Template

```markdown
# Handoff — <one-line goal>

## State
- Branch: `<branch>` → `<base>`
- PR: <url or none>
- HEAD: `<sha>` <subject>
- Dirty: <yes/no — N files> | Clean

## History (compressed)
- <oldest relevant beat>
- <pivot / key decision>
- <newest relevant beat>

## Top 3 changes
1. [<label>](<link>) — <one clause why it matters>
2. [<label>](<link>) — <one clause why it matters>
3. [<label>](<link>) — <one clause why it matters>

## Now
- Done: <≤3 bullets>
- Open: <≤3 bullets, blockers first>
- Next: <single concrete action>

## Commands
```bash
<only the 2–5 commands the next agent must run>
```

## Do not
- <≤3 hard constraints>
```

## Rules

- Agent-agnostic: no model names, no “I”, no session lore.
- Prefer links over pasted diffs.
- If dirty work is the story, say Dirty and link files/artifacts — do not pretend it is committed.
- If user rejected a direction, put the rejection in History and Open.
- Cap total handoff ≤ ~40 lines.

