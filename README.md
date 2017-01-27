# Get-the-most-out-of-Git
This is a demo repository which contains some useful information about Git commands.


## Scenarios

### Setting up the initial scenario

Check the status of the repository

`git status`

Create a new file

`echo 'Hello world!' > Hello.txt`

Add the file

`git add Hello.txt`

Stage the changes

`git commit -m "My first file added"`

First change. Add your name and see the differences.

```
vim Hello.txt
git diff
git add Hello.txt
git diff --cached
git comment -m "Name added"
git status
```

Second change. Add your email address.

```
vim Hello.txt
git add Hello.txt
git comment -m "Email address added"
```
View the history of the project

```
git log
git shortlog
git log --since=7.days
```

View history of one file

```
git log --follow -p [filename]
git log -- [filename]
gitk [filename]
```

We can checkout any commit and create a branch from it

```
git checkout <hash>
git checkout -b temp
```

Return the latest version in the master branch

`git checkout master`

Let's call the first version of the file version 1.0.0  The release is now done, and tagged for future reference.

`git tag 1.0.0`

Tag the previous version

`git checkout 1.0.0~`

See the previous version of the file

`cat Hello.txt`

Let's make it 1.0.0-beta

`git tag 1.0.0-beta`

Checking out by tag name

```
git checkout 1.0.0
git checkout 1.0.0-beta
git checkout 1.0.0
```
View all the tags

`git tags`

---

## Undoing / reverting changes

### Reverting the changes in the working directory
Sometimes you have modified a file in your local working directory and you wish to just revert to what has already been committed.

```
git checkout master
[Change the file]
git status
git checkout Hello.txt
```

### Undoing staged changes (before committing)

The `reset` command resets the staging area to be whatever is in HEAD. This clears the staging area of the change we just staged.

```
[Modify the Hello.txt file to have a bad comment and then go ahead and stage it]
git add Hello.txt
git reset Hello.txt
[Checkout the committed version to clean the working directory]
git checkout Hello.txt
```
`Tip`: to undo the reset, run `git reset HEAD@{1}`

### How to revert changes that have been committed to a local repository

Sometimes you realized that a change that you have already committed was not correct and you wish to undo that commit. There are several ways of handling that issue, and the way I am going to show you is always safe.
Essentially we will undo the commit by creating a new commit that reverses the unwanted changes.

```
[Edit Hello.txt and add "# This is an unwanted but committed change"]
[Add it to the staging area and commit it.]
git add Hello.txt
git commit -m "Oops, we didn't want this commit"
git revert HEAD [--no-edit]
```

Since we were undoing the very last commit we made, we were able to use HEAD as the argument to revert. We can revert any arbitrary commit earlier in history by simply specifying its hash value.

### Removing commits from a branch

Ugly fact:   both the original commit and the "undoing" commit are visible in the branch history (using the git log command). Let's remove them using reset.

`git reset --hard <hash>`

The `--hard` parameter indicates that the working directory should be updated to be consistent with the new branch head.


### Amending commits

```
git add Hello.txt
git commit -m "X"
```

Oops! Forgot to include anything...

```
[Include anything that you forgot]
git add Hello.txt
git commit --amend -m "Commit message ammended"
```

### Squash those two latest commits into one

```
git reset --soft HEAD~2
git commit -m "Two latest commits squashed"
```

---

## Cherry pick scenario

`cherry-pick --no-commit` lets you inspect (and modify) the result before committing:

```
1. Stage new file
2. Commit
3. Stage new intentional bug
4. Commit
5. Fix unintentional and commit it
6. git cherry-pick --no-commit <hash>
```

---

## Rebase vs Merge

### What's the difference between rebase and merge?

**Rebase** helps to **cut up commits and slice them** into any way that you want them served up, and placed exactly where you want them. You can actually rewrite history with this command, be it reordering commits, squashing them into bigger ones.

### When to Rebase, when to Merge?

Don’t use rebase if the branch is public and shared with others. Rewriting publicly shared branches will tend to screw up other members of the team.
When the exact history of the commit branch is important (since rebase rewrites the commit history).
Summarizing, use rebase for short-lived, local branches and merge for branches in the public repository.

### Rebasing without conflicts.

```
git checkout -b newfeature
Switched to a new branch "newfeature"

[.. changed some files, made a few commits ..]

git checkout master
Switched to branch "master"

[.. changed one file, committed ..]
```

So right now, our history looks like this:

![alt text](http://gitready.com/images/rebase1.png "Logo Title Text 1")

Now I want to bring the changes back into the master branch. Now, from here I could merge my changes in with `git merge newfeature`. If I did that, here’s how the commit history would look:

![alt text](http://gitready.com/images/rebase2.png "Logo Title Text 1")

If we did a git rebase newfeature instead, our commit history would look like:

```
git rebase newfeature
git push master
git branch -d newfeature
```
![alt text](http://gitready.com/images/rebase3.png "Logo Title Text 1")

### Rebasing and resolving the conflicts.

Create a new branch

`git checkout -b newfeature`

Add a new file with some content

```
vim Hello.txt (add a new line)
git add .
git commit -m "Starting to develop a new feature"
```

Remove some lines to generate a conflict

```
vim Hello.txt
# Remove some lines
git add .
git commit -m "Removed some lines"
```

Remove some lines to generate a conflict

```
vim Hello.txt
# Remove some lines
git add .
git commit -m "Removed some more lines"
```

Switch to master

`git checkout master`

Add a new line around the middle

```
vim Hello.txt (add a new line around the middle)
git add .
git commit -m "New line added"
```
Rebase

`git rebase newfeature`

(You should see a merge conflict error once you hae done this step).

View diffs

`git difftool --tool=meld`

Resolve the conflicts

Mark the conflicts as resolved

`git add Hello.txt`

Continue with the Rebase process

`git rebase --continue`
