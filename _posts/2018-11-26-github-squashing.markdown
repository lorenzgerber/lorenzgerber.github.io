---
layout: post
title:  "Github Squashing"
date:   2018-11-11 09:34:28 +0800
categories: github squash
---

Github squashing is used to squash multiple commits into one. This can be useful when after committing errors are found and addiontal commits are needed. Especially when working for a Pull Request, it would be nice to 'beautify' the contribution into a single consistent commit.

Squashing has to be done interactively using a text editor with the `rebase -i` command. As an example, squashing 2 commits up to HEAD would be initiated with:
```
git rebase -i HEAD~2 
```
Now a texteditor opens that could look for example as follows:
```
pick eedba5e changed instance type in generic version
pick 7c65581 test commit

# Rebase b3c2063..7c65581 onto b3c2063 (2 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
#       However, if you remove everything, the rebase will be aborted.
#
#       
# Note that empty commits are commented out
```
The uppermost commit is the one that shall be kept. Hence, nothing is changed on that line while before the commit id `7c65581`, the word `pick` is changed to either `squash` or `fixup`. The prior will concatenate the commit messages while the latter will use only the first one, here from `eedba5e`.

In case the commits have already been pushed to remote, it's generally a no-go to squash. However, if you are sure that nobody has pulled the affected commits (quite likely when working on pull request), squash with a forced push to remote can be conducted as follows:

```
git rebase -i origin/master~2 master
git push origin +master
```
The difference between --force and + is described nicely in a [Stack Overflow post][stack-overflow-post] which was also the reference for this blog entry.



In case using --force  is no option, another solution is to `merge --squash` all changes into a new branch that has been created from the most recent commit of where the feature/bugfix shall be merged to finally (master/develop):

```
git checkout -b new_branch
git merge --squash old_branch
git commit
```
After this, a pull request can be done on `new_branch`.

Merge Squash as described in [Stack Overflow][stack-overflow-post-merge-squash]


[stack-overflow-post-merge-squash]: https://stackoverflow.com/questions/5308816/how-to-use-git-merge-squash
[stack-overflow-post]: https://stackoverflow.com/questions/5667884/how-to-squash-commits-in-git-after-they-have-been-pushed
