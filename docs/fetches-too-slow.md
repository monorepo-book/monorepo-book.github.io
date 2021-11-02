---
layout: problem
---
## Problem: fetches are too slow

Symptoms:
- Developers or CI machines experience slow fetches
- Server CPU usage spikes, maxes out, or is throttled during fetch activity

### Validate network topology

- Make sure there's a fast enough network (LAN or WAN) between your clients and the Git server.

### Allow maintenance to complete

In narrow cases, having too many incoming pushes per unit of time can cause fragmentation of indexes due to maintenance not completing.
This in turn can cause all fetch operations to slow down.

- Slow the rate of pushes to fewer than 50/hour in the repo. (This is a workaround and diagnostic tool more than it is a recommendation.)
- Upgrade to GHES 3.2 or later, which includes a refactored maintenance subsystem.

### Use background maintenance on clients

Developer machines typically have network access most of the time, so Git object data can be fetched in the background.
If a developer runs `git maintenance start` in their repository, this enables several background maintenance tasks, including [the `prefetch` task](https://git-scm.com/docs/git-maintenance#Documentation/git-maintenance.txt-prefetch).
By default, this task fetches new objects from the origin server on an hourly basis, leading to the developer not waiting as long to get the latest refs during their foreground `git fetch` and `git pull` commands.

### Improve directory structure

Each version of a directory in Git is tracked by a `tree` object.
Each version of a file is tracked by a `blob`.
All other things equal, a deeply-nested file changing will create more work for Git than a shallowly-nested file.
For example, consider:

```
|- dir1
|    |- file1.txt
|
|- dir2
     |- dir3
          |- dir4
               |- file2.txt
```

When you commit a change to `file1.txt`, Git has to track three new objects:
- the new `blob` for `file1.txt`
- the new `tree` for `dir1`
- the new commit containing the new `tree` for `dir1` and the old `tree` for `dir2`

When you instead commit a change to `file2.txt`, Git has to track five new objects:
- the new `blob` for `file2.txt`
- the three new `tree`s for `dir4`, `dir3`, and `dir2`
- the new commit containing the old `tree` for `dir1` and the new `tree` for `dir2`

In a simple example like this, it makes no difference whether Git creates 3 or 5 objects.
But if on average most of your changes are nested 6-10 levels deep, over time, that drives a lot more growth of the repo than having changes nested 1-5 levels deep.

- Consider shallowing your directory structure.
- Consider making less granular commits, changing many deeply-nested files at once instead of a series of smaller commits.
