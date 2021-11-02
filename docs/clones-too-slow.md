---
layout: problem
---
## Problem: clones are too slow

Symptoms:
- Developers or CI machines experience slow clones
- `git-sizer` indicates large total sizes for blobs, trees, and/or commits

### Reduce amount of data transferred

- For dev machines, consider [partial clone](https://github.blog/2020-12-21-get-up-to-speed-with-partial-clone-and-shallow-clone/#user-content-partial-clone) (`--filter=blob:none` or `--filter=blob:limit=<size>`)
- On CI machines, consider [shallow clone](https://github.blog/2020-12-21-get-up-to-speed-with-partial-clone-and-shallow-clone/#user-content-shallow-clones) with `--single-branch`

#### Partial clones

Partial clone tells Git:

> Hey, there are some contents of this repo that I don't need to have locally.
> You can fetch them later if I end up needing them.

A blobless clone fetches all the commit history and all the trees (directory structures) for the entire history.
It holds off on fetching the blobs (file contents) for most of history, only grabbing the ones needed for the tip of the branch(es) you're using.

The pros are fairly obvious: less space taken up locally, and less sent over the network.
The con: any time you need blobs that you don't already have, you'll need to have network connectivity to the repo _and_ those operations may take a little longer.
For most individual developers, this is a great tradeoff, since most people don't spend a lot of time working deep in history and do have a network connection.

#### Shallow clones

Shallow clone tells Git:

> Hey, I won't be working with history in this repo.
> Don't bother fetching it.

This can be useful on an ephemeral CI runner.
Specifically, where you want to build latest, certainly not care about history, and won't reuse the copy of the repo.
It can save on network bandwidth and is often the fastest way to get a fully copy of the working directory onto another machine.

At a certain size, though, you may find you're better off with persistent runners and a long-lived copy of the repo.
In that case, you should start with a partial clone and incrementally fetch to it.

For developers, it's usually not the best choice, since any commands which touch history will report incorrect or misleading results.
Also, it can be expensive for the server to handle fetches or to "unshallow" a shallow clone (turning it back into a regular clone).
We recommend _against_ this path for developer use.

### Remove large files

- Don't commit built artifacts and vendored dependencies into Git.
Block using server-side controls (and/or client-side pre-commit hooks, if you have a way to distribute them).
- Extract large files into Git-LFS.
- Going even further, consider rewriting your repo to extract _historical_ files into Git-LFS.

### Truncate repo history

- Rename the existing repo and create a new, tip-only version for future development.

You can attempt to use `git-replace` to "graft back in" the older history, but it's ignored by GitHub for any server-side operations.
It also disables valuable performance features in Git, so replace objects should only be used when necessary for a history investigation and then disabled again.
