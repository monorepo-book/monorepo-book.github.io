# Working with Git at scale

In previous sections, we talked about specific tools and optimizations for working with monorepos.
It's worth zooming out and looking at the ways you and your devleopers interact with Git.
Choices you make about branching model, forking, and integration with other tools can have a large impact on developer productivity with your monorepo.

## Branching model

The branching model dictates where new work, deployed or shipped work, and maintenance work happens in your repository.
Well-known examples include [git-flow][gitflow], [GitHub Flow][githubflow], and [trunk-based development][trunkbased].
More complex options are also possible in Git, but our guidance is to keep your model as simple as possible.
In a monorepo, every team should be on the same branching model (or else it will be quite challenging to determine where changes should be made).

[gitflow]: https://nvie.com/posts/a-successful-git-branching-model/
[githubflow]: https://docs.github.com/en/get-started/quickstart/github-flow
[trunkbased]: https://trunkbaseddevelopment.com/

## Forking

The [GitHub docs][forksdoc] currently say:
> A fork is a new repository that shares code and visibility settings with the original “upstream” repository. Forks are often used to iterate on ideas or changes before they are proposed back to the upstream repository, such as in open source projects or when a user does not have write access to the upstream repository.

Forks act as a trust boundary, clearly delineating that known parties have direct access to add to the repository.
Unknown or less-known parties have no direct access, which helps thwart low-quality and malicious contributions.
This introduces (intentional) additional friction in the process of landing a change in a repository.

In a corporate software development setting, especially with a monorepo, it's unlikely that developers lack write access to upstream.
Also, the trust boundary is different – there are other tools to deal with low-quality and malicious contributions.
While the choice is ultimately yours, we recommend having developers work out of feature branches rather than forks in a monorepo.

[forksdoc]: https://docs.github.com/en/get-started/quickstart/fork-a-repo

## Integration with other tools

Finally, don't forget about the other tools in your development workflow.
Make sure that your CI/CD systems use appropriate settings (or else they might suffer from [slow clones](clones-too-slow.md)).
Make sure that your issue tracker handles multiple semi-independent streams of work in a single repository.
