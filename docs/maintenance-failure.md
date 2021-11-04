---
layout: problem
---
## Problem: maintenance on server fails or takes too long

While recent versions of GHES have mostly eradicated maintenance failures, they're still possible with certain data shapes and under very high load.

Symptoms:
- Maintenance times out or fails.

### Don't use the repo as a database or log file storage

Normally, guidance is that files which [deltafy](glossary.md#packfile) nicely are preferred in Git.
However, if a large number of big files with very similar content are added over time, the algorithm that looks for deltas can exhibit poor performance.
In any situation where (1) Git has many potential choices to delta two objects together and (2) evaluating those choices is expensive, Git spends lots of time choosing deltas.
And since we generally don't recompute existing deltas, the cost accrues primarily among new objects.

Log files (such as from an operating system, a web server, or similar) are prone to triggering this runaway behavior.
Log files are usually structured, self-similar text (offering many choices) and can be quite long (so evaluation is expensive).
They should be sent to a dedicated log storage system, not a Git repository.

Using a Git repo as a document database can trigger similar poor performance.
For example, storing a huge number of JSON files can be problematic.
Even when their contents change, they're often structurally similar and very large.

### Deepen your directory structure

Each version of a directory in Git is tracked by a `tree` object.
Each version of a file is tracked by a `blob`.
All other things equal, very wide directories (many entries) may bloat your repository quicker than narrow directories.

Why is this?
Consider a structure like:

```
|- src
|    |- file0000.txt
|    |- file0001.txt
|    |- file0002.txt
|    ⋮
|    |- file4999.txt
```

Each commit which changes any file will build a new tree for `src`.
A tree with 5000 entries is around 200 kB and will [deltafy](glossary.md#packfile) well.
This means it won't take up much disk space, but it will dramatically slow down traversal operations in maintenance.

If you instead shard those files into several directories:

```
|- src
|    |- dir00
|    |    |- file0000.txt
|    |    |- file0001.txt
|    |    |- file0002.txt
|    |    ⋮
|    |    |- file0099.txt
|    |- dir01
|    |    |- file0100.txt
|    |    ⋮
|    |    |- file0199.txt
|    ⋮
|    |- dir49
|         |- file4900.txt
|         ⋮
|         |- file4999.txt
```

Then modifying a single file will require one new 50-entry `tree` and one new 100-entry `tree`.
In aggregate, and especially over time, this will result in much less new data being created.
It will also help keep traversal operations fast.

- Deepen your directory structure.
Don't go deeper than necessary, since that can [trigger other issues](fetches-too-slow.md#shallow-your-directory-structure).
But in practice, deepening the tree with smaller directories is a good tradeoff.
- Consider making less granular commits, changing many co-located files at once instead of a series of smaller commits.

### Eliminate unreachable objects

Every object in the repository consumes around 150 bytes of heap memory during repacking.
If your repo grows so large that repacks start paging memory to disk, this can lead to serious slowdowns.
For objects still reachable in history, there's little recourse other than truncating the repo.

Unreachable objects represent "dead weight" and can be eliminated with a pristine garbage collection (GC).
On versions of GHES before 3.3, you should contact GitHub Support first for help deciding if this operation is right for your repo.
Certain data shapes can cause a pristine GC to make things dramatically _worse_ rather than better.
<!-- !! Uncomment when GHES 3.3 ships. !!
If you're on GHES 3.3 or later, this is safe due to the introduction of a new feature in Git.
In a GHE console, run `ghe-repo-gc --prune owner/repo` (where `owner/repo` is the repository you want to GC). -->

### Manually repack the repository

Often it's just a single or handful of commits which introduce a pathological data shape.
Because routine maintenance reuses previous results, the algorithm can get caught in a local optimum which ends up not so optimal with a new data shape.
Manual repacking can sometimes push past the local optimum (and once it's done, future maintenance will reuse its results).
In a GHE console, run `ghe-repo-gc owner/repo` (where `owner/repo` is the repository you want to GC), or use the button in Stafftools.

### Monitor for failures and deal with them swiftly

No matter what originally causes maintenance failure, it should be dealt with quickly.
Once a replica fails maintenance, interacting with that repo (including future maintenance attempts) consumes more system resources.
