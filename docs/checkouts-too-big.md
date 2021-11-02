---
layout: problem
---
## Problem: checkouts are too big or too slow

Symptoms:
- Developers or CI machines deal with huge working directories (and don't need all the content)
- When switching branches, Git appears to hang for a while
- `git-sizer` indicates large checkout sizes

### Tell Git you've got a big monorepo

- Turn on [`feature.manyFiles`](https://git-scm.com/docs/git-config#Documentation/git-config.txt-featuremanyFiles).

### Work with less data

- Consider [sparse checkouts](https://github.blog/2020-01-17-bring-your-monorepo-down-to-size-with-sparse-checkout/).
- Consider composing the monorepo from sub-repos using `git submodule`.

### Optimize your hardware and software

- Turn off virus scanning for your working directory.
- Upgrade developer workstations to fast SSDs.
- Once you've upgraded to SSDs, set [`checkout.workers`](https://git-scm.com/docs/git-config#Documentation/git-config.txt-checkoutworkers) to a negative value in order to speed things up.
