==================================================
  How to use Git Rebase to contribute to Kworkflow
==================================================

Knowing how to use `git rebase` can be very useful when contributing to KW.
We will explore practical two scenarios to illustrate how to use it and why.

Scenario 1: Sync local with fork
--------------------------------

Suppose you created a branch called `feature` from commit `a1a1a1` which was the
current HEAD of the `unstable` branch. You added two or  more  commits  in  your
local branch. You create a PR to merge your  branch  onto  `unstable`,  but  now
unstable has many more commits: `a2a2a2`, `a3a3a3,`, ..., `a8a8a8`, and  so  on.
You have to rebase your local branch to be synchronized with unstable.

First, make sure your local version of unstable matches KW's  unstable.  If  you
forked it on GitHub, go to your GitHub fork, select the  `unstable`  branch  and
click on the `synchronize` button. After that, you rebase your commit:

```
git rebase -i `a1a1a1`
```

Where `a1a1a1` is the sha of the last commit of the `unstable` branch before you
created a new branch and started making commits from there.  This command will
open your default editor with the commits you want to rebase:

```
pick a1a1a1 <commit message for a1a1a1>
pick b1b1b1 <commit message for b1b1b1>
pick b2b2b2 <commit message for b2b2b2>
```

Change `pick` to `edit` in the first line, which corresponds to commit `a1a1a1`.
After saving and closing the file, git will enter rebase mode. From there, you
can run:

```bash
git merge unstable
```

It will merge the local updated copy of `unstable` branch  using  `fast-forward`
strategy, meaning you don't have to solve merge conflicts. After that,  you  can
continue the rebase:

```bash
git rebase --continue
```

It is possible that the new commits from `unstable` change some files which were
also change by your commits  `b1b1b1`  and  `b2b2b2`.  That  could  cause  merge
conflicts, but, if that is the case (most times, it won't), `git` will tell  you
that and ask you to manually fix the conflict. After fixing  the  conflict,  you
can add the conflicted files to the staging area  an  run  `git  commit`,  which
should open the editor with the previous  commit  message  before  there  was  a
conflict.

Scenario 2: fixing bug or codestyle
-----------------------------------

Suppose you created a PR with two or more commits, but that caused some  bug  or
maybe violated codestyle guidelines in  the  first  commit.  You  realized  your
mistake and go fix it by creating a third or fourth commit which fixes a bug  or
codestyle violation in the first commit. That's the wrong way  of  doing  things
and you PR won't be accepted till you do things right.

To solve it, you have to rebase your code:

```bash
git rebase -i <commit>
```

Replace `<commit>` with the sha  of  the  parent  commit  of  the  commit  which
introduced the bug. Yes, it is the parent commit of the buggy  commit,  not  the
commit itself. Golden Tip: use the syntax `HEAD~n` in place of `<commit>` to  go
back `n` commits ago; for example, `HEAD~3` to correct the third-to-last  commit
which introduced the bug.

After opening your editor, change  `pick`  to  `edit`  for  every  commit  which
introduced a bug or codestyle violation. Then, save and exit.  You  are  now  in
rebase mode. Edit the files and fix whatever has to be fixed. Add these files to
the staging area and commit them using amend:

```bash
git commit --amend
```

If you do not wish to edit the commit message, you can add the flag `--no-edit`:

```bash
git commit --amend --no-edit
```

Continue the rebase:

```bash
git rebase --continue
```

If you select `edit` just for only one commit, you are done and  the  rebase  is
completed. If you selected `edit` for multiple  commits,  after  continuing  the
rebase, git will jump to the next commit which you set  to  `edit`.  Repeat  the
steps above,  fixing  and  changing  things  as  needed,  commiting  them  using
`--amend` (in order to amend them in the same commit, otherwise it would  create
a new commit), and continuing the rebase.

Conclusion
----------

You have successfully learned how to use the `git rebase` command to apply fixes
and synchronize branches. Good job!