## Fixing problems

Having identified the problem, we can explore options available to fix it.
This is a list of potential problems you might see.
Drilling into the page for that problem, you'll get a list of potential solutions.
(If you're following along with the `git-sizer` example from the [previous page](detection.md), try "Checkouts are too big" below.)

_Note:_ For most of these problems, another possible solution is "split up the monorepo".
We'll assume that's not on the table, since you're looking into improving monorepo performance 😀

- [Clones are too slow](clones-too-slow.md)
- [Fetches are too slow](fetches-too-slow.md)
- [Pushes are too slow](pushes-too-slow.md)
- [Checkouts are too big or slow](checkouts-too-big.md)
- [Local Git commands are slow](client-commands-slow.md)
- [Losing the race to update](racing-to-update.md)
- [Server maintenance fails or takes too long](maintenance-failure.md)
- [Server CPU usage is high](server-cpu-high.md)

If you aren't sure which of these is your problem, try using the [tools and methods](detection.md) mentioned elsewhere.
After you've resolved any detected problems, take a wider view of your [workflows](workflows.md) to make sure they're compatible with monorepos.
Also, be sure to check out the additional [resources](resources.md) we've linked to.
