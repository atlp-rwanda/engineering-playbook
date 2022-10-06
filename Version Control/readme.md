# Version Control

> We always use source code control. It's like a time machine. We can work in parallel universes of our source code, experimenting without fear of losing work, rolling back if something goes wrong.

We use [Git](https://git-scm.com/) as our defacto software versioning system (SVS), and [Github](https://github.com) for our hosting.

## Git Workflow

We use [Gitflow Workflow as documented here](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow), with [our own conventions](../Conventions) on branch naming, commit messages and PR format.

## Advanced Git Features

> TBD - this is work in progress, submit a PR for suggestions on this

Our guidelines around:

- `Pre-commit` hooks
- [When and how to `rebase` feature branches](#rebasing)
- Release management (release branches, etc)
- [Tagging](https://git-scm.com/book/en/v2/Git-Basics-Tagging)
- [When and how to `cherry-pick`](#cherry-picking)
- [When and how to do a hot fix](#hot-fixing)

## Github - Pivotal Tracker/Trello Integration

Both Trello and Pivotal Tracker have very nice integrations with Github [that is explained in detail here](https://www.pivotaltracker.com/integrations/GitHub/) and [here](https://blog.trello.com/github-and-trello-integrate-your-commits). With this, you can:

- Automatically attach pull requests, branches, and commits to a PT story.
- You can still connect one PT board to multiple repositories. Very useful especially when working with n-tier and microservice architectures where one project will have multiple repos.

### Rebasing

> Rebasing is taking all your branch's commits and adding them on top of later commits that were added to the branch you created yours from. This is useful when say you created your feature branch from `develop` and as you were working, your team members completed theirs and their pr's were merged. Rebasing will make sure your commits are added after theirs on your feature branch and `develop`.

Steps:

- Ensure you have the most up-to-date version of the branch you're rebasing on (we'll assume `develop` in this case)
  - `git checkout develop`
  - `git pull`
- Checkout to your feature branch (whichever branch you're working with)
  - `git checkout ft-user-authentication-10000000`
- Run the rebase
  - `git rebase develop`

Assuming no conflicts were encountered, the rebase is completed.

#### Handling Rebase Conflicts

> Not all merges go smoothly

If you've dealt with [merge conflicts](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/) before, rebase conflicts are handled essentially the same way. During the rebase, git adds each commit onto the new base one by one. If it reaches a commit with a conflict, it'll pause the rebase and resume once it's fixed. Here's what to do:

- Run git status to find out where the conflicts are
  - `git status`
- [Fix the conflict](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/#competing-line-change-merge-conflicts)
- Continue the rebase
  - `git rebase --continue`
- It'll pause for any more conflicts, and once they're set you just need to force push to update your remote
  - `git push --force-with-lease`
  - Read more about `--force-with-lease` [here](https://developer.atlassian.com/blog/2015/04/force-with-lease/)

[Read more](https://git-scm.com/docs/git-rebase) about git rebase.

### Cherry-picking

> Given one or more existing commits, apply the change each one introduces, recording a new commit for each

What `git cherry-pick` does, basically, is take a commit from somewhere else, and "play it back" wherever you are right now. Because this introduces the same change with a different parent, Git builds a new commit with a different ID. When you git cherry-pick a commit, only the change associated with that commit is re-applied to the working tree. Let's see how:

- Make sure the branch with the foreign commit is updated (we'll use `bg-cache-invalidation-10000000`).
  - `git pull bg-cache-invalidation-10000000`
- Get the commit `HASH` of the commit you want to apply to the branch
  - Run git log
    - `git log`
  - Scroll through the commits and find the commit `HASH` (at least the first 5 characters) of the commit you want to apply to your branch
  - Copy it somewhere (or just to your clipboard)
- Switch to the branch you want to apply the commit to (we'll use master)
  - `git checkout master`
- Cherry Pick the commit (replace `HASH` with what you copied)
  - `git cherry-pick HASH`

`master` should now have the changes introduced by the commit `HASH` from `bg-cache-invalidation-10000000`

### Hot fixing

> Bugs do crawl

Cases may arise where you need quickly apply a fix to your production (or any) environment. Steps you could take:

- Branch from where you want to apply the fix (we'll use master)
  - `git checkout master`
  - `git branch bg-fix-memory-leak-1000000`
  - `git checkout bg-fix-memory-leak-1000000`
- Work on a patch for the bug and commit it
- Use the [Cherry Pick](#cherry-pick) instructions above to apply the commit to your `master` branch

Your now left with a working `master` but all your other branches are outdated. Let's see how we would go about updating `develop` (and probably all others) without breaking anything:

- If you're using a branch, say `develop`, to create all your other feature branches you'll want to apply the fix to develop first. Here we'll rebase, but what will actually happen is Git will try to combine the snapshots of both HEAD commits into a new snapshot. If a portion of code or a file is identical in both snapshots (i.e. because a commit was already cherry-picked), Git won't touch it.
- Hence all we need do is follow [rebasing](#Rebasing) instructions. Only in this case, we're first updating develop:
  - `git checkout develop`
  - `git rebase master`
- We can then alert the whole team to update their branches:
  - `git checkout ft-user-authorisation`
  - `git fetch`
  - `git rebase develop`
