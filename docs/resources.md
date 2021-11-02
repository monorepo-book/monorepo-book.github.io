## Additional resources

### Blog posts

- [Commits are snapshots, not diffs](https://github.blog/2020-12-17-commits-are-snapshots-not-diffs/)
- [Git clone: a data-driven study on cloning behaviors](https://github.blog/2020-12-22-git-clone-a-data-driven-study-on-cloning-behaviors/)
- [Get up to speed with partial clone](https://github.blog/2020-12-21-get-up-to-speed-with-partial-clone-and-shallow-clone/)
- [Bring your monorepo down to size with sparse checkout](https://github.blog/2020-01-17-bring-your-monorepo-down-to-size-with-sparse-checkout/)
- [A deep dive into Git performance using Trace2](https://devblogs.microsoft.com/devops/a-deep-dive-into-git-performance-using-trace2/)

### Tools

- [git-sizer](https://github.com/github/git-sizer) (as mentioned in the [detection](detection.md) section)

### Git troubleshooting

If you ever want to see what's happening under the covers with Git, you're likely to want one or more of the following environment variables:
- [`GIT_TRACE2=1`](https://devblogs.microsoft.com/devops/a-deep-dive-into-git-performance-using-trace2/) for deep tracing
  - `GIT_TRACE2_PERF` offers a nice, table-oriented format which is easy to read
- [`GIT_CURL_VERBOSE=1`](https://git-scm.com/book/en/v2/Git-Internals-Environment-Variables#_networking) for network operations
- [`GIT_TRACE_PACKET=1`](https://git-scm.com/book/en/v2/Git-Internals-Environment-Variables#_debugging) for packet-level tracing
- [`GIT_SSH_COMMAND="ssh -vvv"`](https://git-scm.com/docs/git-config#Documentation/git-config.txt-coresshCommand) for SSH tracing (you can use 1, 2, or 3 `v`s depending on how verbose you want the output)
