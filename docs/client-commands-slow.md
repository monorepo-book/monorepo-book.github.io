---
layout: problem
---
## Problem: local Git commands (like `add` or `status`) are slow

Symptoms:
- Running `git add` or `git status` makes Git appear to hang

### Tell Git you've got a big monorepo

- Turn on [`feature.manyFiles`](https://git-scm.com/docs/git-config#Documentation/git-config.txt-featuremanyFiles).

### Use new Git features

- Turn on the [split index](https://www.git-scm.com/docs/git-update-index#_split_index) using [`core.splitIndex`](https://git-scm.com/docs/git-config#Documentation/git-config.txt-coresplitIndex). This can help reduce the size of index _writes_ in some cases, but index reads can still be slow.
- Make sure [FSmonitor is enabled](https://git-scm.com/docs/git-config#Documentation/git-config.txt-corefsmonitor). On Windows, use the native FSmonitor; on macOS and Linux, use Watchman.
- Use `sparse-checkout` with sparse index using [`git sparse-checkout init --cone --sparse-index`](https://www.git-scm.com/docs/git-sparse-checkout#Documentation/git-sparse-checkout.txt-eminitem). Currently incompatible with the split index feature.
