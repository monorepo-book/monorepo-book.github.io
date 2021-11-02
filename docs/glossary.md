# Glossary

_This glossary is a vast simplification of everything Git can do._
_It's intended to give you a working vocabulary for understanding this book._
_Particularly lacking are the definitions of `index` and `packfile`._
_Interested readers should dive into the details in [Chapter 10 of Pro Git, "Git Internals"](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain)._

## Blob

Git stores the files it tracks in structures called "blobs".
A blob consists of the length of the file followed by the file contents themselves.
"Loose blobs" are blobs written out to disk after being compressed.
In practice, blobs are usually part of a [packfile](#packfile) rather than being loose on disk.

## Commit

Git stores snapshots of all your content in structures called "commits".
A commit is a text file which lists some metadata like who created the commit, a message about the commit, optionally one or more parents (predecessor commits), and the [tree](#tree) which represents the root directory being tracked.
Commits can be "loose" and written out to disk like a [blob](#blob), but more commonly are found as part of a [packfile](#packfile).

## Index

The index represents Git's knowledge of state as you make changes in your [working tree](#working-tree).
A file which has changes in the working tree but not in the index is said to be "not staged".
A file which has changes in the working tree and has been added to the index is said to be "staged".
Once you create a commit, files in the working tree match their tracked state in the index, and your working directory is said to be "clean".

## LFS

LFS is the Large File Storage system.
Git was not intended to track "large" files (say, 5MB+), binary files (which don't [deltafy](#packfile) well), and other non-plaintext formats.
However, in the course of building software, we often need to store such files in or near a repository.

LFS solves the problem by keeping the contents of the file outside the repository.
Through some clever tricks, LFS replaces the large or binary file with a small, plaintext file "just in time" to have it committed to the repository.
That small plaintext file informs LFS how to retrieve the full contents of the file later.
Through the same clever tricks, the real contents get placed in the [working tree](#working-tree) "just in time" when an LFS file is checked out.

## Object database

Git objects (commits, trees, blobs, and annotated tags) are stored in the **object database**.
This key-value store allows accessing object via a hash of their contents.
In your local repository, the object database consists of the files contained within `.git/objects`.
Objects are stored in two forms: loose and packed.
The loose objects have a relationship of one object per file, and are located at `.git/objects/<X>/<Y>` where `<X>` is the first two hex digits of the hash and `<Y>` is the rest of the hash as a hex string.
[Packfiles](#Packfile) store multiple objects per file and are stored in `.git/objects/pack`.

## Packfile

Loose Git objects are compressed before being written to disk, but this isn't the maximum amount of space savings possible.
A packfile is a Git structure which takes multiple logically-separate objects and "packs" them together into a single file.

Along the way, Git applies compression to the objects in packfiles.
This is known as "deltafying" the content.
It's not the same as storing diffs of files as they change over time the way some version control systems do.
But, it's the same insight that many files have similarities and can be stored more efficiently by not repeating the same content.

## Ref

Git tracks important places in the history of a repository using a structure called a "ref".
The two most common kinds of refs are branches and tags.
Branches are names which track (point to) the latest in a series of commits, such as `main` or `my-feature`.
Tags are names which point to particular important commits without moving, such as `release-1.0.0` or `big-refactor`.

## Tree

Git stores directories it tracks in structures called "trees".
A tree is a text file that lists files, their permissions, and the IDs of the blobs which contain the contents of the files.
"Loose trees" are written out to disk much like loose [blobs](#blob).
But also like blobs, trees are more typically found in [packfiles](#packfile).

## Working tree

The working tree (or sometimes workdir) is the directory where you check out files from Git and operate on them (e.g. make edits, run builds, and create new commits).
