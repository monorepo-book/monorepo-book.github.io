## Why a monorepo?

If monorepos are complex and need extra care, why do some teams choose them?

1. Some teams find it reduces the number of "moving parts".
To deploy multiple repos together, some sort of outside orchestration is needed.
With a monorepo, you may be able to avoid outside orchestration.
2. It can be easier to find and share code.
If the entire "universe of code" is in one place, then you can find it easily and use it readily.
3. Making cross-cutting or API-impacting changes can be simpler.
Like the point above, if you can find all callers and callees of a change, you can change them all at one time.
4. In industry, there are highly-visible examples of successful monorepos.
These include the Windows repo at Microsoft and the all-company repos at Facebook and Google.

## Why NOT a monorepo?

For each point in a monorepo's favor, there's a counterpoint against it.
We aren't saying you should _never_ reach for a monorepo.
However, we're saying you should understand the tradeoffs and make the decision with open eyes.

> It reduces the number of moving parts.

Placing all source for all projects together _does_ mean you don't need to figure out how to orchestrate multiple repos when you want to test, deploy, or ship changes.
But this moves the problem to a different layer – something still has to decide which sources are relevant – while complicating the developer experience.

> It can be easier to find and share code.

It can also lead to unintended tight coupling that's very hard to disentangle later.
When it's so easy to `#include` any other code, there's a real risk of turning the codebase into an unstructured mess.

> Making cross-cutting or API-impacting changes can be simpler.

It's hard to actually achieve this in practice, and at best it's expensive.
Making the change in current code doesn't magically upgrade instances which are already deployed, for instance.
It also doesn't help with safe deployment, with data store migrations, with external clients, or with ensuring that all the pieces still work after making the changes.
Solving each of those problems requires additional tools and disciplined practices independent of where the source code is stored.

> There are highly-visible examples of successful monorepos.

These companies have several things in common, including that software is a core business asset for them.
Perhaps even more telling is the fraction of budget allocated to maintaining the monorepo, including infrastructure, tools, and people.
Monorepos don't maintain themselves; it takes a lot of work and thought to do so.
If the storage of source code isn't core to your business function, or if you can't budget for dedicated headcount and computing resources to maintain your monorepo, you're in for a tough time.

---

If all this hasn't scared you off, read on to learn about [Git's dimensions of scale](dimensions.md).
