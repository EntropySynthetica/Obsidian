# Git Cheat Sheet
Created Friday 17 February 2017

#git

## Install latest version of git on Ubuntu

```bash
sudo add-apt-repository ppa:git-core/ppa
sudo apt update
sudo apt install git
```

Verify with

`git --version`

## Common Examples

Download a remote git repo to local dir.
`git clone <github https>`

Start Git repo in a directory
`git init`

Add filename to git repo
`git add <filename>`

Check status of git repo
`git status`

Commit changes to local repo
`git commit -m 'i did this'`

Ammed Commit notes.
`git commit --ammend -m 'I actually did this'`	 

Verify what remote git site git will backup to. 
`git remote -v`

Delete a branch in remote repo. 
`git push origin --delete <branch>`	

View Commit History
`git log`

more succinct output
`git log --oneline`

with a visual graph of branches
`git log --graph`

Track renamed file
`git mv file_from file_to`					

To set your identity:
`git config user.name "Jane Doe"`
`git config user.email "myworkemail@address.com"`

Create a new Branch
`git checkout -b <branch>`

Delete a Branch
`git branch -d <branch>`

Show all Branches
`git branch`

Show all Remote Branches
`git branch -r`

Pull all Branches 
`git pull --all`

Remove all changes since last commit. 
`git reset --hard HEAD`

Add more changes to last commit
`git commit --amend`

Rewind back n commits
`git reset --hard HEAD~n`        # n is the last n commits

or to a specific commit
`git reset --hard <commit-sha>`

See the differences in your staged (or unstaged) changes
`git diff --staged` # for staged changes
`git diff` # for unstaged changes

Check that you can connect via SSH to git repo
`ssh -vT git@github.com`

Change the git remote location
`git remote set-url origin https://github.com/USERNAME/REPOSITORY.git`

Advanced Git Reference
https://dev.to/g_abud/advanced-git-reference-1o9j

## Creating and workign with a git repo

### New Project Git Project from Repo

1. Create a new Repo on Github.  Find the repository URL on the overview page.
2. Go to the directory you want the repo to live.  
3. run `git clone <repositoryURL>` 
4. Add files.
5. run `git status` to veryify things are working
6. add files with `git add filename` or add all with `git add -A`
7. run `git status` again to verify files are being tracked.
8. commit with `git commit -m 'Comments go here'`
9. Push changes to github with `git push origin main`


### Pull Changes to local

1. Go into the directory of the project
2. run `git pull --all`


### Push changes

1. Verify there are changes with `git status`
2. Commit changes with `git commit -m 'Comments go here'`
3. Sync back to github with `git push origin main`


### New Project from existing directory

1. Go into the directory
2. Run `git init`
3. run `git status` to veryify things are working
4. add files with `git add filename` or add everything with `git add -A`
5. commit with `git commit -m 'Comments go here'`
6. Add remote repo with `git remote add origin <repositoryURL>`
7. Verify it with `git remote -v`
8. Sync back to github with `git push origin main`


### Branch a repo

1. `git branch <branch name>`
2. `git checkout <branch name>`
3. `git status`
4. `git add <filename>`
5. `git commit <filename> -m 'Notes'`


### Merge back to main

1. Verify your changes `git status`
2. Switch to main `git checkout main`
3. `git merge <branch name>`
4. Delete now unused branch `git branch -d <branch name>`
5. Verify with `git status` 
6. Sync back to github `git push origin main`


### Overwrite local changes with what is on the remote. 

1. `git fetch --all`
2. `git reset --hard origin/main`

Or if you are on some other branch

`git reset --hard origin/<branch_name>`

### Testing gitlab ssh
`ssh -T [git@gitlab.com](mailto:git@gitlab.com)`

### Change git remote to SSH
1. `git remote -v`
2. `git remote set-url origin <repo uri>`

### Setting multiple remotes
`git remote add <short name for repo> <repo uri>`
 
 Verify with 
 `git remote -v`

 Then use the short name in pushes or pulls
 `git push <short name for repo> main`

### Ignore changes to already-tracked files

`git update-index --assume-unchanged <file>`

And if you want to start tracking changes again, you can undo the previous command using:

`git update-index --no-assume-unchanged <file>`

---


## To set your identity:
`git config --global user.name "Jane Doe"`
`git config --global user.email myworkemail@address.com`

### To only set the idendity for one repo: 
`git config user.email myworkemail@address.com`

### To set your editor:
`git config --global core.editor vim`

### To enable color:
`git config --global color.ui true`


## Git Stash Notes

### To stage all changes for commit:
`git add --all`

### To stash changes locally, this will keep the changes in a separate changelist
called stash and the working directory is cleaned. You can apply changes from the stash anytime
`git stash`

### To stash changes with a message
`git stash save "message"`

### To list all the stashed changes
`git stash list`

### To apply the most recent change and remove the stash from the stash list
`git stash pop`

