---
title: Maintaining a Monorepo
layout: default
---
## Congratulations, you own a monorepo!

Does this describe you?
It seems like only yesterday you were `git init`ing a new project.
Today, it takes 5 minutes to grab a fresh clone and 2 seconds to run `git status`.

> Repositories never get smaller.

The above quote is so often heard in the virtual hallways of GitHub that I don't know who to attribute it to.
Feels right, though, doesn't it?
Git holds your project's history, and history only grows.

At a certain size, scale, and use pattern, your cute little repository became a **monorepo**.
And you, dear maintainer, may need to take some steps to keep it fast and friendly.
This site (or "book" as I've been calling it) will help you understand the nature of monorepos and Git scaling, how to troubleshoot common problems, and generally how to feed and care for your monorepo.

### Contents

- What's a monorepo? (below)
- [When to reach for a monorepo](why-monorepo.md)
- [Git's dimensions of scale](dimensions.md)
- [How to detect and analyze scale problems](detection.md)
- [How to resolve scale problems](solutions.md)
- [Appendix: Glossary](glossary.md)
- [Appendix: Additional resources](resources.md)

### What's a monorepo?

[Wikipedia](https://en.wikipedia.org/wiki/Monorepo) says:
> In version control systems, a monorepo [...] is a software development strategy where code for many projects is stored in the same repository.
> As of 2017, various forms of this software engineering practice were over two decades old, but the general concept had only recently been named.
> Many attempts have been made to differentiate between monolithic applications and other, newer forms of monorepos.

At GitHub, we tend to think of a monorepo as having one or more of:
- large amounts of code
- multiple discrete projects
- lots of users

If you're still in the decision-making stage, you should read up on [when to choose a monorepo](why-monorepo.md).
If you're already dealing with one, you could skip ahead to learn about the [dimensions of Git scale](dimensions.md).

### Acknowledgements and thanks

This project began as a way to stop repeating advice that I was giving to nearly every customer interested in a monorepo.
Along the way, I realized that there are many, _many_ others at GitHub and in the community who also have hard-won knowledge to contribute.
It's impossible to estimate how many collective years of deep knowledge are reflected here, but it's a pretty large number!

Thanks to the various contributors who've improved this walkthrough since its inception as "the Book of Monorepo".
In alphabetical order by GitHub handle, they include:
- [@bk2204](https://github.com/bk2204)
- [@derrickstolee](https://github.com/derrickstolee)
- [@dkunkler](https://github.com/dkunkler)
- [@larsxschneider](https://github.com/larsxschneider)
- [@mhagger](https://github.com/mhagger)
- [@primetheus](https://github.com/primetheus)
- [@terrorobe](https://github.com/terrorobe)
- [@tgummerer](https://github.com/tgummerer)
- [@ttaylorr](https://github.com/ttaylorr)
- ...your name next?

All the stuff that's right is from these folks 👆 but [I'll](https://github.com/vtbassmatt) claim the errors.
The book represents the distillate of many person-years of collected wisdom and experience from a variety of people at GitHub, Microsoft, and beyond.
It's not perfect or complete, though, and [we welcome _your_ contributions](https://github.com/monorepo-book/monorepo-book.github.io)!

This work © GitHub, Inc. and contributors.
