---
layout: problem
---
## Problem: pushes are too slow

Symptoms:
- Developers complain that pushes are slow (or slower than they used to be).

### Reduce number and duration of pre-receive hooks

- On GHES, you may create custom pre-receive hooks.
These are executed synchronously during the push, which can slow the push down.
Consider removing them entirely, reducing the number, or speeding up their performance.

### Fetch more often

- The further the client and server drift from one another, the longer each push will take.
Consider fetching regularly, even if you aren't pushing.
A great way to automate this is to schedule background maintenance.
If a developer runs `git maintenance start` in their repository, this enables several background maintenance tasks, including [the `prefetch` task](https://git-scm.com/docs/git-maintenance#Documentation/git-maintenance.txt-prefetch).

### Put replicas closer to each other

- For GHES in a replicated configuration, accepting each push requires consensus between all the nodes.
The protocol used to establish consensus, three-phase commit (3PC), has to make multiple network roundtrips.
Every millisecond of latency between nodes is amplified by this 3PC process, putting an upper bound on how fast the system can accept pushes.

### Make batch updates instead of one-offs

- Each update is one transaction, whether it updates a single ref or many.
Ref update transactions are the limiting factor, not how many refs are updated.
The more you can batch together, the less overhead you incur per ref.

### Close out stale PRs

- Although not usually the limiting factor, each open PR represents potential work the server has to do for each update.
By closing old, stale PRs, you avoid this overhead.
