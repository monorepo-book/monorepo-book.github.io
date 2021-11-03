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

### Shallow your directory structure

A deeply-nested directory structure can bloat a repo more quickly, and sometimes in ways that don't [deltafy](glossary.md#packfile) well.
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

When you commit a change to `file1.txt`, Git has to track four new objects:
- the new `blob` for `file1.txt`
- two new `tree`s for `dir1` and the root directory
- the new commit containing the new `tree` for `dir1` and the old `tree` for `dir2`

When you instead commit a change to `file2.txt`, Git has to track six new objects:
- the new `blob` for `file2.txt`
- four new `tree`s for `dir4`, `dir3`, `dir2`, and the root
- the new commit containing the old `tree` for `dir1` and the new `tree` for `dir2`

In a simple example like this, it makes no difference whether Git creates 4 or 6 objects.
But if on average most of your changes are nested 6-10 levels deep, over time, that drives a lot more growth of the repo than having changes nested 1-5 levels deep.

- First, consider making less granular commits, changing many deeply-nested files at once instead of a series of smaller commits.
- If you have unnecessary layers of directories, consider shallowing your directory structure.
But, be aware that going [too shallow](maintenance-failure.md#deepen-your-directory-structure) will cause problems than it resolves.
This guidance is primarily for pathological depth, for example, putting each file several layers deep with no siblings for many levels.
