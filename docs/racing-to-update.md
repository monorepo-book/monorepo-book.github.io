---
layout: problem
---
## Problem: hard to update `main` or other branches

Symptoms:
- Frequently required to fetch/merge/push or apply changes from target branch to source branch
- Hundreds or thousands of developers working in a single monorepo

### Change your workflow

- Select a workflow such as GitHubFlow which concentrates development in topic branches.
- Use pull requests, and turn on protected branches for `main`, releases, and other shared branches.
- Use the [merge queue](https://github.com/github/roadmap/issues/272) to keep `main` clean while automating the drudgery of landing changes.
