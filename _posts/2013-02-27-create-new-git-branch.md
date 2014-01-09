Title: Create new local and remote branch in a git repo  
Date: Wed Feb 27 16:24:49 2013  
Tags: git

Via: [Zorched/One-Line Fix](http://www.zorched.net/2008/04/14/start-a-new-branch-on-your-remote-git-repository/)

# Creating remote branch and local copy of it

* Create the remote branch

    `git push origin origin:refs/heads/new_branch_name`

* Make sure everything is up-to-date

    `git fetch origin`

* See the branch created

    `git branch -r`

* Start tracking the new branch

    `git checkout --track -b new_branch_name origin/new_branch_name`

  This will make `git pull` update the local branch.

* Make sure everything is up-to-date

    `git pull`

## Deleting a remote branch

    `git push origin :heads/new_branch_name`

# Easier way

A commenter (Olivier Clavel) suggested an easier method made possible since git 1.7.0:

* Create the local branch first, from a current one

    `git checkout -b new_branch_name`

* Push that branch to the remote

    `git push origin new_branch_name`

  Now, local branch can push to remote.

* Set the upstream branch to track

    `git branch --set-upstream new_branch_name origin/new_branch_name`

  Now, local branch can pull from remote branch too.

* Deleting remote branch

    `git push origin :new_branch_name`
