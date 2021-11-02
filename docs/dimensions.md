## Dimensions of scale in Git

The phrase "giant Git repo" means vastly different things depending on who you talk to.
Git has many different scale dimensions.
It's useful to identify these dimensions to think more precisely about what makes a "giant" repo.

Some of these dimensions are (at least partially) entangled.
As the value of one gets bigger, another also gets bigger.
Other dimensions are inversely related &mdash; as one aspect gets worse, another gets better.
This can make [diagnosing](detection.md) and [solving](solutions.md) problems tricky.
Still, it's good to start with an understanding of the space.

### Sizes

Size is an obvious place to start.
The more data Git has to operate on, the longer each operation will take, and the more space will be required.

- Size of [packfile(s)](glossary.md#packfile) &mdash; the contents of your `.git` folder, both on the client and on the server
- Size of [working tree](glossary.md#working-tree) &mdash; the contents you `git checkout` so you can work on them
- Size of [index](glossary.md#index) &mdash; the bookkeeping Git does to track the difference between your working directory and the branch you're on
- Size of [LFS](glossary.md#lfs) data &mdash; contents not directly under Git's control, but still have to pass through it once in a while

### Quantities

The next area which springs to mind are counts of things.
Typically the scale factor is similar to [sizes](#sizes) &mdash; more of these means more work for Git to traverse during its activities.

- Number of [refs](glossary.md#ref) &mdash; branches, tags, and some other less common tracking references
- Number of [packfiles](glossary.md#packfile) &mdash; certain Git operations work far better with one packfile than with many
- Number of [commits](glossary.md#commit)/[trees](glossary.md#tree)/[blobs](glossary.md#blob) &mdash; these are the internal objects which represent the snapshots, directories, and files Git keeps track of
- Number of open pull requests (PRs) &mdash; a PR typically represents at least one ref, and may also drive additional work on a typical Git hosting service

### Activity

Of course, all of the [sizes](#sizes) and [quantities](#quantities) mentioned above change over time.
Those changes can dramatically alter Git performance.
In addition to rates of change in scalar values, there are other indicators of activity that drive load on Git or on a Git hosting service.

- Active users, typically expressed per unit of time &mdash; think "monthly active users"
- Pushes and fetches, again over time &mdash; these represent work the Git server has to do in order to keep clients updated
- PRs / time &mdash; in addition to the static number of PRs open, the rate at which they're opened can impact performance...
- Merges / time &mdash; ...as can the rate at which they're merged!
- API interactions / time &mdash; while vendor-specific, nearly all Git hosts have an API which interacts with Git data, and this again represents work for the service

### Race to update

The "race to update `main`" is a unique challenge that's highly exacerbated in monorepos.
It's not particularly expensive for Git but it's hugely expensive in terms of developer productivity.

When you try to push a change to your shared main branch, your commit has to have the current tip as one of its ancestors.
But if someone else has pushed their change first, then you have to fetch that change and either rebase or merge your change onto theirs.
This takes time, and in the meantime, someone else may again "win the race".

This is a Git-level bottleneck.
It doesn't matter whether your changes overlap with the other developers' changes (e.g. if you're editing the same files or not).
Merge conflicts make the "merge or rebase" part take longer, which can result in even more cases of losing the race.
In a monorepo, this race can hit developers working on entirely different projects.

### Shape

The entire "shape" of your repository's history can have an impact on performance and certain operations.
While you can't always correct old mistakes, it's worth at least understanding how they can bite you.

Git has a very simple data model at its core.
In order to make it fast and scalable, there are a number of optimizations, caches, and "tricks" living above the simple model.
Some of those optimizations and caches work best when they can assume a certain "shape" of the underlying data.

#### Deep vs shallow ancestry

The "deeper" a repo, the longer any operations will take which need to walk through history.

#### "Spiky" (many tips)

When a repo is too "spiky" – that is, it has many unmerged tips of refs – then some optimizations can't be used.
A great example is reachability bitmaps, which is a shortcut for knowing what objects are reachable from what commits.
Because they're expensive to create, reachability bitmaps don't cover every possible entrypoint to the repository.
The more ref tips you have, the less likely a given ref can take full advantage of the reachability bitmaps.

#### Merge structure

The way branches are commonly merged can affect operations need to find, for example, a common ancestor.
Perfectly linear histories (always rebase, never merge) are easy to reason about, but deepen the repo.
"Fork and join"-shaped histories (feature branches merged into main) are shallower but may be harder to process during fetches and pulls.
Criss-cross merges, where branches are merged back and forth, can really exacerbate problems finding common ancestors.

#### Deltafyable content vs non-deltafyable content

Adding text-based content tends to work _with_ the optimizations in how Git stores data, while non-text-based content tends to work against them.
More important than "text" vs "non-text" is whether content is deltafyable.
That is, when you make a small change to the logical content, does it yield a small change in the file's bytes or a large one?
Swapping one variable name in source code is highly deltafyable since most of the file is still the same.
Two zip files made from nearly-identical contents may differ wildly due to compression, so they're poorly deltafyable.

### Client/server interaction

We would be remiss not to think about bandwidth and latency between client and server.
This includes both the physical links as well as any intervening proxies, load balancers, and other equipment which may store (or even alter!) traffic on the wire.

- Latency to server &mdash; how fast can a request get there and back?
- Bandwidth to server &mdash; how much data can be transferred per unit of time?

### Developer tolerance

Believe it or not, developer tolerance for performance and complexity is a key dimension of Git scale.

- Local operations: some developers run `git status` in their prompt.
Any delay represents extreme irritation.
Other developers don't mind if `git checkout` takes 30+ seconds, so local command performance isn't worth digging into.
- Remote operations: a developer on a fast, reliable network will have far fewer problems with pushes and pulls than someone on a slow, unreliable network.
- Complexity: developers familiar with very small, low-overhead repos may find the friction of working in a large monorepo very challenging.
For example, having to work within a [sparse checkout](checkouts-too-big.md#work-with-less-data) cone may be frustrating.
For others coming from legacy version control systems with even more complexity may find the relative simplicity of a big monorepo a relief.
