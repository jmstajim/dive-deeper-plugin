# /dive-deeper

> Stop planning on the surface. Make Claude actually think it through.

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that forces deep analysis during planning — tracing execution paths, finding all related code, surfacing edge cases, and catching problems before they become bugs.

## The problem

You're planning a change with Claude. It gives you a reasonable-looking plan. But you've been burned before — the plan misses a caller three files away, doesn't consider what happens when the array is empty, or ignores that two things can run at the same time.

You end up typing "look deeper", "check edge cases", "are you sure that's everything?" over and over.

`/dive-deeper` replaces all of that with one command.

## What it does

When invoked, Claude will:

| Step | What happens |
|------|-------------|
| **Map blast radius** | Find all callers, callees, shared state, and affected files |
| **Trace full flow** | Walk through user-facing scenarios start to finish, including interruptions |
| **Surface edge cases** | Empty/nil/zero states, boundaries, concurrency & reentrancy, data migration, partial failures |
| **Present findings** | Concrete failure scenarios filtered by real risk, with proposed fixes |
| **Revise the plan** | Update the plan to address everything found above |

Each finding comes in a structured format:

```
### [Finding title]
Where: file:line
Risk: high/medium/low
Scenario: [concrete sequence of events that causes the problem]
Fix: [specific change needed]
```

## Install

### CLI

```bash
/plugin marketplace add jmstajim/dive-deeper-plugin
/plugin install dive-deeper@dive-deeper-plugin
```

### VS Code

1. Open Claude Code in VS Code
2. Type `/plugin` or click **Manage plugins**
3. Go to **Marketplaces** tab
4. Enter `jmstajim/dive-deeper-plugin` and click **Add**
5. Go to **Plugins** tab, find `dive-deeper` and install it

## Usage

While planning a feature or change:

```
/dive-deeper
```

That's it. Works best when Claude already has context about what you're building — invoke it after the initial plan is drafted. If something is unclear, Claude will ask before guessing.

## Example

```
You:  Add a caching layer to the API client

Claude: [drafts a plan with 5 steps]

You:  /dive-deeper

Claude: Found 3 issues:
        1. Cache invalidation race between background refresh and user-triggered fetch
        2. Cached responses persist across logout — leaks user data
        3. No cache size limit — memory grows unbounded on long sessions
        [updates plan with fixes]
```

## License

MIT