### To apply any stash from the list of stashes. This does not remove the stash from the stash list
`git stash apply stash@{6}`

### To commit staged changes
`git commit -m "Your commit message"`

### To edit previous commit message
`git commit --amend`

### Git commit in the past
```
git commit --date="`date --date='2 day ago'`"
git commit --date="Jun 13 18:30:25 IST 2015"
```
more recent versions of Git also support --date="2 days ago" directly

### To change the date of an existing commit
```
git filter-branch --env-filter \
'if [ $GIT_COMMIT = 119f9ecf58069b265ab22f1f97d2b648faf932e0 ]
 then
 export GIT_AUTHOR_DATE="Fri Jan 2 21:38:53 2009 -0800"
 export GIT_COMMITTER_DATE="Sat May 19 01:01:01 2007 -0700"
 fi'
```

### To removed staged and working directory changes
`git reset --hard`

### To go 2 commits back
`git reset --hard HEAD~2`

### To remove untracked files
`git clean -f -d`

### To remove untracked and ignored files
`git clean -f -d -x`

### To push to the tracked main branch:
`git push origin main`

### To push to a specified repository:
`git push git@github.com:username/project.git`

### To delete the branch "branch_name"
`git branch -D branch_name`

### To make an exisiting branch track a remote branch
`git branch -u upstream/foo`

### To see who commited which line in a file
`git blame filename`

### To sync a fork with the main repo:
`git remote add upstream git@github.com:name/repo.git`    # Set a new repo
`git remote -v`                                           # Confirm new remote repo
`git fetch upstream`                                      # Get branches
`git branch -va`                                          # List local - remote branches
`git checkout main`                                     # Checkout local main branch
`git checkout -b new_branch`                              # Create and checkout a new branch
`git merge upstream/main`                               # Merge remote into local repo
`git show 83fb499`                                        # Show what a commit did.
`git show 83fb499:path/fo/file.ext`                       # Shows the file as it appeared at 83fb499.
`git diff branch_1 branch_2`                              # Check difference between branches
`git log`                                                 # Show all the commits
`git status`                                              # Show the changes from last commit

### Commit history of a set of files
`git log --pretty=email --patch-with-stat --reverse --full-index -- Admin\*.py > Sripts.patch`

### Import commits from another repo
`git --git-dir=../some_other_repo/.git format-patch -k -1 --stdout <commit SHA> | git am -3 -k`

### View commits that will be pushed
`git log @{u}..`

### View changes that are new on a feature branch
`git log -p feature --not main`
`git diff main...feature`

### Interactive rebase for the last 7 commits
`git rebase -i @~7`

### Diff files WITHOUT considering them a part of git This can be used to diff files that are not in a git repo!
`git diff --no-index path/to/file/A path/to/file/B`

### To pull changes while overwriting any local commits
`git fetch --all`
`git reset --hard origin/main`

### Update all your submodules
`git submodule update --init --recursive`

### Perform a shallow clone to only get latest commits (helps save data when cloning large repos)
`git clone --depth 1 <remote-url>`

### To unshallow a clone
`git pull --unshallow`

### Create a bare branch (one that has no commits on it)
`git checkout --orphan branch_name`

### Checkout a new branch from a different starting point
`git checkout -b main upstream/main`

### Remove all stale branches (ones that have been deleted on remote) So if you have a lot of useless branches, delete them on Github and then run this
`git remote prune origin`

### The following can be used to prune all remotes at once
`git remote prune $(git remote | tr '\n' ' ')`

### Revisions can also be identified with :/text So, this will show the first commit that has "cool" in their message body
`git show :/cool`

### Undo parts of last commit in a specific file
`git checkout -p HEAD^ -- /path/to/file`

### Revert a commit and keep the history of the reverted change as a separate revert commit
`git revert <commit SHA>`

### Pich a commit from a branch to current branch. This is different than merge as this just applies a single commit from a branch to current branch
`git cherry-pick <commit SHA1>`

### Undo last commit
 If you want to nuke commit C and never see it again
    (F)
 A-B-C
     ↑
   main
`git reset --hard HEAD~1`

### Undo last commit
 If you want to undo the commit but keep your changes
    (F)
 A-B-C
   ↑
 main
`git reset HEAD~1`

### list files changed in ${commit_id}
`git diff-tree --no-commit-id --name-only -r ${commit_id}`

### list files changed in ${commit_id}, porcelain way, meant to be user facing
`git show --pretty="" --name-only bd61ad98`

## Keep all files as is and start git over fresh 
This is useful if secrets have been commited and the history needs to be wiped out to protect them. 

`git checkout --orphan newBranch`
`git add -A`  # Add all files and commit them
`git commit`
`git branch -D main`  # Deletes the main branch
`git branch -m main`  # Rename the current branch to main
`git push -f origin main`  # Force push main branch to github
`git gc --aggressive --prune=all`     # remove the old files

