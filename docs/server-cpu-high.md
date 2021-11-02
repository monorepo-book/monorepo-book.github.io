---
layout: problem
---
## Problem: high CPU usage on server

Symptoms:
- You notice high CPU usage on your GHES instance.
- Git or other operations begin to slow down unexpectedly.

### Refactor automation

- Every API call and Git operation puts load on the server.
If there are automated tools making frequent calls, consider:
  - dialing back their frequency
  - running them at off-peak hours
  - adding jitter to their start times
- For triggered work, switch from polling to webhooks.
- Redesign your automation to avoid write amplification.
For example, if every user push kicks off some kind of automated workflow which itself does a push, you've doubled (or more) the impact of each user.
The act of writing causes even more writes to be scheduled, hence each write is amplified.

### Use caution with, or don't use, shallow clones

- Shallow clones with a depth greater than 1 are computationally expensive.
Switch your workflow to use shallow clones with `--depth=1`.
- Fetching from shallow clones is also computationally expensive.
We recommend using [partial clone](https://github.blog/2020-12-21-get-up-to-speed-with-partial-clone-and-shallow-clone/#user-content-partial-clone) if you need to query the commit history.
- Unshallowing a shallow clone is also computationally expensive.
We recommend using a full clone or a partial clone in lieu of unshallowing.
