In git, there are two main ways to undo commits, `reset` and `revert`. `reset` will undo commits as
if they never happened. `revert` will undo changes and create a new commit without changing history.

If you have not pushed changes, you will almost always want `reset` because there is no need to clutter
up your history with revert commits. If you have pushed the changes that you want to reset, you will need
to decide if you are able to change history. Changing the history of pushed changes can cause complications
if anyone else has pulled the changes that you are now altering. If you can't change history, then you will
need to use `revert`.

## Reset

### Resetting commits

Suppose you have the following commits and status:

```
A---B---C develop
```

```
$ git status
On branch develop
Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   path/to/a/file.txt
```

Now, suppose you commit that change with `git commit -am "Changes"`, then your tree looks like:

```
A---B---C---D develop
```

```
$ git status
On branch develop
nothing to commit, working directory clean
```

If you want to reset back to `C`, you have three options:

1. `git reset --hard C`
1. `git reset C` OR `git reset --mixed C`
1. `git reset --soft C`

The tree will look like `A---B---C develop` after all of them, but the status will look different:

#### --hard
`--hard` will throw away the changes as if they were never made!

```
$ git reset --hard C
$ git status
On branch develop
nothing to commit, working directory clean
```

#### --mixed
`--mixed` or with no option will unstage the changes from git but keep them on disk

```
$ git reset C
$ git status
On branch develop
Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   path/to/a/file.txt
```

#### --soft
`--soft` will leave the changes staged and `git commit -m "Changes"` will redo the commit.

```
$ git reset --soft C
$ git status
On branch develop
Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)

        modified:   path/to/a/file.txt
```

### Resetting merges
Suppose you accidentally merged and want to `reset`.

```
     A---B---C random_changes
    /         \
D---E---F---G---M develop (HEAD)
```

To reset a merge, it's the same as resetting a commit! If you want to reset the merge and put `develop`
back to how it was before merging `random_changes` in, simply do `git reset G`.

#### --merge
If you attempt a merge, but a conflict occurs and you don't want to deal with it and want to just not merge,
you git do

```
git reset --merge
```
which will reset you back to the state before you attempted the merge!

## Revert

### Reverting commits
`revert` is pretty simple, it will just reverse the changes made in the specified commit and create
a new commit with the opposite changes. So if you added a line, reverting that commit will remove that line.

```
A---B---C---D develop (HEAD)
```

becomes

```
A---B---C---D---D' develop (HEAD)
```

### Reverting merges
Reverting a merge is bit more complicated. You must specify the parent of the mainline branch. Usually it will
be 1. So you need to do

```
git revert -m 1 M
```

```
     A---B---C random_changes
    /         \
D---E---F---G---M develop (HEAD)
```

becomes

```
     A---B---C random_changes
    /         \
D---E---F---G---M---M' develop (HEAD)
```

This says that you do not want any of the changes that were merged so any future merges won't include commits
in the reverted merge! This is because those commits are already merged even though the changes they introduced
have been reverted. In order to reintroduce those changes, you will need to revert the revert commit.

```
git revert M'
```
