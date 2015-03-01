## Branches

```bash
# Delete a branch
git branch -d BRANCH_NAME

# Delete a branch on the remote server
git push REMOTE :BRANCH_NAME
```

> NOTE: The above works to delete a remote branch by push nothing to the ref because the full
> syntax is `git push REMOTE SOURCE_REF:DESTINATION_REF`

## Pull Request Reviews

```bash
# Set branches to fetch
git remote set-branches REMOTE BRANCH_NAME
# Fetch remote
git fetch REMOTE
# Checkout remote branch
git checkout REMOTE/BRANCH_NAME
```

For example, to review a pull request from `ryan/random_changes`:

```bash
git remote set-branches ryan random_changes
git fetch ryan
git checkout ryan/random_changes
```

> NOTE: You will be in a "detached HEAD" state. This just means `HEAD` is at a remote ref and
> there is no local ref pointed to it. This is ok though because you want to review the remote ref!

## Working with Remotes
It is nice to track remote branches because then you can pull and push with simply `git pull` or
`git push`. This is beneficial because then you will only ever pull or push to the branch you are
on so accidentally doing `git pull origin develop` while you're on master!

**WARNING**: Using just `git push` by default in git earlier than v2.0 will push ALL local branches
to ALL remote branches. This can be changed by setting `push.default = simple` which is set in our
git config so make sure you've copied the most recent version. Simple mode will push only the current
branch to its remote tracking branch and only if it has a remote tracking branch.

```bash
# When pushing a branch for the first time.
git push -u REMOTE

# To pull the remote branch in your local one
git pull

# To push the local branch to the remote
git push

# To pull and rebase (see notes on rebasing below)
git pull --rebase
```

## Rebasing

### What is Rebasing?
Rebasing can be considered the core function of git. Almost everything you can do with git
can be accomplished in some form with the rebase command. It seems complicated but is actually
quite simple. Imagine you have the following:

```
     A---B---C random_changes (HEAD)
    /
D---E---F---G---H develop
```
The branch `random_changes` includes commits `D`, `E`, `A`, `B`, `C` and the branch `develop` includes
commits `D`, `E`, `F`, `G`, `H`.

`git merge develop` would do something like:

```
     A---B---C---M random_changes (HEAD)
    /           /
D---E---F---G---H develop
```
The branch `random_changes` now includes all commits and a new merge commit `M`.

`git rebase develop` would do something like:

```
                 A'---B'---C' random_changes (HEAD)
                /
D---E---F---G---H develop
```
The branch `random_changes` now includes all commits from `develop` like when merging, however, now
instead of `A`, `B`, and `C`, it has `A'`, `B'`, and `C'`. These are new commits which make the same
changes however they are now rooted at commit `H` instead of commit `E`. Git essentially goes through
your commits and "replays" them from a new position.

If a conflict occurs while rebasing, it will pause and allow you to correct the conflict. Once it is
corrected, you add the files using `git add` then commit the correction using `git commit --amend`. Then
you continue rebasing by using `git rebase --continue`.

If a conflict occurs and you don't want to deal with it now or want to say fuck it and just merge and deal
with the merge conflict after the fact, simply do

```bash
git rebase --abort
```

### Why?
Rebasing accomplishes much the same as merging, so why use one or the other? Rebasing avoids having
merge commits littered through out the history so you don't have `develop` being merged into a topic
branch a ton of times before merging it back into `develop` which leads to a messy history. However,
it does this at the cost of re-writing history.

You should never rebase commits which have been pushed to a shared remote because that will cause
huge headaches for anyone who has pulled the old code if you re-wrote the history.

Rebasing is useful in a few cases though:

1. Say you just made a quick commit to develop, but you forgot to pull before committing. If you
were to pull now, you'd cause a recursive merge. This isn't a bad thing but in an ideal world, pulling
a remote tracking branch should always be fast-forward. You could reset, pull, and then redo your commit,
but that's a lot of work. Why not let rebase do essentially that for you! You can `git pull --rebase`
and it will replay your commit on top of the remote like you had pulled before committing!

1. Changing local history. Using rebase's interactive mode (via `git rebase -i`). This will allow
you to squash commits into other commits and combine their changes into one, pause and allow you to
edit a commit, reorder the commits, remove commits, and more. This can be useful to clean up your
local history before pushing to a remote branch.

## Tagging

```bash
# Add a tag
git tag TAG

# Push tags
git push origin --tags

# Delete a tag
git tag -d TAG

# Delete a tag on the remote server
git push origin :TAG

# List tags in version order
# NOTE: This is an alias. See /sites/ycharts/confs/developers/git_config
git vtag
```

## Reverting Stuff

```bash
# Amend last commit (change message, etc.)
git commit --amend

# Undo last commit and redo
git reset --soft HEAD^
git commit -c ORIG_HEAD

# Undo merge (when a conflict occurs, before committing the merge)
git reset --merge
```

## Creating a github mirror of a svn codebase

First, Go to Github and create `PROJECT_NAME`

```bash
git svn clone -s SVN_REPO_URL LOCAL_DIR
cd LOCAL_DIR
git remote add origin git@github.com:ycharts/PROJECT_NAME.git repo_dir
git push origin master
```

## Making a symbolic link from virtualenv to working git repo

```bash
cd ~/.virtualenvs/ycharts/src
ln -s -f /sites/project project
```
