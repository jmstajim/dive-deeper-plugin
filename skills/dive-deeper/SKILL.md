---
name: dive-deeper
description: Deep-dive analysis during planning — trace full execution paths, find all related code, surface edge cases and potential problems. Only invoke via /dive-deeper command.
---

# Dive Deeper

The user feels the current analysis is incomplete — blind spots, unexplored paths, or risks that haven't been considered. Your job is to find them.

## How to work

Actually search the codebase — don't reason from memory. Use Grep to find call sites, Glob to find related files, Read to examine implementations. Every claim ("this function is called from 3 places") must be backed by a tool call. If you haven't grepped for it, you don't know it.

If something is unclear — the intent behind the change, which behavior is expected, or how a component is supposed to work — ask the user before guessing. A wrong assumption baked into the analysis is worse than no analysis. Keep questions focused and specific.

## What to do

### 1. Map the blast radius

Start from the function/type/file at the center of the change. Trace outward:

- **Who calls this?** Grep for all call sites — not just the obvious ones. Check tests, UI bindings, interface implementations, closures, dependency injection.
- **Who does this call?** Follow the dependency chain down. If you're changing a service, what does it depend on? Could those break?
- **Who shares data with this?** Look for shared state — globals, singletons, injected objects, config keys, file paths, notification/event names.

Build a concrete list of affected files with line numbers. Not "this might affect some views" — name them.

### 2. Trace the full flow

Pick the most important user-facing scenario that touches this code. Walk through it step by step:

- What triggers it?
- What state must exist before it works?
- What state does it leave behind after?
- What happens if it's interrupted halfway (process killed, network lost, concurrent call)?

If the change touches multiple flows, trace each one. Don't say "this could be a problem" — show the specific sequence of events that leads to failure.

### 3. Surface edge cases

Think about the inputs and states the original plan didn't consider:

- **Empty/nil/zero**: Empty collection? Nil value? Zero count?
- **Boundaries**: First item, last item, single item, maximum allowed.
- **Concurrency & reentrancy**: Can two callers hit this simultaneously? Can it be called while already running?
- **Migration**: Does this change the shape of persisted data? What happens to existing data?
- **Partial failure**: If this fails halfway, is the state consistent or half-updated?

### 4. Present findings

For each finding, filter first: is this a real risk or theoretical noise? Does the current plan already handle it? Only report what actually matters.

Format:

```
### [Title]
Where: file:line
Risk: high / medium / low
Scenario: [concrete sequence of events leading to failure]
Fix: [specific change needed]
```

### 5. Revised plan

This is the main deliverable. Take the original plan and update it — add missing steps, reorder if needed, flag items that need extra attention. Make it clear what changed and why.

If the plan is actually solid after deep analysis, say so — "I checked X, Y, Z and the plan covers them." Don't manufacture problems to seem thorough.

## What NOT to do

- Don't re-read files you already read — look at the ones you HAVEN'T.
- Don't list generic risks ("this could have race conditions") — show the specific race.
- Don't pad findings. Three real problems beat ten theoretical ones.
- Don't lose sight of the original task. Deep analysis serves the plan, not the other way around.
