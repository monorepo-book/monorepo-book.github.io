## Detecting problems

In a vacuum, there's nothing wrong with a "huge monorepo".
If your dev team is happy, your CI is perking along, and your infrastructure/operations team aren't complaining, then you're in good shape!
But most likely, the reason you're investigating your monorepo is because you're experiencing some kind of problem.

How can we detect a problem, determine its underlying cause, and address it?
This section is all about detection and analysis.
The two key approaches are to be aware of symptoms and to collect hard data.

### Obvious symptoms

The first part of detecting the problem is characterizing the symptoms.

- Long clone/fetch times
- Long checkout times
- Failed network operations (push/pull)
- Hard to [win the race](dimensions.md#race-to-update) to update `main`
- Unhappy IT operations team (file storage filling up, developers asking for larger SSDs on their dev machines)
- Implementation-specific failures (500s, error logs)

### Get the data

In addition to symptoms, we can look at quantitative data as well.
The premier tool for this is `git-sizer`, which will report a variety of metrics about your repo.

#### `git-sizer`

> [git-sizer](https://github.com/github/git-sizer) computes various size metrics for a local Git repository, flagging those that might cause you problems or inconvenience.
Much like this book, `git-sizer` is an encapsulation of many years of experience running Git at scale.
Unlike this book, it can offer concrete, specific pointers about _your_ repository.
If `git-sizer` flags a problem, carefully consider whether you want to reduce that dimension and/or [find a mitigation](solutions.md).

Here's a fairly large Git monorepo, run through `git-sizer` (and reformatted for readability):

| Name                         | Value     | Level of concern                 |
| ---------------------------- | --------- | -------------------------------- |
| *Overall repository size*    |           |                                  |
| Count of commits             |   485 k   | `                              ` |
| Total size of commits        |   204 MiB | `                              ` |
| Trees Count                  |  2.71 M   | `*                             ` |
| Total size of trees          |  3.84 GiB | `**                            ` |
| Total tree entries           |   109 M   | `**                            ` |
| Count of blobs               |  1.36 M   | `                              ` |
| Total size of blobs          |  19.9 GiB | `**                            ` |
| Count of annotated tags      |   109 k   | `****                          ` |
| Count of refs                |   191 k   | `*******                       ` |
|                              |           |                                  |
| *Biggest objects*            |           |                                  |
| Maximum commit size          |   236 KiB | `****                          ` |
| Maximum parents on a commit  |     3     | `                              ` |
| Maximum entries in a tree    |   112 k   | `!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!` |
| Maximum size of a blob       |  86.6 MiB | `*********                     ` |
|                              |           |                                  |
| Maximum history depth        |  58.1 k   | `                              ` |
| Maximum tag depth            |     1     | `                              ` |
|                              |           |                                  |
| *Biggest checkouts*          |           |                                  |
| Number of directories        |  23.5 k   | `***********                   ` |
| Maximum path depth           |    22     | `**                            ` |
| Maximum path length          |   257 B   | `**                            ` |
| Number of files              |   165 k   | `***                           ` |
| Total size of files          |  2.31 GiB | `**                            ` |
| Number of symlinks           |   166     | `                              ` |
| Number of submodules         |     4     | `                              ` |

Right away, we get an idea from the fingerprint on the right side that "max entries in a tree" might be a problem.
Other areas of concern include the 87MiB blob, almost 200K refs, and the 23K directories.

With symptoms and quantitative data in hand, we can turn our attention to [addressing the problems](solutions.md).
If some of the topics on this page didn't quite make sense, take a look at [dimensions](dimensions.md) to get some more insight.
